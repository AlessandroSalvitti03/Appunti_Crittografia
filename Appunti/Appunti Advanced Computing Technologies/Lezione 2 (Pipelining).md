
### 1. Il Processore MIPS e l'Esecuzione Sequenziale

Il processore MIPS si basa su un'architettura RISC (Reduced Instruction Set Computer) di tipo LOAD/STORE. Questo significa che le operazioni della ALU avvengono solo sui registri, e servono istruzioni dedicate (`lw` e `sw`) per trasferire i dati tra i registri e la memoria.

L'esecuzione di ogni istruzione nel MIPS può essere suddivisa in un massimo di 5 fasi fondamentali:

1. **Instruction Fetch (IF):** Prelevamento dell'istruzione dalla memoria usando il Program Counter (PC).

2. **Instruction Decode e Register Fetch (ID):** Decodifica dell'istruzione e lettura degli operandi dal Register File (RF).

3. **Execution (EX):** La ALU esegue l'operazione aritmetico-logica, oppure calcola l'indirizzo di memoria per i salti e le load/store.

4. **Memory Access (MEM):** Accesso in lettura o scrittura alla memoria dati.

5. **Write-Back (WB):** Scrittura del risultato finale nel Register File.

**Approfondimento sull'implementazione:** In un'implementazione a singolo ciclo, la durata del clock deve accomodare l'istruzione più lenta (tipicamente la `Load`, es. 8ns). Per migliorare l'efficienza temporale e condividere le risorse, si passa a un'implementazione **multi-ciclo**, in cui ogni singola fase richiede un ciclo di clock più breve (es. 2ns). Questo riduce il ciclo base, ma un'istruzione impiega comunque più cicli (es. 5 cicli per un totale di 10ns).


- **Registri**: Il MIPS dispone di **32 registri a 32 bit** (GPR). Il registro **R0** contiene sempre il valore zero.

- **Formato Istruzioni**: Tutte le istruzioni hanno una dimensione fissa di **32 bit** (4 byte). Esistono 3 formati principali (Register-Register, Register-Immediate, Jump/Call).

- **Istruzioni di Salto**: Oltre a `beq` e `bne` (condizionali), ricorda `j` (jump incondizionato) e `jr` (jump register, che salta all'indirizzo contenuto in un registro) .

### 2. Il Concetto di Pipelining

Il pipelining è una tecnica di ottimizzazione delle prestazioni basata sulla **sovrapposizione dell'esecuzione di istruzioni multiple** che derivano da un flusso sequenziale.

**Esempio (L'analogia del bucato):** I libri di testo spiegano la pipeline usando la metafora di una lavanderia. Supponiamo di dover fare 4 carichi di bucato, ognuno dei quali richiede lavaggio, asciugatura, stiratura e riposizionamento nell'armadio. Invece di aspettare che il primo carico sia completamente nell'armadio prima di lavare il secondo (esecuzione sequenziale), l'approccio in pipeline prevede di inserire il secondo carico in lavatrice non appena il primo passa nell'asciugatrice. In questo modo tutte le macchine (gli stadi) lavorano simultaneamente su carichi (istruzioni) diversi.

**Principi chiave:**

- **Throughput vs Latenza:** Il pipelining migliora il _throughput_ (la quantità di istruzioni completate nell'unità di tempo) ma **non riduce la latenza** (il tempo di esecuzione della singola istruzione). Nel MIPS, un'istruzione impiegherà sempre 5 cicli per attraversare i 5 stadi.

- **Speedup Ideale:** Se gli stadi sono perfettamente bilanciati, lo speedup asintotico è teoricamente uguale al numero di stadi della pipeline (in questo caso, un miglioramento di quasi 5 volte).

___

- **Rispetto al Singolo Ciclo**: La latenza peggiora (da 8ns a 10ns), ma il throughput migliora di **4 volte** (1 istruzione completata ogni 2ns invece di ogni 8ns).

- **Rispetto al Multi-Ciclo**: La latenza resta invariata (10ns), ma il throughput migliora di **5 volte** (1 istruzione ogni 2ns invece di ogni 10ns).

- **Condizione Ideale**: Lo speedup è uguale al numero di stadi solo se questi sono **perfettamente bilanciati**.

### 3. I Pericoli della Pipeline: Gli Hazard

Nella realtà, ci sono situazioni in cui l'istruzione successiva non può essere eseguita nel ciclo di clock previsto, riducendo lo speedup ideale. Questi eventi si chiamano **Hazard** e si dividono in tre categorie: Strutturali, sui Dati e sul Controllo.

#### A. Hazard Strutturali

Si verificano quando l'hardware non supporta la combinazione di istruzioni che si vogliono eseguire contemporaneamente nello stesso ciclo di clock (tentativo di usare la stessa risorsa per due compiti diversi).

- **Come li evita il MIPS:** L'architettura MIPS previene la maggior parte degli hazard strutturali separando fisicamente la memoria Dati dalla memoria Istruzioni (evitando conflitti tra IF e MEM).

- **Approfondimento sul Register File:** Anche il Register File viene usato contemporaneamente dallo stadio ID (lettura) e WB (scrittura). Per evitare hazard, si sfrutta una temporizzazione ottimizzata: la scrittura nel registro avviene nella **prima metà del ciclo di clock**, mentre la lettura nella **seconda metà**. In questo modo si evita l'inserimento di stalli.

#### B. Hazard sui Dati (Data Hazards)

Avvengono quando un'istruzione dipende dai risultati di un'istruzione precedente che si trova ancora all'interno della pipeline (tentativo di usare un risultato prima che sia pronto).

**Tipi di dipendenza:**

- **RAW (Read After Write - Dipendenza vera):** Un'istruzione legge un dato prima che il precedente lo scriva. È il caso standard della pipeline MIPS a 5 stadi.

- **WAW (Write After Write) e WAR (Write After Read):** Questi non possono accadere nel MIPS di base, poiché le istruzioni richiedono tutte 5 stadi e la scrittura avviene sempre rigidamente nell'ultimo stadio (WB).

- **RAW (Read After Write)**: È l'unico hazard sui dati possibile nella pipeline MIPS standard a 5 stadi.

- **WAR e WAW**: Le slide chiariscono che **non possono avvenire** nel MIPS a 5 stadi perché le letture avvengono sempre allo stadio 2 (ID) e le scritture sempre allo stadio 5 (WB) . Potrebbero però presentarsi in pipeline più complesse o con unità multi-ciclo (es. Floating Point).

- **Load/Store Hazard**: Questo è un caso particolare menzionato dal prof. Se una `lw` è seguita immediatamente da una `sw` che usa lo stesso registro, con il forwarding corretto (dal registro MEM/WB all'ingresso della memoria) si possono avere **0 stalli** .

**Soluzioni agli Hazard sui Dati:**

1. **Soluzioni Software (Scheduling / Nops):** Il compilatore può intervenire inserendo istruzioni `nop` (no operation) oppure "riordinando" il codice (Instruction Scheduling). Esegue cioè istruzioni indipendenti per distanziare l'istruzione che produce il dato da quella che lo consuma.

2. **Soluzioni Hardware (Bolle/Stalli):** L'hardware rileva l'hazard e inserisce una "bolla" (bubble), congelando la prima parte della pipeline e facendo scorrere a vuoto la seconda parte per 1 o più cicli finché il dato non viene scritto. Rallenta molto le prestazioni.

3. **Propagazione o Bypassing (Forwarding):** È l'aggiunta di percorsi hardware (multiplexer) che prendono un risultato temporaneo appena calcolato dalla ALU (es. nel registro ID/EX o EX/MEM) e lo passano ("forwardano") direttamente all'input della ALU per l'istruzione successiva, senza aspettare che raggiunga lo stadio WB.

**Eccezione al Forwarding: Il Load-Use Hazard** **Esempio/Approfondimento:** Se un'istruzione `lw` preleva un dato che serve all'istruzione successiva (es. `add`), il dato dalla memoria è disponibile solo **alla fine dello stadio MEM**. Ma all'istruzione `add` serve all'inizio del suo stadio EX (che avviene contemporaneamente al MEM della load). In questo caso, il forwarding puro non è sufficiente. L'hardware (tramite una "Hazard Detection Unit") deve obbligatoriamente inserire **1 ciclo di stallo** per far slittare l'istruzione ricevente, per poi applicare il forwarding.

#### C. Hazard sul Controllo (Branch Hazards)

Insorgono per via delle istruzioni di salto condizionato (come la `beq`). Il problema nasce perché bisogna decidere quale istruzione caricare nel ciclo successivo, ma l'esito della condizione viene tipicamente valutato solo più avanti nella pipeline.

**Soluzioni ai Control Hazards:**

- **Stallo al Branch (Stall):** La soluzione più semplice e conservativa. Consiste nel fermare l'Instruction Fetch e aspettare finché non si conosce la destinazione del salto. È un metodo lento e penalizzante per le prestazioni.

- **Predizione dei salti (Branch Prediction):** Si cerca di intuire l'esito del salto (es. assumendo che il salto "non venga effettuato" e proseguendo l'esecuzione sequenziale). Se l'hardware indovina, non si perde tempo. Se sbaglia, l'hardware effettua un _flush_ (svuotamento), cancellando le istruzioni prelevate erroneamente (sostituendole con dei `nop`) e preleva all'indirizzo corretto. Tecniche più avanzate usano la "predizione dinamica", in cui si guarda la storia recente (se il salto è stato preso o no in precedenza) per migliorare l'affidabilità della previsione.

- **Delayed Branch (Salto ritardato):** Menziato nel libro _Computer Organization_, è una tecnica in cui il calcolatore esegue _sempre_ l'istruzione che si trova nello slot immediatamente successivo al salto (il _branch delay slot_). È compito del compilatore riordinare il codice per posizionare in quello slot un'istruzione utile e indipendente dal salto, per mascherare il ritardo in modo trasparente.