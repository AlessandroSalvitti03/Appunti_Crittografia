### 1. Analisi di un Ciclo con Tomasulo

Il grande vantaggio dell'algoritmo di Tomasulo risiede nella sua capacità di parallelizzare l'esecuzione di cicli iterativi. Le slide presentano l'analisi ciclo per ciclo del seguente loop: `Loop: LD F0, 0(R1)` `MULTD F4, F0, F2` `SD F4, 0(R1)` `SUBI R1, R1, #8` `BNEZ R1, Loop`

Per comprendere il funzionamento, vengono fatte delle assunzioni sulle latenze: la moltiplicazione richiede 4 cicli di clock, mentre la prima `LD` subisce un _cache miss_ impiegando 8 cicli, e la `LD` della seconda iterazione è un _hit_ da 1 solo ciclo.

### 2. Esecuzione Dinamica e "DataFlow" Graph

L'esecuzione del codice svela come l'hardware aggiri gli stalli:

- **Creazione del DataFlow:** Già al ciclo 3, la ridenominazione implicita dei registri crea un vero e proprio grafo "DataFlow", instradando i dati direttamente dalle unità di memoria (`Load1`) alla stazione di prenotazione della moltiplicazione (`Mult1`).

- **Isolamento delle iterazioni:** Il registro `F0` della seconda iterazione non vede mai la `Load` dalla locazione 80 associata alla prima iterazione. Al ciclo 7, si osserva che **il Register File è completamente distaccato dalla prima iterazione**.

- **Sovrapposizione Totale:** Al ciclo 8, la prima e la seconda iterazione del ciclo si sovrappongono completamente (overlapped). L'hardware sta calcolando i dati della seconda iterazione prima ancora che la prima sia terminata.

### 3. Perché Tomasulo permette la sovrapposizione?

Il motivo per cui Tomasulo riesce a sovrapporre le iterazioni dei cicli si basa su due pilastri architetturali:

1. **Register Renaming (Ridenominazione dei registri):** Le diverse iterazioni utilizzano destinazioni fisiche differenti per i registri, ottenendo di fatto uno **srotolamento dinamico del ciclo in hardware (dynamic loop unrolling)**. L'hardware sostituisce i nomi statici dei registri nel codice con "puntatori" dinamici, incrementando virtualmente la dimensione del register file ed evitando conflitti.

2. **L'Unità Intera e la Predizione dei Salti:** Affinché più iterazioni possano essere emesse in parallelo, è cruciale che l'unità dedicata ai calcoli interi (`SUBI`, `BNEZ`) "vada avanti" (_get ahead_) rispetto alla più lenta unità in virgola mobile. Per fare questo, l'hardware deve fare affidamento sulla **Branch Prediction (Predizione dei Salti)**, permettendo all'emissione delle istruzioni di oltrepassare i blocchi di base e le istruzioni di controllo del flusso logico.

### 4. Svantaggi e Limiti Pratici (Drawbacks)

Nonostante i formidabili benefici, questo schema presenta delle criticità tecnologiche:

- **Complessità:** Richiede un'enorme quantità di hardware aggiuntivo e ha causato ritardi storici nello sviluppo di macchine come l'IBM 360/91 e il MIPS 10000.

- **Memorie associative ad alta velocità:** Sono necessarie molte logiche di comparazione per monitorare costantemente il bus.

- **Il collo di bottiglia del Common Data Bus (CDB):** Le prestazioni massime sono limitate dalla larghezza di banda del CDB. Per alleviare questo limite si possono inserire CDB multipli, ma questo porta a far esplodere la complessità logica delle unità funzionali, che devono possedere memorie associative parallele per sorvegliare più bus contemporaneamente.

### 5. Sintesi e Contributi Duraturi (Lasting Contributions)

L'architettura di Tomasulo ha lasciato un segno indelebile nei moderni processori, con tre contributi chiave: la schedulazione dinamica, il register renaming e la disambiguazione delle istruzioni Load/Store.

I concetti fondamentali che lo rendono ancora attuale sono:

- **Sfruttamento Hardware dell'ILP:** Funziona perfettamente anche quando le dipendenze non sono note al momento della compilazione. Questo garantisce portabilità: un binario compilato per una vecchia macchina continua a girare velocemente anche su macchine di nuova generazione.

- **Reservation Stations:** Agendo da buffer per gli operandi e permettendo di rinominare un set più ampio di registri, impediscono ai registri di diventare un collo di bottiglia, annullano gli hazard WAW e WAR che affliggevano lo Scoreboard, e permettono lo srotolamento del ciclo direttamente in hardware.

- **Oltre i Basic Blocks:** Come dimostrato nel ciclo di esempio, il calcolatore non è limitato all'interno dei rami del codice lineare (basic blocks), ma si spinge ben oltre i salti (branch).