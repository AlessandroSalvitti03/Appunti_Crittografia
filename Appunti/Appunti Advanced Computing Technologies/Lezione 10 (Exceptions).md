### 1. Introduzione: Alterare il normale flusso di controllo

Il normale flusso di esecuzione sequenziale di un programma può essere interrotto da eventi inaspettati che richiedono l'attenzione immediata del processore. Sebbene molti calcolatori e architetture (come l'x86) utilizzino il termine "interrupt" per descrivere tutti questi eventi, l'architettura MIPS fa una distinzione importante in base alla loro origine:

- **Interrupt (Eventi Asincroni / Esterni):** Sono causati da dispositivi esterni al processore e alla memoria. Esempi classici sono le richieste di servizio da parte di un dispositivo di I/O, la scadenza di un timer, un malfunzionamento hardware o un'interruzione di corrente. Dato che sono asincroni, possono essere gestiti al termine dell'istruzione corrente, rendendoli più facili da trattare.

- **Eccezioni (Eventi Sincroni / Interni):** Sono causate direttamente dall'esecuzione di una particolare istruzione all'interno del programma (avvengono _within instructions_). Esempi includono un codice operativo non definito (undefined opcode), un overflow aritmetico, un accesso disallineato alla memoria, o problemi legati alla memoria virtuale (page fault, miss del TLB). Rientrano in questa categoria anche le _System Call_ (o trap), che sono eccezioni "richieste dall'utente" per invocare il Sistema Operativo.

**Approfondimento / Classificazione:** Gli eventi si possono classificare anche in base a come il sistema riprende l'esecuzione.

Oltre alla divisione tra sincroni e asincroni, le eccezioni possono essere classificate in base a:

- **User requested vs Coerced:** Le eccezioni richieste dall'utente (es. _system call_) sono prevedibili. Quelle "coerced" sono imposte dall'hardware in situazioni anomale.

- **User maskable vs Nonmaskable:** Una maschera hardware può controllare se il processore deve rispondere a un determinato interrupt o ignorarlo.

- **Within vs Between instructions:** Le eccezioni sincrone avvengono _all'interno_ (within) dell'istruzione, obbligando a fermarla e riavviarla; gli eventi asincroni che avvengono _tra_ (between) un'istruzione e l'altra sono spesso situazioni catastrofiche che causano l'interruzione del programma.

- **Resume vs Terminate:** L'evento può permettere la ripresa dell'esecuzione del programma (resume) oppure obbligare il sistema a terminarlo (terminate).

### 2. Il Meccanismo di Gestione (Hardware & Sistema Operativo)

Quando si verifica un'eccezione, hardware e Sistema Operativo (SO) devono collaborare strettamente. L'hardware è responsabile di:

1. **Fermare** il programma corrente all'istruzione Ii​, completando tutte le istruzioni precedenti.
2. **Salvare l'indirizzo** dell'istruzione interrotta in un registro speciale chiamato **EPC (Exception Program Counter)**.
3. **Registrare la causa** dell'eccezione. Nel MIPS si usa il **Registro Causa** (Cause Register), che contiene un codice specifico (es. 10 per istruzione indefinita, 12 per overflow aritmetico).
    - _Nota architetturale:_ Un approccio alternativo al Registro Causa sono gli _Interrupt Vettorizzati_, dove l'indirizzo a cui salta il SO dipende direttamente dal tipo di eccezione.
4. **Disabilitare ulteriori interrupt** e trasferire il controllo (passando in modalità supervisore/kernel) a un indirizzo predesignato (nel MIPS è tipicamente `0x8000 0180`) dove risiede l'**Interrupt Handler** (la procedura software del SO che gestisce l'evento).

Il Sistema Operativo prenderà poi le decisioni necessarie (es. terminare il processo o risolvere il page fault) e, per ripristinare l'esecuzione, utilizzerà un'istruzione speciale (nel MIPS `ERET`, Exception Return) per tornare alla modalità utente e ricaricare il PC dal registro EPC.

* Prima di abilitare nuovamente gli interrupt per permettere l'annidamento (nested interrupts), l'handler deve spostare il valore del PC dai registri speciali (EPC) ai registri di uso generale (GPR). 
* L'uscita dall'handler avviene tramite l'istruzione speciale **RFE** (Return-From-Exception) o **ERET**, che ripristina la modalità utente e lo stato dei controlli hardware in un colpo solo.

### 3. Eccezioni Sincrone 

Le eccezioni sincrone sono eventi causati direttamente dall'esecuzione di una specifica istruzione. Ecco le loro caratteristiche principali:

- **Origine**: Sono generate da un'istruzione che non può essere completata e richiede l'intervento del sistema operativo.
    
- **Esempi comuni**: Includono chiamate di sistema (_system calls_), opcode illegali, istruzioni privilegiate, errori di allineamento dell'indirizzo o _page fault_.
    
- **Rilevamento nella Pipeline**: Solitamente vengono rilevate nelle fasi avanzate della pipeline, come EX (Esecuzione) o M (Memoria).
    
- **Priorità**: Se una singola istruzione causa più eccezioni in diverse fasi della pipeline, l'eccezione rilevata nella fase più antica (quella cronologicamente precedente nell'ordine dei cicli) ha la precedenza e annulla le altre.

### 4. Interrupt Precisi 

Un interrupt (o eccezione) è definito "preciso" quando lo stato della macchina è preservato in modo coerente nel momento in cui l'esecuzione viene interrotta. Perché un interrupt sia considerato preciso, devono essere soddisfatte queste condizioni:

- **Stato del Program Counter**: Il PC deve puntare esattamente all'istruzione che ha causato l'eccezione (istruzione "faulting") o alla successiva istruzione pronta per essere eseguita.
    
- **Istruzioni Precedenti**: Tutte le istruzioni che precedono quella che ha causato l'errore (nell'ordine logico del programma) devono aver terminato la loro esecuzione e aver aggiornato lo stato del processore (_committed_).
    
- **Istruzioni Successive**: Nessuna istruzione successiva a quella incriminata deve aver modificato lo stato dell'architettura (registri o memoria).
    
- **Importanza**: Questa proprietà è fondamentale per le eccezioni "riavviabili" (come i _page fault_), dove il sistema operativo deve poter riprendere l'esecuzione esattamente dal punto dell'interruzione dopo aver risolto il problema.

### 5. Interrupt Precisi vs Imprecisi

Affinché un programma possa essere riavviato in modo sicuro dopo un'eccezione, il processore deve implementare **Interrupt Precisi**. Un interrupt si dice _preciso_ se esiste un singolo punto di interruzione tale per cui:

- Tutte le istruzioni precedenti a quel punto hanno completato e committato il loro stato.
- Nessuna istruzione successiva (inclusa quella interrotta) ha modificato lo stato dell'architettura in modo permanente.

**Esempio matematico (Perché sono desiderabili?):** Supponiamo di calcolare la funzione f(x)=xsin(x)​. Cosa succede se x→0? Il calcolo f(0)=00​ genererà un "Not a Number" (NaN) e un'operazione illegale. Se l'interrupt fosse _impreciso_ (come avveniva in alcuni vecchi calcolatori o in pipeline molto sbilanciate), il PC salvato potrebbe puntare a istruzioni molto successive, rendendo difficilissimo per il programmatore o per il SO capire esattamente _quale_ istruzione ha causato l'errore matematico. Le eccezioni precise permettono invece di isolare perfettamente l'errore o di "sistemare" la situazione (es. un TLB fault) per poi ri-eseguire l'istruzione in modo del tutto trasparente per l'utente.

### 6. Gestione delle Eccezioni nella Pipeline (Il "Flush")

Nelle architetture con pipeline, le eccezioni sono trattate in modo simile agli hazard sul controllo (i salti). Più istruzioni si trovano in fasi diverse nello stesso momento: un'eccezione per istruzione non definita avviene nello stadio ID, mentre un overflow aritmetico avviene nello stadio EX.

Se si verifica un overflow nello stadio EX:

1. L'hardware forza nel Program Counter l'indirizzo dell'handler del SO (`0x8000 0180`).
2. L'hardware esegue un **Flush** (svuotamento) delle istruzioni che seguono l'istruzione problematica. Vengono attivati segnali di controllo speciali (es. `IF.Scarta`, `ID.Scarta`, `EX.Scarta`) che trasformano le istruzioni prelevate erroneamente in bolle (NOP), azzerando i loro segnali di controllo per impedire che sporchino i registri o la memoria negli stadi MEM e WB.
3. L'istruzione colpevole non viene completata (si impedisce il write-back), preservando lo stato precedente all'errore.

_Approfondimento sulle eccezioni multiple:_ Se più istruzioni nella pipeline generano eccezioni nello stesso ciclo di clock, l'hardware assegna la priorità in base all'ordine originale del programma (l'istruzione che si trova nello stadio più avanzato della pipeline ha la precedenza).

Per gestire eccezioni fuori ordine, l'hardware applica il **tagging**: ogni istruzione viaggia nella pipeline con un flag che indica se ha generato un'eccezione. L'eccezione viene effettivamente 'lanciata' solo quando l'istruzione raggiunge il **commit point** (stadio M). Se un'istruzione precedente ha già generato un'eccezione, quelle successive vengono trasformate in NOP marcati (bolle).
### 7. Speculazione Hardware ed Eccezioni (Capitoli Avanzati / Appendice)

Nelle architetture avanzate (Superscalari, Tomasulo, esecuzione fuori ordine), il processore adotta il meccanismo della **Speculazione**: cerca di prevedere i salti ed esegue le istruzioni prematuramente per mantenere alto il throughput.

In questo contesto, la gestione delle eccezioni diventa ancora più complessa. L'hardware adotta una filosofia chiara:

- **Predizione:** Le eccezioni sono eventi rari. Il processore "predice" sempre che non ci saranno eccezioni, massimizzando le prestazioni.
- **Controllo e Recupero (Recovery):** Se un'istruzione eseguita speculativamente genera un'eccezione (es. un page fault causato da una load eseguita in anticipo su un salto che poi si rivela non preso), l'hardware _non solleva immediatamente l'eccezione_. L'informazione sull'errore viene parcheggiata insieme ai risultati temporanei nel **Buffer di Riordino (Reorder Buffer - ROB)**. Lo stato architetturale viene aggiornato (Commit Point) rigorosamente _in ordine_.
- Se l'istruzione colpevole arriva in testa al ROB ed è confermato che doveva essere realmente eseguita, la pipeline viene "flusshata", l'eccezione viene sollevata e si lancia l'handler.
- Se l'istruzione faceva parte di un ramo speculativo errato, viene semplicemente scartata ("flushed") insieme alla sua eccezione "fantasma", garantendo un'esecuzione sempre precisa e pulita.

Per srotolare i loop dinamicamente, Tomasulo si affida a: 

* **Reservation Stations (RS):** Buffer che memorizzano le istruzioni in attesa di operandi, eliminando i colli di bottiglia dei registri fisici. * **Load/Store Buffers:** Gestiscono la memoria e risolvono le ambiguità degli indirizzi.

- **Common Data Bus (CDB):** Il bus su cui viaggiano i risultati per tutte le RS in ascolto. Nota: è un potenziale **collo di bottiglia**; più CDB aumentano le prestazioni ma complicano enormemente la logica delle Unità Funzionali.