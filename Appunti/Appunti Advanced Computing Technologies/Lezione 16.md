# Processori Paralleli: Architetture MIMD

## 1. Introduzione alle macchine MIMD (Multiple Instruction Multiple Data)

- Nelle architetture MIMD, **ogni processore preleva le proprie istruzioni e opera sui propri dati** in modo indipendente.
- Queste macchine sono spesso realizzate utilizzando microprocessori commerciali standard (off-the-shelf), il che offre enormi vantaggi in termini di rapporto costo/prestazioni.
- Sono altamente scalabili, permettendo un numero variabile di nodi di elaborazione.
- La loro flessibilità permette di utilizzarle come macchine mono-utente ad alte prestazioni per una singola applicazione, come multiprocessori multi-programmati (eseguendo contemporaneamente molti task) o una combinazione di queste funzioni.
- Tra le problematiche principali che queste macchine affrontano vi sono i problemi di tolleranza ai guasti (fault tolerance issues).

**Esempi di macchine MIMD:**

- **Symmetric Multiprocessor (SMP):** Processori multipli in un unico box che comunicano tramite memoria condivisa attraverso un Bus. Gli attuali chip MultiCore sono progettati in questo modo e ogni processore esegue una copia del Sistema Operativo.
- **Non-uniform shared-memory con I/O separato:** Più processori connessi tramite una rete scalabile generale, ognuno con una propria memoria locale. Sfruttano un sistema operativo estremamente "leggero" sul nodo per gestire operazioni semplici come schedulazione e sincronizzazione. L'I/O viene gestito separatamente tramite un host accessibile via rete.
- **Cluster:** Numerose macchine indipendenti connesse mediante una rete generale. La comunicazione avviene tramite scambio di messaggi.

## 2. Il concetto di Parallelismo nei MIMD

- Per sfruttare appieno un sistema MIMD con $n$ processori, è necessario eseguire almeno $n$ thread o processi indipendenti.
- Un thread può andare da un grande processo indipendente fino a singole iterazioni parallele di un ciclo.
- Il parallelismo sfruttato in questi sistemi si definisce **Thread-Level Parallelism**.
- **Concetto fondamentale:** A differenza di quanto avviene nelle CPU superscalari (dove se ne occupa l'hardware), nei sistemi MIMD **il parallelismo viene identificato dal software** (dal programmatore o dal compilatore).

## 3. Spazio di Indirizzamento della Memoria e Organizzazione Fisica

Le macchine parallele necessitano di condividere dati. Esistono due concetti _ortogonali_ da non confondere: il modello logico degli indirizzi e l'organizzazione fisica della memoria.

**Modelli Logici di Spazio di Indirizzamento:**

1. **Singolo Spazio di Indirizzamento Logicamente Condiviso (Shared-Memory Architectures):** Qualsiasi processore può fare un riferimento in memoria a qualsiasi locazione. Lo spazio degli indirizzi è condiviso: lo stesso indirizzo fisico su 2 processori punta all'esatta stessa locazione in memoria.
    - La comunicazione tra processori avviene _implicitamente_ attraverso operazioni di `load` e `store` per accedere alle variabili condivise in memoria.
    - È il modello più antico e diffuso. Attenzione: memoria condivisa _non significa_ necessariamente avere una sola memoria centralizzata.
2. **Spazi di Indirizzamento Multipli e Privati (Message Passing Architectures):** Lo spazio degli indirizzi è logicamente disgiunto. Lo stesso indirizzo fisico su 2 processori fa riferimento a 2 locazioni distinte in 2 memorie diverse.
    - La comunicazione avviene in modo _esplicito_ tramite primitive di invio e ricezione (`send/receive`).
    - In questo modello **non esiste il problema della cache coherency** tra processori.

**Organizzazione Fisica della Memoria:**

1. **Centralizzata (UMA - Uniform Memory Access):** Al massimo poche decine di processori (<100 core). Grandi cache, una memoria singola divisa in più banchi. Chiamati spesso SMP.
2. **Distribuita (NUMA - Non Uniform Memory Access):** Pensata per supportare un alto numero di processori. Richiede un'interconnessione ad altissima larghezza di banda. Il lato negativo (cons) è il costo della comunicazione dati tra i processori. _Poiché i concetti sono indipendenti, esistono sistemi multiprocessore con un singolo spazio logico di indirizzamento ma con una memoria fisica distribuita in moduli fisicamente separati (come nei sistemi SMP su larga scala)._

## 4. Confronto tra Modelli di Comunicazione

Sia Shared Memory (SM) che Message Passing (MP) sono "Turing completi per la comunicazione", ovvero uno può essere costruito sull'altro e viceversa. La scelta del migliore dipende dal programma.

**Shared Memory (Memoria Condivisa):**

- _Modello di programmazione:_ Il programma è una collezione di thread di controllo (creabili dinamicamente) che possiedono variabili private (es. stack locali) e variabili condivise (es. variabili statiche o global heap). La coordinazione avviene sincronizzandosi sulle variabili condivise.
- _Vantaggi:_ Comunicazione implicita tramite load/store, basso overhead quando i dati sono in cache, facilità di programmazione.
- _Svantaggi:_ Complesso da scalare fisicamente ad alte dimensioni, richiede operazioni di sincronizzazione forzate, difficile controllare dove i dati vengano allocati dentro i sistemi di caching.

**Message Passing (Scambio di Messaggi - MPPs):**

- _Modello di programmazione:_ Collezione di processi nominati definiti all'avvio. C'è spazio locale, **nessun dato condiviso**. Il software (solitamente usando MPI - Message Passing Interface) deve gestire esplicitamente la comunicazione inviando e ricevendo, ed ogni comunicazione è di per sé un evento implicito di coordinazione.
- _Vantaggi:_ Comunicazione esplicita, meno hardware da progettare, controllo semplificato del posizionamento dei dati (nessun caching automatico misterioso), focalizza l'attenzione sulle costose operazioni non locali.
- _Svantaggi:_ Overhead software molto alto (nei primi MPP invocare l'OS per un messaggio richiedeva da 100μs a 1ms). Mandare messaggi costa poco (come una store), ma riceverli è costoso (richiede polling o interrupt). Difficile programmare.

## 5. Il Problema della Coerenza della Cache (Cache Coherence)

Nei sistemi Symmetric Shared Memory basati su bus, le cache mantengono sia dati privati che dati condivisi (per comunicare). La replica dei dati condivisi in cache multiple riduce la latenza, il fabbisogno di bandwidth in memoria e i conflitti di accesso. Tuttavia, queste cache private creano un problema fondamentale: **la presenza di più copie della stessa variabile in più cache**.

- Se un processore scrive un dato aggiornandolo nella sua cache locale, questa modifica potrebbe non diventare visibile istantaneamente agli altri processori, creando **incoerenza (Cache Coherence Problem)**. Un processore potrebbe continuare a leggere un valore obsoleto (stale value).
- Evitare l'uso delle cache per i dati condivisi richiederebbe un accesso diretto alla memoria principale che renderebbe il sistema lentissimo e saturerebbe il bus.

**Cosa significa Coerenza?**

- Un assunto base del tipo "qualsiasi lettura deve restituire la scrittura istantaneamente" è troppo restrittivo e impossibile da implementare in hardware.
- La regola migliore e sufficiente si basa su due fondamenti:
    1. Una scrittura di un processore deve poter essere letta da un altro se vi è abbastanza distanza temporale ed in assenza di altre scritture nel mezzo.
    2. **Serializzazione (Serialization):** Le scritture su una stessa locazione di memoria devono essere viste in ordine coerente da tutti i processori (es. non si può mai leggere un dato vecchio "dopo" aver letto una sua versione più nuova).

_Nota:_ Non bisogna confondere la **coerenza** con la **consistenza**. La coerenza definisce il comportamento delle operazioni rispetto alla _stessa locazione_ di memoria, mentre la consistenza definisce le regole temporali per l'accesso a memory location _diverse_.

## 6. Soluzioni: Protocolli Cache-Coherence

La soluzione hardware è implementare un protocollo che tenga traccia dello "stato di condivisione" dei blocchi di dati. Le due classi principali sono i **Directory-Based Protocols** e gli **Snooping Protocols**. Le slide si concentrano sui secondi.

### Protocolli Snooping (Snoopy Bus)

- Tutti i controller della cache "snoopano" (spiano/monitorano) il bus per determinare se possiedono una copia del blocco richiesto in una transazione e agiscono di conseguenza.
- Non c'è uno stato centralizzato: l'informazione è custodita in ciascuna cache. Il funzionamento richiede un sistema di broadcast sul bus ed è perfetto per sistemi centralizzati con singolo bus condiviso.
- Introdotto da _Goodman (1983)_, per evitare che i continui check dei tag sul bus rallentino o causino "stalli" alla CPU, i tag indirizzi della cache vengono duplicati (o vi si aggiunge una porta di lettura dedicata al bus).

**Dettaglio tecnico sul Dual-Porting:** Per evitare che il monitoraggio del bus interferisca con le operazioni del processore, non si duplica l'intera cache ma solo la porzione dei **tag degli indirizzi**. In pratica, si aggiunge una **porta di lettura extra** dedicata alle attività di snooping. Senza questo accorgimento, ogni transazione sul bus costringerebbe il processore a fermarsi (stallo) per permettere al controller della cache di controllare i tag.

Esistono due tipologie per la gestione in scrittura:

1. **Write-Invalidate Protocol:** Chi scrive emette sul bus un segnale di invalidazione che "distrugge" tutte le copie di quel dato presenti nelle altre cache prima di scrivere. Le successive scritture del processore sono libere, poiché è l'unico proprietario (Singolo scrittore, molti lettori). In presenza di cache Write-Back, la memoria non viene subito aggiornata e questo riduce pesantemente il traffico sul bus.
2. **Write-Update (o Write-Broadcast):** Ogni volta che il processore scrive un nuovo dato, questo viene fatto viaggiare in broadcast sul bus andando ad aggiornare i valori corrispondenti contenuti nelle altre cache. Richiede continue trasmissioni (costante utilizzo del bus) ma riduce la latenza per l'accesso ai nuovi valori da parte delle CPU in lettura. Si comporta come le cache write-through.

**La maggior parte dei multiprocessori commerciali opta per:** Cache Write-Back (per ridurre traffico) e protocollo Write-Invalidate (per preservare la bandwidth). Nel Write-back cache con Snooping, se un processore tenta di leggere un dato ma un altro processore ne ha una copia locale aggiornata (dirty copy), quest'ultimo interviene sul bus, passa il dato al richiedente e fa abortire l'accesso alla memoria centrale lenta.

**Legenda per la lettura dei diagrammi di stato:** 
* **RMS (Read Miss Shared):** Si verifica un miss in lettura e il blocco viene caricato come "condiviso". 
* **RME (Read Miss Exclusive):** Miss in lettura dove il sistema rileva che nessun'altra cache ha il dato, caricandolo come "esclusivo". 
* **WM (Write Miss):** Anche detto **Read-with-Intent-to-Modify (RWITM)**: il processore vuole scrivere ma non ha il dato, quindi lo richiede con l'intenzione di modificarlo subito. 
* **SHR (Snoop Hit on a Read):** La cache intercetta sul bus una richiesta di lettura di un altro processore per un dato che lei possiede. 
* **SHW (Snoop Hit on a Write):** La cache intercetta una scrittura altrui su un proprio dato (che dovrà quindi essere invalidato o aggiornato).

## 7. Il Protocollo d'Esempio MSI

È un protocollo base Write-Invalidate. Ogni blocco di cache può essere in 3 stati:

- **M (Modified/Dirty):** La cache ha l'unica copia valida in tutto il sistema. È modificata e scrivibile.
- **S (Shared/Clean):** Il blocco è pulito, può essere letto. Esistono copie in altre cache o in memoria.
- **I (Invalid):** Il blocco non contiene dati validi.

Se un processore ha un dato in "S" e vuole scriverlo, deve ottenere la proprietà esclusiva emettendo un segnale di invalidazione `BusRdX` per smontare gli altri in "I" (demote), portando il suo in "M" (upgrade). Essendo operazioni non del tutto atomiche, possono esserci rischi di race condition e deadlock risolvibili forzando il mantenimento del bus finché le invalidazioni non vengono recepite.

## 8. Varianti Evolute: Il Protocollo MESI

Il MESI estende l'MSI introducendo lo stato **E (Exclusive)** per distinguere blocchi esclusivi puliti e blocchi esclusivi "sporchi" (scritti). I 4 stati di una linea di cache con MESI:

- **Modified (M):** Linea valida e sporca. Nessuna copia altrove.
- **Exclusive (E):** Linea valida e pulita (allineata con la memoria). **Nessuna copia altrove**.
- **Shared (S):** Linea valida e pulita. Possibili copie in altre cache.
- **Invalid (I):** Linea invalida.

**Il grande vantaggio del MESI:**

- Se un processore esegue una scrittura su un blocco che si trova in stato **E (Exclusive)**, non ha bisogno di inviare alcun segnale di invalidazione sul bus (risparmiando tempo e cicli macchina), perché sa per certo che è l'unico a possedere quel blocco. Lo stato cambia in **M** silenziosamente.
- Al contrario, una scrittura su un blocco **S (Shared)** impone di lanciare l'invalidazione per distruggere le eventuali copie altrui (cambiando in M).

**Supporto Hardware MESI:** Richiede un segnale fisico "Shared" sul bus cablato come wired-OR. Quando una CPU chiede un blocco (BusRd), gli altri controller monitorano: se qualcuno lo possiede, solleva il segnale "shared". Il processore richiedente valuta il segnale: se nessuno ha quel blocco, la linea finisce in stato **E**, altrimenti andrà in stato **S**. (Le slide riportano anche i complessi diagrammi di transizione degli stati e tabelle esemplificative step-by-step dei cambi di stato della cache tra P1 e P2 nel tempo).

### 9. Logica degli Esercizi: Come varia lo stato della Memoria

Negli esercizi passo-passo delle slide, il punto critico è capire quando la memoria è aggiornata (**Up-to-date**):

- **Stati Shared (S) ed Exclusive (E):** In entrambi questi stati, il contenuto della memoria principale è **aggiornato** e identico a quello della cache.
    
- **Stato Modified (M):** La memoria **NON è aggiornata**. Il processore ha modificato il dato localmente e non lo ha ancora scritto nel database centrale.
    
- **Transizione chiave:** Se un processore ha un dato in stato **Modified** e un altro processore lo richiede in lettura, il primo deve fornire il dato e la memoria deve essere aggiornata nel processo (passando allo stato **Shared**).