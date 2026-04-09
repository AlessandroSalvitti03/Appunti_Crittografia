
### 1. Definizione di ILP e Limiti della Pipeline Base

L'**Instruction-Level Parallelism (ILP)** (Parallelismo a livello di istruzioni) è la potenziale sovrapposizione dell'esecuzione di istruzioni non correlate all'interno di un programma.

Fino ad ora abbiamo visto la pipeline di base, le cui prestazioni sono regolate dalla seguente equazione: 

**CPI della Pipeline = CPI ideale + Stalli Strutturali + Stalli per Hazard sui Dati + Stalli per Hazard di Controllo**. 

È la misura della massima performance ottenibile dall'implementazione (che sarebbe 1.0 per una pipeline semplice a 5 stadi, ma inferiore a 1.0 per i processori Multiple Issue).

Qualsiasi tecnica progettata per incrementare le prestazioni (riducendo uno di questi termini) cerca in realtà di estrarre e sfruttare maggiormente l'ILP dal programma. L'ILP può essere incrementato principalmente in due modi: aumentando la profondità della pipeline (per sovrapporre più istruzioni) oppure impiegando processori a **emissione multipla (Multiple Issue)**, capaci di avviare l'esecuzione di più istruzioni in un singolo ciclo di clock.

### 2. Pipeline Complesse

Nei moderni processori, mischiare operazioni su numeri interi (Integer) e operazioni in virgola mobile (Floating Point) complica notevolmente la pipeline. Mentre l'ALU per gli interi può impiegare 1 solo ciclo di clock, le unità funzionali in virgola mobile (Fadd, Fmul, Fdiv) richiedono latenze maggiori o potrebbero non essere completamente "pipelined" (ad esempio, un'unità Fdiv non-pipelined può impiegare molti cicli occupando fisicamente la risorsa).

Questo scenario complesso porta all'insorgere di nuove problematiche:

- **Conflitti strutturali nello stadio di esecuzione**, causati da unità funzionali non pipelined a lunga latenza.

- **Conflitti strutturali nello stadio di Write-Back (WB)**, in quanto istruzioni con latenze diverse, anche se emesse in cicli diversi, potrebbero terminare e tentare di scrivere i risultati nei registri esattamente nello stesso ciclo di clock.

- **Hazard di scrittura fuori ordine (Out-of-order write hazards):** L'esecuzione fuori ordine introduce la possibilità di incappare in **hazard WAW (Write After Write)** e **WAR (Write After Read)**, che nella pipeline semplice a 5 stadi (in cui si scriveva sempre rigidamente all'ultimo stadio) non potevano accadere.

- Problemi nel garantire eccezioni precise (Precise exceptions).

Esempi di latenza riportati nelle slide per il processore MIPS:

- **FP add**: 4 cicli di clock.

- **FP multiply**: 7 cicli di clock.

- **FP divide (non-pipelined)**: ben 25 cicli di clock. Questi numeri chiariscono perché lo stadio di Write-Back (WB) diventa un collo di bottiglia strutturale quando più unità terminano contemporaneamente.

### 3. Dipendenze vs Hazard

È fondamentale fare una distinzione concettuale: **le dipendenze sono una proprietà del programma, mentre gli hazard sono una proprietà della pipeline** (ovvero di come il calcolatore cerca di eseguire il codice). Se due istruzioni sono dipendenti, non possono essere eseguite del tutto in parallelo.

Esistono tre categorie principali di dipendenze:

1. **True Data Dependences (Dipendenze Vere):** Corrispondono agli **hazard RAW** (Read After Write). Si ha flusso di dati reale da un'istruzione a un'altra.

2. **Name Dependences (Dipendenze Nominali o False Dipendenze):** Avvengono quando due istruzioni usano lo stesso registro/memoria, ma _senza_ che ci sia un flusso di dati tra loro. Si dividono in:
    - _Anti-dipendenza:_ Corrisponde agli **hazard WAR**. Un'istruzione scrive una risorsa che un'istruzione precedente deve leggere.
    - _Dipendenza di output:_ Corrisponde agli **hazard WAW**. Due istruzioni scrivono la stessa risorsa.

3. **Control Dependences (Dipendenze di Controllo):** Determinano l'ordinamento delle istruzioni in base ai salti (es. rami di un costrutto `if`).

**Approfondimento / Soluzione:** Poiché le dipendenze nominali (Anti e Output) non trasportano informazioni, possono essere risolte cambiando il "nome" del registro usato. Questo processo è detto **Register Renaming (Ridenominazione dei registri)** e può essere fatto staticamente dal compilatore o dinamicamente dall'hardware.

### 4. Due filosofie per l'ILP: Schedulazione Statica vs Schedulazione Dinamica

Per gestire la complessità delle emissioni multiple (Multiple Issue) e sfruttare l'ILP, esistono due approcci principali:

- **Static Scheduling (o VLIW / Static Multiple Issue):** Si affida al **software** (il compilatore) per trovare il parallelismo.
- **Dynamic Scheduling (o Superscalari):** Dipende dall'**hardware** per localizzare il parallelismo e riordinare l'esecuzione a runtime.

#### A. Static Scheduling (VLIW)

Nell'approccio statico, il compilatore riordina il codice in modo da creare un **Issue Packet (Pacchetto di istruzioni)**, spesso chiamato VLIW (Very Long Instruction Word), in cui raggruppa istruzioni indipendenti che l'hardware eseguirà simultaneamente.

**Esempio di tecnica: L'espansione dei cicli (Loop Unrolling)** Per ottenere prestazioni migliori sui cicli che operano su array, il compilatore crea copie multiple del corpo del ciclo (es. invece di processare un elemento per iterazione, ne processa 4 assieme). Durante l'unrolling, il compilatore applica la **ridenominazione dei registri**, usando registri temporanei diversi per ogni copia del corpo originario, al fine di eliminare le anti-dipendenze (WAR) e le dipendenze di output (WAW). Questo espone un vasto parallelismo che riempie efficacemente le slot del VLIW.

**Limiti dello Static Scheduling:** La schedulazione statica fa molta fatica in caso di rami imprevedibili (unpredictable branches), latenze di memoria variabili (come i ritardi imprevisti causati dai cache miss), e soffre di "esplosione della dimensione del codice" (code size explosion) e grande complessità del compilatore.

#### B. Dynamic Scheduling (Architetture Superscalari)

Per nascondere stalli imprevedibili come i cache miss e semplificare il compito del compilatore, i processori dominanti nel mercato desktop/server adottano la schedulazione dinamica (Superscalare). In questo caso, l'hardware preleva e riordina le istruzioni per limitare gli stalli, pur mantenendo corretto il flusso di dati e il comportamento delle eccezioni.

L'esecuzione viene divisa concettualmente in tre stadi principali:

1. **In-order Issue (Emissione in ordine):** Le istruzioni vengono prelevate, decodificate ed emesse in ordine di programma.

2. **Out-of-order Execution (Esecuzione fuori ordine):** Le istruzioni vengono parcheggiate in strutture chiamate **Reservation Stations (Stazioni di prenotazione)**, associate alle varie unità funzionali (ALU, FPU). Un'istruzione inizia l'esecuzione non appena _tutti i suoi operandi sono pronti_, indipendentemente dall'ordine originale. Questo risolve dinamicamente gli hazard RAW.

3. **In-order Commit (Ritiro in ordine):** L'esecuzione fuori ordine implica che le istruzioni finiscono in ordine sparso (Out-of-order completion). Per garantire il comportamento "naturale" del programma, i risultati temporanei vengono parcheggiati in un **Reorder Buffer (ROB)**. I risultati vengono scritti permanentemente nei registri o in memoria (operazione di _commit/retirement_) solo nel loro rigoroso ordine sequenziale originale (in-order commit) e solo quando si è certi che l'istruzione non ha causato o subito eccezioni.

**Approfondimento sull'Hardware:** La combinazione delle Reservation Stations e del Reorder Buffer permette all'hardware di implementare la ridenominazione dei registri dinamicamente a runtime (Dynamic Register Renaming), risolvendo così in hardware i conflitti WAW e WAR.

- **Tre fasi del ROB**: Le slide specificano che le istruzioni passano attraverso tre fasi gestite dal buffer: **Issue** (invio), **Execute** (esecuzione) e **Commit/Retire** (ritiro).

- **Risultati temporanei**: Il ROB non serve solo a riordinare, ma funge da deposito per i risultati delle istruzioni che hanno finito l'esecuzione ma non possono ancora essere scritte permanentemente nei registri perché le istruzioni precedenti sono ancora in volo.

___
Parlando dei limiti della schedulazione statica, è utile aggiungere il contesto numerico fornito:

- **Definizione di Basic Block**: Una sequenza di codice rettilineo senza rami in entrata (tranne l'ingresso) e senza rami in uscita (tranne l'uscita).

- **Limite intrinseco**: La quantità di parallelismo disponibile all'interno di un singolo basic block è molto ridotta.

- **Frequenza dei salti**: Nei programmi MIPS tipici, la frequenza dei rami è tra il **15% e il 25%**, il che significa che vengono eseguite solo **da 4 a 7 istruzioni** tra una coppia di salti. Questo spiega perché è indispensabile sfruttare l'ILP _attraverso_ i rami (speculazione) per ottenere prestazioni elevate.

### 5. La Speculazione (Speculation)

Sia i compilatori sia i processori superscalari moderni spingono l'ILP oltre il limite delle dipendenze di controllo utilizzando la **speculazione**: si tratta di "indovinare" le proprietà di un'istruzione per iniziare a eseguire prematuramente le istruzioni successive.

- **Esempi:** Si specula in genere sull'esito di un salto condizionato (Branch Prediction), oppure si può speculare che un'istruzione di memoria (load) non faccia riferimento allo stesso indirizzo di una store precedente, permettendo l'esecuzione anticipata della load.

- **Gestione dell'errore (Misprediction):** Ovviamente, se l'hardware specula male, deve disporre di meccanismi per scartare i risultati calcolati in base alla congettura sbagliata. Nei processori con Dynamic Scheduling, l'hardware semplicemente "svuota" (esegue un _flush_) il Reorder Buffer scartando le istruzioni speculative non ancora ritirate, e riprende l'esecuzione dal percorso corretto.