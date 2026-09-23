### 1. Oltre il CPI = 1: Architetture VLIW

L'obiettivo iniziale della pipeline è avvicinarsi a un CPI (Clock Cycles Per Instruction) pari a 1. Per superare questo limite (e avere quindi un CPI < 1, o IPC > 1), le architetture devono emettere più operazioni per singolo ciclo di clock. A differenza delle architetture Superscalari (che affidano all'hardware il compito di trovare il parallelismo a runtime), le architetture **VLIW (Very Long Instruction Word)** o a **Schedulazione Statica** si affidano interamente al **compilatore**.

- **Il concetto:** Il compilatore raggruppa un numero fisso di operazioni indipendenti (es. da 4 a 16 operazioni come somme, load, store, moltiplicazioni) in un'unica "lunghissima" parola di istruzione (Issue Packet). È il software a garantire che non ci siano hazard sui dati (RAW) all'interno dell'istruzione e che i dati non vengano usati prima di essere pronti (assenza di interlock hardware).

- **Vantaggi:** L'hardware è molto più semplice rispetto ai processori superscalari. Non necessita di circuiti complessi per il riordinamento dinamico delle istruzioni, controlli RAW incrociati o hardware di schedulazione.

- **Svantaggi:** Richiede un enorme numero di registri per mantenere attive tutte le unità funzionali, un'elevatissima larghezza di banda verso le memorie e la cache, e soffre dell'espansione della dimensione del codice (code size explosion). Inoltre, fatica moltissimo in presenza di salti imprevedibili e non può adattarsi dinamicamente ai ritardi della memoria (come i cache miss).

- **ILP (Parallelismo a livello di istruzioni)**: È **trasparente all'utente** e mira a velocizzare l'esecuzione sovrapponendo singole operazioni (add, mul, load).
    
- **Parallel Processing**: È **non trasparente**, richiede che l'utente (o il programmatore) divida il programma in chunk separati per diversi processori.

![[Pasted image 20260507122748.png|278]]![[Pasted image 20260507122705.png|389]]

#### Organizzazione e Parallelismo
Il compito principale del compilatore è analizzare il codice sorgente per estrarre il massimo parallelismo possibile, sia a livello di singole istruzioni (**ILP**) che di cicli (**LLP**). Invece di inviare una sola istruzione alla volta, il compilatore raggruppa più operazioni indipendenti in una singola "istruzione lunghissima". Per farlo, deve conoscere perfettamente la struttura interna della CPU: deve sapere quante unità di calcolo ci sono (ALU, unità floating point, ecc.) e mappare ogni operazione sull'unità giusta, rispettando rigorosamente i tempi di esecuzione e le dipendenze tra i dati.

#### Sicurezza e Gestione dei Rischi
A differenza dei processori tradizionali, i chip VLIW spesso non hanno "interblocchi" hardware. Se un'operazione richiede tre cicli per essere completata, l'hardware non fermerà automaticamente l'istruzione successiva che ha bisogno di quel risultato. È responsabilità del compilatore garantire che non avvengano errori (data hazards). Se il compilatore si accorge che un dato non sarà pronto in tempo, deve inserire esplicitamente delle istruzioni "vuote", chiamate **NOP** (No-Operation), per forzare il processore ad aspettare.
### 2. Strategie di Scheduling e Istruzioni

Esistono due approcci principali per gestire il parallelismo a livello di istruzioni (**ILP**): quello dinamico, dove l'hardware stesso individua il parallelismo a runtime, e quello **statico**, che si affida interamente al software per identificare le operazioni eseguibili in simultanea. Mentre le soluzioni hardware intensive dominano i mercati desktop e server, lo scheduling statico punta a massimizzare l'uso delle unità funzionali (FU) e a mantenere le pipeline piene attraverso un'attenta riorganizzazione del codice effettuata dal compilatore prima dell'esecuzione.

#### Il limite dei Basic Block
Il compilatore opera riordinando il codice in modo che sia privo di dipendenze, condizione essenziale per i processori VLIW. Tuttavia, l'ottimizzazione si scontra con il limite dei **Basic Block** (sequenze di istruzioni dritte senza rami o salti). Poiché il parallelismo disponibile all'interno di un singolo blocco è solitamente molto ridotto e limitato dalle dipendenze dei dati, per ottenere incrementi significativi di prestazioni il compilatore deve imparare a sfruttare l'ILP attraverso più blocchi, superando le barriere poste dai salti condizionali (_branch_).

#### Pro e Contro dell'approccio VLIW
L'adozione dello scheduling statico porta con sé una serie di compromessi strutturali:

- **Vantaggi:** L'hardware è estremamente semplice, il che rende facile aggiungere nuove unità funzionali; inoltre, un buon compilatore può essere incredibilmente efficace nel rilevare il parallelismo.
    
- **Svantaggi:** È necessario un numero enorme di registri per alimentare costantemente le unità di calcolo, la dimensione del codice tende ad aumentare e la gestione dei salti diventa complessa, richiedendo spesso una fase di _profiling_ per prevedere le probabilità dei rami.
    

#### Metodi di Scheduling
Per superare queste sfide, il documento elenca diversi metodi che il compilatore può utilizzare, come il semplice spostamento del codice (_code motion_), l'unrolling dei cicli (per eseguire più iterazioni contemporaneamente) e il _software pipelining_. Vengono menzionate anche tecniche di scheduling globale più avanzate, come il **Trace Scheduling**, il _Superblock_ e lo scheduling speculativo, che cercano di ottimizzare i percorsi di esecuzione più probabili all'interno del programma.

### 3. Ottimizzazione dei Cicli: Espansione dei Cicli (Loop Unrolling)

I compilatori utilizzano algoritmi sofisticati per estrarre l'ILP (Instruction-Level Parallelism), ma all'interno di un blocco base (Basic Block, una sequenza di istruzioni senza salti interni) il parallelismo disponibile è minimo.

Una tecnica classica per esporre maggiore parallelismo è l'**Espansione dei Cicli (Loop Unrolling)**. Questa tecnica replica il corpo del ciclo più volte. Ad esempio, "srotolando" un ciclo di 4 volte, il processore elabora 4 elementi del vettore per ogni singola iterazione del nuovo ciclo.

- **Ridenominazione dei registri (Register Renaming):** Quando il compilatore srotola il ciclo, deve utilizzare registri temporanei differenti per ciascuna copia del corpo originale. Questo processo, chiamato _ridenominazione dei registri_, è fondamentale per eliminare le dipendenze nominali (false dipendenze WAW e WAR) che altrimenti bloccherebbero l'esecuzione parallela.

- **Limiti:** L'unrolling comporta costi di startup e "wind-down" (chiusura) per **ogni** iterazione del ciclo srotolato. Oltre a riempire la memoria istruzioni (aumento del codice), si arriva rapidamente a un punto in cui si esauriscono i registri disponibili.

"VLIW" è spesso usato come sinonimo di **Static Multiple Issue**. In questo schema, il compilatore deve garantire che non ci siano hazard all'interno di un pacchetto di istruzioni (Issue Packet), o tra pacchetti emessi consecutivamente.

### 4. Ottimizzazione dei Cicli: Software Pipelining

Per superare i limiti dell'Unrolling, i compilatori utilizzano il **Software Pipelining**. Questa tecnica riorganizza il ciclo in modo che ogni singola iterazione nel codice software-pipelined contenga istruzioni pescate da **differenti iterazioni** del ciclo originale.

**Approfondimento / Esempio pratico:** Immaginiamo un ciclo originale con tre operazioni dipendenti: Load (L), Calcolo (C), Store (S). Nel Software Pipelining, il corpo del nuovo ciclo conterrà, nello stesso momento:

- La **Store** per il dato dell'iterazione `i`
- Il **Calcolo** per il dato dell'iterazione `i+1`
- La **Load** per il dato dell'iterazione `i+2`

Questa tecnica divide concettualmente l'esecuzione in tre fasi:

1. **Prolog (Prologo):** Riempie gradualmente la "pipeline software" avviando le prime Load e Calcoli.
2. **Iterate (Corpo centrale a regime):** Tutte le unità funzionali lavorano a pieno regime senza conflitti. Il grande vantaggio rispetto all'Unrolling è che i costi di startup e wind-down si pagano **una sola volta per l'intero ciclo**, e non per ogni singola "fettina" srotolata. Se in un loop srotolato 4 volte si otteneva 0.36 FLOPS/ciclo, con il Software Pipelining si può raggiungere 1 FLOP/ciclo ideale.
3. **Epilog (Epilogo):** Svuota la pipeline scrivendo gli ultimi risultati.

Questa tecnica è definita anche come **"Symbolic Loop Unrolling"**. Il vantaggio chiave, oltre all'efficienza dei FLOPS, è che richiede **meno codice** rispetto all'unrolling fisico massiccio, poiché il corpo del ciclo (Kernel) rimane compatto.

![[Pasted image 20260507123711.png]]

### 5. Supporto Hardware ai Cicli: Rotating Register Files

Il Software Pipelining e il Loop Unrolling consumano moltissimi registri e causano una massiccia duplicazione del codice nel prologo e nell'epilogo. Per risolvere questi colli di bottiglia, architetture avanzate (come IA-64 / Itanium) utilizzano i **Rotating Register Files (File di registri rotanti)**.

L'hardware alloca un nuovo set di registri fisici per ogni iterazione del ciclo, in modo trasparente. Viene utilizzato un **RRB (Rotating Register Base)** che si somma al numero logico del registro per ottenere l'indirizzo fisico.

- A ogni iterazione, l'RRB viene incrementato/decrementato.
- _Esempio:_ Se un'istruzione carica un dato nel registro `P9` e la latenza della Load è di 3 cicli, l'istruzione dipendente che ne ha bisogno 3 iterazioni (cicli) dopo lo troverà "magicamente" scalato in un nuovo registro calcolato via RRB, senza che il compilatore debba riassegnare manualmente decine di registri ed espandere esplicitamente il codice del prologo/epilogo.

**Eliminazione di Prologo ed Epilogo**: In architetture avanzate come l'IA-64, i registri rotanti (insieme a predicati speciali) permettono di eseguire il software pipelining **senza scrivere esplicitamente il prologo e l'epilogo** nel codice, riducendo drasticamente la dimensione del binario.

### 6. Oltre i Cicli: Trace Scheduling

Non tutto il codice è costituito da cicli regolari (es. programmi con costrutti `if-else` irregolari). In presenza di flussi di controllo intensi, i Basic Block sono troppo corti per estrarre ILP.

La tecnica del **Trace Scheduling** permette di parallelizzare istruzioni estese attraverso più blocchi base:

1. **Profilazione (Profiling):** Il compilatore esegue prima il programma (o usa euristiche) per individuare il percorso ramificato più probabile e frequente, chiamato **Trace (Traccia)**.

2. **Schedulazione:** La traccia intera viene schedulata globalmente ignorando le diramazioni, come se fosse un unico gigantesco blocco base in cui si ottimizza pesantemente il codice.

3. **Compensation Code (Codice di compensazione):** Poiché la traccia salta su percorsi che potrebbero non essere sempre presi, il compilatore deve inserire del codice correttivo (Fixup code) nei punti di ingresso laterali e uscita laterale, per ripristinare la legalità del programma se la previsione risulta sbagliata. Il codice di compensazione, specialmente per gli ingressi laterali, è estremamente complesso da generare.

#### L'Ordine di Priorità e la Probabilità
Non viene scelto un solo percorso, ma il compilatore lavora su una gerarchia di probabilità:

- **Schedulazione Decrescente**: I _trace_ vengono selezionati e schedulati in ordine decrescente di probabilità. Questo garantisce che i percorsi eseguiti più spesso ricevano l'ottimizzazione migliore, anche a scapito di quelli meno frequenti.
    
- **Dipendenza dall'Input**: La probabilità che un _trace_ venga effettivamente eseguito dipende strettamente dal set di dati in ingresso al programma.
    
#### Il Limite dei Cicli (Loop Barriers)
È importante specificare che questa tecnica ha un "confine" invalicabile:

- **Barriera dei Loop**: Il _trace scheduling_ non può procedere oltre la barriera di un ciclo.
    
- **Sinergia con l'Unrolling**: Per superare questo limite, la tecnica deve essere combinata con il _loop unrolling_, che trasforma le iterazioni dei cicli in una sequenza lineare di blocchi che il _trace scheduling_ può poi processare.
    
#### Meccanismi di Ottimizzazione Globale
Per far sì che il codice si muova liberamente lungo la traccia, il compilatore usa strumenti specifici:

- **Renaming (Rinomina)**: Lo spostamento delle istruzioni tra diversi blocchi base all'interno del _trace_ si affida spesso alla rinomina dei registri per evitare conflitti e mantenere la correttezza dei dati.
    
- **Trade-off delle Prestazioni**: Bisogna evidenziare che i percorsi "non comuni" (quelli fuori dalla traccia principale) possono subire un rallentamento (overhead) a causa delle istruzioni di compensazione aggiunte.
    
#### Vincoli e Soluzioni Avanzate (Sicurezza e Legalità)
Il movimento del codice non è libero, ma deve seguire regole ferree:

- **Preservazione del Flusso**: Lo spostamento non deve mai alterare il flusso dei dati del programma (Legalità) né cambiare il comportamento delle eccezioni (Sicurezza).
    
- **Esecuzione Speculativa e Predicata**: Per superare i vincoli di dipendenza dal controllo, il compilatore può usare:
    
    - **Istruzioni Speculative**: Muovono un'istruzione prima del ramo da cui dipende (es. caricare un dato prima di sapere se servirà davvero).
        
    - **Istruzioni Predicate**: Trasformano i rami in istruzioni condizionali (come nell'architettura IA-64), rimuovendo del tutto il salto condizionale.

### 7. Code Motion
#### Dallo spostamento semplice a quello "globale"
Inizialmente, il _code motion_ è una tecnica di base per mantenere piene le pipeline o le unità funzionali. Tuttavia, nel Trace Scheduling, questa tecnica acquisisce un **"gusto globale"** poiché le istruzioni non vengono spostate solo all'interno di un singolo blocco, ma attraverso i confini di più blocchi di base. Per evitare conflitti durante questi spostamenti, il compilatore utilizza in modo appropriato il **renaming** (rinomina dei registri).

#### Le sfide: Side Exit e Side Entry
Spostare il codice lungo una traccia non è privo di rischi, specialmente quando si incontrano rami che entrano o escono dal percorso principale:

- **Sotto una Side Exit (Semplice):** Spostare un'istruzione al di sotto di un punto in cui il programma potrebbe uscire dal trace è considerato un'operazione relativamente semplice.
    
- **Sotto una Side Entry (Complesso):** Spostare un'istruzione al di sotto di un punto in cui il flusso di controllo entra lateralmente nel trace è molto più complicato e richiede una gestione meticolosa del codice di compensazione.
    
#### I due pilastri: Sicurezza e Legalità
Il compilatore deve rispettare restrizioni ferree affinché lo spostamento sia valido:

- **Legalità:** Il risultato finale del programma deve essere sempre corretto, garantendo che il flusso dei dati non cambi.
    
- **Sicurezza:** Lo spostamento non deve causare "eccezioni spurie" (ad esempio, un errore di memoria che non si sarebbe verificato nel codice originale).
    
#### Cosa limita il movimento?
Non tutto il codice può essere spostato liberamente a causa di due ostacoli principali:

- **I Branch (Salti):** Le diramazioni limitano la capacità del compilatore di muovere il codice liberamente.
    
- **I Memory Hazards:** I pericoli legati ai puntatori della memoria (due istruzioni che potrebbero puntare allo stesso indirizzo) limitano pesantemente lo scheduling.
    
#### Soluzioni avanzate
Per superare i vincoli imposti dalle dipendenze di controllo, il documento propone due soluzioni:

- **Istruzioni Predicate (Hyperblock):** Eliminano i branch trasformando il controllo in un'operazione logica.
    
- **Speculative Scheduling:** Muove speculativamente un'istruzione prima del branch da cui dipenderebbe. Un esempio è il **Load Speculativo**, che carica un dato in anticipo senza causare eccezioni, impostando un "poison bit" se qualcosa va storto e verificandolo solo successivamente nel blocco originale.

### 8. Speculazione ed Esecuzione Predicata (IA-64)

Per superare la rigidità dei salti (Branch), i progettisti usano tecniche speculative (Hardware o Software) che tentano di ipotizzare il comportamento delle istruzioni per anticiparne l'esecuzione.

- **Predicated Execution (Esecuzione Predicata):** Permette di eliminare del tutto i salti difficili da prevedere. Nel set IA-64, quasi tutte le istruzioni possono essere eseguite in modo condizionale basandosi su un _registro predicato_. Invece di diramarsi in due blocchi (if/else), il compilatore valuta la condizione in registri booleani (es. `p1`, `p2`) ed emette entrambe le istruzioni contemporaneamente. L'istruzione che ha il predicato _Falso_ diventa dinamicamente una `nop` (nessuna operazione). Questo trasforma un hazard di controllo in una dipendenza sui dati e fonde 4 blocchi base in un unico blocco.

- **Speculazione Software sulle Load:** Si vuole spostare una Load in alto (prima di un branch o di una Store) per nascondere la latenza di memoria, ma c'è il rischio di causare un'eccezione o leggere un dato non aggiornato (Memory Hazard). L'hardware fornisce supporto tramite:

    - _Load Speculative:_ Una load anticipata che, in caso di errore (es. page fault fittizio), non genera un'eccezione immediata ma imposta un **bit "veleno" (poison bit)** sul registro destinazione. L'eccezione scatta solo se e quando un'istruzione "check" conferma che il dato era realmente necessario nel blocco originale.
    
    - _Advance Load (Data Speculative Load):_ Una load posta prima di una Store potenzialmente conflittuale. Aggiunge l'indirizzo in una speciale **Address Check Table** hardware (associativa). Se la Store successiva scrive effettivamente a quell'indirizzo, invalida l'elemento in tabella. Un'istruzione di controllo (Check) successiva forza il salto a un _fixup code_ se il dato è stato invalidato.

"Advance Load", il termine tecnico specifico per la tabella hardware citata è **ALAT (Advanced Load Address Table)**.
- **Funzionamento**: Quando viene eseguita una `ld.a` (advance load), l'indirizzo viene inserito nell'ALAT. Qualsiasi Store successiva allo stesso indirizzo "cancella" la voce corrispondente. L'istruzione `chk.a` (check advance) controlla semplicemente se l'indirizzo è ancora presente nell'ALAT; se non c'è, fa scattare il codice di recupero.