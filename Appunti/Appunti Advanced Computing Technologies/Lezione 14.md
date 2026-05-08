### 1. Il Ruolo della Speculazione nell'Estrazione dell'ILP

Come visto studiando i limiti dell'ILP, estrarre alti livelli di parallelismo richiede di superare gli stalli causati dalle dipendenze di controllo (i branch). La soluzione adottata dalle architetture moderne è la **Speculazione**: un approccio in cui il compilatore o il processore tentano di "indovinare" il risultato di un'istruzione (ad esempio prevedendo l'esito di un salto condizionato) per poterne rimuovere le dipendenze e avviare le istruzioni successive prima del tempo.

La gestione della speculazione (e in generale degli hazard) divide storicamente i progettisti in due grandi scuole di pensiero: la **Parallelizzazione Statica** (affidata al Compilatore/Software) e la **Parallelizzazione Dinamica** (affidata all'Hardware).

### 2. Compiler Speculation (Speculazione basata sul Software / Statica)

Nella speculazione basata sul software, il compilatore ha la totale responsabilità di trovare istruzioni indipendenti, riordinare il codice, rimuovere gli hazard e impacchettare le istruzioni in modo che l'hardware possa eseguirle senza mai doversi fermare a controllare le dipendenze. Questo approccio è la base delle architetture **VLIW** (Very Long Instruction Word) come l'Intel Itanium (IA-64).

- **Il Meccanismo:** Il compilatore sfrutta tecniche globali come il _Trace Scheduling_ (o il Loop Unrolling) analizzando un'amplissima "finestra di istruzioni" del programma (molto più grande di quella che l'hardware potrebbe analizzare in tempo reale). Sposta istruzioni di Load prima dei salti (Load Speculative) per nascondere i ritardi della memoria. Dove non ci sono istruzioni parallele disponibili, il compilatore inserisce istruzioni vuote (`NOP`).
- **I Vantaggi:** Eliminando la necessità di controllare gli hazard a tempo di esecuzione, l'hardware del processore è estremamente snello e semplice. Consuma molta meno energia (evitando strutture complesse come il Reorder Buffer o le Station di Prenotazione) e permette l'inserimento di più unità di calcolo nello stesso chip a parità di transistor.
- **Gli Svantaggi (Il collo di bottiglia):** Il compilatore non possiede la sfera di cristallo. Non può prevedere gli eventi dinamici imprevisti a run-time, il più grave dei quali è una **miss nella memoria cache**. Se il compilatore ha impacchettato rigidamente le istruzioni e si verifica una miss, il processore è costretto a bloccarsi brutalmente. Inoltre, il codice compilato per una macchina VLIW perde il requisito della **compatibilità binaria**: un software scritto per un VLIW a 4 vie non funzionerà correttamente su un VLIW a 8 vie senza essere ricompilato.

### 3. Hardware Speculation (Speculazione basata sull'Hardware / Dinamica)

Nella speculazione hardware, tipica dei processori superscalari moderni (come la famiglia x86, Intel Core i7 o ARM Cortex ad alte prestazioni), la macchina riceve un flusso di istruzioni sequenziale ed è l'hardware stesso a decidere dinamicamente quali eseguire fuori ordine e in parallelo.

- **Il Meccanismo:** Il processore utilizza **Predittori dei salti (Branch Predictors)** (come predittori a torneo o predittori ibridi a più livelli) per indovinare il flusso del codice. Attraverso l'algoritmo di Tomasulo Speculativo, usa il **Register Renaming (Ridenominazione dei Registri)** per evitare dinamicamente le false dipendenze (WAW e WAR) ed esegue le istruzioni parcheggiando i risultati nel **Reorder Buffer**. Quando la speculazione è corretta, i risultati diventano permanenti; se è errata, l'hardware azzera le mappature del Reorder Buffer e scarta il lavoro speculativo.
- **I Vantaggi:** Gestisce perfettamente eventi asincroni o latenze variabili. Se un'istruzione genera una miss in cache, l'esecuzione fuori ordine permette all'hardware di far avanzare le altre istruzioni libere da dipendenze, "nascondendo" le latenze di memoria in tempo reale. Soprattutto, mantiene una totale compatibilità del codice.
- **Gli Svantaggi:** La logica di controllo (scoreboard, buffer di riordino, renaming, reti di bypass) richiede una quantità impressionante di transistor, aumentando spaventosamente i consumi energetici e rendendo complicato l'aumento della frequenza del clock (Power Wall). L'errore speculativo si paga caro: nel Core i7 una misprediction costa circa 17 cicli di clock sprecati per riempire nuovamente la pipeline.

_(Conoscenza Esterna)_: **Il verdetto (Crosscutting Issue):** Nel mondo reale dei general-purpose computer, ha "vinto" la speculazione hardware. La complessità del comportamento asincrono delle memorie cache e l'assoluta necessità economica di far girare codice binario "vecchio" in maniera sempre più veloce, ha spinto l'industria ad abbracciare i consumi e la complessità hardware, abbandonando quasi del tutto i sogni di gloria del VLIW.

### 4. Oltre i Limiti dell'ILP: L'Evoluzione verso il Multithreading Hardware (TLP)

Poiché aumentare il numero di istruzioni parallele emesse in speculazione hardware porta a rendimenti decrescenti a causa dei limiti fisici imposti dal "Muro della Memoria" e dal "Muro del Controllo" (branch misprediction), i progettisti hanno dovuto trovare un nuovo modo per mantenere le massicce pipeline superscalari costantemente occupate.

La soluzione consiste nel passare dallo sfruttamento del parallelismo delle istruzioni (ILP) allo sfruttamento del **Parallelismo a Livello di Thread (Thread-Level Parallelism - TLP)** tramite il Multithreading hardware. Un thread è un processo "leggero" che possiede un proprio stato architetturale: un Program Counter, un insieme di registri e un proprio stack.

_Approfondimento - Gli approcci al Multithreading hardware:_

1. **Multithreading a grana fine (Fine-Grained):** Il processore possiede lo stato hardware di molteplici thread e, ad _ogni ciclo di clock_, scambia il thread in esecuzione, tipicamente seguendo un algoritmo round-robin.
    - _Vantaggio:_ È in grado di nascondere qualunque tipo di stallo (anche le lunghissime latenze della memoria). Se il Thread 1 aspetta la cache, la macchina esegue semplicemente istruzioni dei Thread 2, 3 e 4.
2. **Multithreading a grana grossa (Coarse-Grained):** Il processore cambia thread esclusivamente in corrispondenza di eventi particolarmente "pesanti", come una miss nella cache di livello 2 o livello 3, che comporterebbe centinaia di cicli di stallo per accedere alla memoria principale DRAM.
3. **Simultaneous Multithreading (SMT):** _(La soluzione adottata dai moderni Intel Core sotto il nome commerciale di Hyper-Threading)_. In un processore superscalare ad emissione multipla dinamica ci sono molti slot di esecuzione liberi (magari perché un singolo thread non ha abbastanza ILP). L'SMT riempie queste "bocche da fuoco" permettendo all'hardware di avviare, nello stesso esatto ciclo di clock, micro-istruzioni provenienti da thread completamente diversi, sfruttando al massimo le risorse e massimizzando il _throughput_ del calcolatore.