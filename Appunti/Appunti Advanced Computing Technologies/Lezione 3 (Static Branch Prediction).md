

### 1. Il Problema dei Salti: I Control Hazards

Le istruzioni di salto condizionato (come `beq` o `bne` nel MIPS) verificano una condizione e, se questa è soddisfatta, modificano il Program Counter (PC) con l'indirizzo di destinazione del salto, detto **Branch Target Address (BTA)**.

In un'architettura con pipeline, questo meccanismo crea un problema fondamentale: per mantenere la pipeline piena, il processore deve prelevare (fetch) una nuova istruzione a ogni ciclo di clock. Tuttavia, la decisione di effettuare o meno il salto (e il calcolo del BTA) non è nota immediatamente. Questo ritardo nel determinare quale sia l'istruzione corretta da prelevare genera un **Control Hazard** (o Branch Hazard).

I Control Hazards riducono le prestazioni reali rispetto allo speedup ideale della pipeline, e la perdita dipende dalla penalità del salto (i cicli di stallo) e dalla frequenza dei salti nel codice.

- **Equazione del CPI**: Il $CPI$ reale in presenza di salti è dato da:

		$CPI_{base} + \text{Frequenza dei salti} \times \text{Penalità del salto}$.

- **Frequenza dei salti**: Nelle slide viene indicato che i salti condizionali rappresentano circa il **20%** delle istruzioni eseguite, mentre i salti incondizionati (jump) circa il **5%**.
    
- **Penalità dei Jump**: Ricorda che per i salti incondizionati (`j`), la penalità è fissa di **1 ciclo** di stallo, poiché l'indirizzo di destinazione è noto solo dopo lo stadio ID.

### 2. Valutazione Anticipata del PC (Early Evaluation)

Nella pipeline MIPS originaria a 5 stadi, l'esito del salto e il BTA sono pronti solo al termine dello stadio di esecuzione (EX) o decisi in memoria (MEM). Senza alcuna ottimizzazione (e senza propagazione), questo richiederebbe 3 cicli di stallo; con la propagazione, si ridurrebbero a 2 cicli.

**Come migliorare le prestazioni?** L'obiettivo è spostare l'esecuzione del salto il prima possibile nella pipeline. L'ottimizzazione standard consiste nell'aggiungere risorse hardware per spostare la decisione nello **stadio ID (Instruction Decode)**:

- Si aggiunge un sommatore dedicato nello stadio ID per calcolare il BTA.

- Si aggiunge un hardware dedicato per il confronto dei registri. L'uguaglianza (`beq`) può essere verificata in modo rapidissimo sfruttando semplici reti logiche (come lo XOR bit a bit seguito da un OR generale), senza dover impiegare la complessa ALU principale (come descritto nell'Appendice C per la costruzione delle ALU di base).

**Conseguenza:** Spostando la decisione nello stadio ID, la penalità in caso di salto effettuato si riduce a **un solo ciclo di stallo** (la singola istruzione che è stata sbadatamente prelevata nello stadio IF). Tuttavia, questo ritardo di un ciclo per ogni salto porta ancora a una perdita di prestazioni del 10-30%.

**Sposta-dati (Forwarding)**: Spostando il confronto allo stadio ID, è necessario aggiungere dei **multiplexer di forwarding** specifici per questo stadio. Se i dati necessari al confronto sono prodotti dall'istruzione immediatamente precedente, potrebbe essere comunque necessario uno stallo (Data Hazard nello stadio ID).

### 3. Tecniche di Predizione dei Salti (Branch Prediction)

Per evitare completamente lo stallo, i processori tentano di "indovinare" l'esito del salto. L'efficacia di una tecnica di predizione si valuta in base all'**accuratezza** (percentuale di predizioni corrette) e al **costo della penalità** in caso di errore (misurato in tempo perso per eliminare le istruzioni inutili, operazione nota come _flush_).

Esistono due macro-categorie:

- **Predizione Dinamica:** L'hardware valuta l'andamento del programma a runtime, cambiando la predizione in base alla storia recente del salto (es. con buffer di predizione a 1 o 2 bit).

- **Predizione Statica:** Le azioni da compiere per un salto sono decise a tempo di compilazione e rimangono fisse per l'intera esecuzione.

**BPB (Branch Prediction Buffer)**: È una piccola memoria indirizzata dalla parte bassa dell'indirizzo dell'istruzione di salto che contiene bit che indicano se il salto è stato preso o meno l'ultima volta.

### 4. Tecniche di Predizione Statica

I processori e i compilatori possono utilizzare diverse strategie statiche:

- **Branch Always Not Taken (Sempre Non Effettuato):** Si assume che il salto fallisca sempre. La pipeline continua a prelevare le istruzioni sequenziali come se il salto non ci fosse. Se la predizione è corretta, non si perde alcun ciclo. Se la condizione nello stadio ID si rivela verificata (predizione errata), la pipeline subisce un _flush_: l'istruzione appena prelevata viene trasformata in una `nop` (nessuna operazione) e si preleva quella corretta al BTA, con una penalità di 1 ciclo.

- **Branch Always Taken (Sempre Effettuato):** Si assume che il salto avvenga. Tuttavia, nella pipeline MIPS, non c'è alcun vantaggio ad applicare questo schema perché l'indirizzo di destinazione (BTA) viene calcolato nello stesso momento in cui si conosce l'esito della condizione (stadio ID). Non potendo sapere prima la destinazione, non si può anticipare il fetch.

- **BTFNT (Backward Taken Forward Not Taken):** Si basa sulla direzione del salto. I salti all'indietro (_backward_) sono usati tipicamente per chiudere i cicli (loop), che hanno una forte probabilità di essere ripetuti, quindi vengono predetti come "effettuati". I salti in avanti (_forward_), usati spesso per i costrutti `if-else`, vengono predetti come "non effettuati".

- **Profile-Driven Prediction:** Usa le statistiche (profiling) raccolte in esecuzioni precedenti del programma per suggerire al compilatore quali salti verranno effettuati con maggiore probabilità.

- **Efficacia**: La predizione statica "Sempre non effettuato" ha un'accuratezza di circa il **33%** (poiché molti salti sono loop eseguiti molte volte).

- **BTFNT**: Questa tecnica è molto più efficace (accuratezza circa **63-69%**) perché sfrutta la natura dei cicli.

- **Profile-Driven**: Questa è la più accurata tra le statiche, raggiungendo il **75-92%** di successo, ma richiede una fase di pre-esecuzione del programma per raccogliere i dati.
### 5. Ottimizzazione Avanzata: Il Salto Ritardato (Delayed Branch)

Il salto ritardato (Delayed Branch) è una soluzione molto elegante per i Control Hazards: la pipeline MIPS esegue **sempre** l'istruzione che segue immediatamente l'istruzione di salto (cioè quella inserita nel _branch delay slot_), a prescindere dal fatto che il salto venga poi effettivamente preso o meno. In questo modo lo slot di ritardo di 1 ciclo viene mascherato.

È compito esclusivo del compilatore trovare un'istruzione indipendente e utile da inserire nel delay slot. Ci sono tre strategie di scheduling per riempire questo slot:

1. **From before (Da prima):** Si sposta un'istruzione indipendente antecedente al salto all'interno del delay slot. È la scelta ottimale, poiché questa istruzione doveva comunque essere eseguita.

2. **From target (Dalla destinazione):** Si copia nel delay slot l'istruzione che si trova al BTA. Questa strategia è preferibile se c'è un'alta probabilità che il salto venga preso (come nei salti all'indietro dei cicli).

3. **From fall-through (Dal percorso sequenziale):** Si anticipa nel delay slot l'istruzione del percorso non preso. È preferibile se si è quasi sicuri che il salto non verrà effettuato.

**Approfondimento / Requisito fondamentale:** Per poter applicare legalmente le strategie 2 e 3, l'istruzione spostata nel delay slot non deve causare danni al programma se la predizione del compilatore si rivela errata. L'esecuzione di quell'istruzione avverrà lo stesso (sprecando lavoro), ma non deve sovrascrivere registri o causare eccezioni invalidando il corretto funzionamento dell'algoritmo (ad esempio, è sicuro farlo se la destinazione è un registro temporaneo altrimenti inutilizzato nel percorso alternativo). Quando il compilatore non riesce a trovare alcuna istruzione adatta da spostare in sicurezza, deve necessariamente riempire il delay slot con un'inutile `nop`.

- **Annullamento (Cancelling/Nullifying Branches)**: Alcune architetture usano salti che "annullano" l'istruzione nel delay slot se la predizione è errata. Se il salto è predetto "preso" e non avviene, l'istruzione nel delay slot viene trasformata in `nop` dall'hardware. Questo permette al compilatore di essere più aggressivo nello scheduling senza rischiare di corrompere lo stato del programma.