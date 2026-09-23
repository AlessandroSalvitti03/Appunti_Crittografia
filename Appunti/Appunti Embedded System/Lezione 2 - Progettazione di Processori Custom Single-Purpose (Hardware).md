*PDF di riferimento: 01_SPP.pdf*

**Panoramica della Lezione:**  
Questa lezione affronta la progettazione hardware dei **processori custom single-purpose**, illustrando l'intero flusso di sintesi a livello **Register-Transfer (RT)** a partire da una specifica algoritmica fino alla separazione architetturale tra **Controller** e **Datapath**. Vengono consolidati i fondamenti della **logica combinatoria e sequenziale**, esplorate le strategie di **ottimizzazione** a diversi livelli di astrazione e analizzate le ragioni della maggiore complessità della progettazione hardware rispetto a quella software mediante il **diagramma a Y di Gajski-Kuhn**.

---

## Concetti Chiave (Indice)

- **Architettura e Trade-off dei Processori Custom Single-Purpose**
- **Fondamenti di Logica Combinatoria e Sequenziale a Livello RT**
- **Metodologia di Progettazione a Livello RT: Modello FSMD, Datapath e Controller**
- **Tecniche di Ottimizzazione Hardware (Algoritmo, FSMD, Datapath e FSM)**
- **Complessità della Progettazione HW vs. SW e Diagramma a Y (Gajski-Kuhn)**

---

## Appunti Dettagliati

### 1. Architettura e Trade-off dei Processori Custom Single-Purpose

Un **processore single-purpose** è un circuito digitale progettato per eseguire un'unica ed esclusiva funzione di calcolo. Quando l'applicazione richiesta è non standard (ad esempio un algoritmo proprietario o una logica di controllo specifica), si realizza un **custom single-purpose processor** su misura. Dal punto di vista strutturale, l'architettura interna è chiaramente suddivisa in due blocchi interconnessi: il **controller** (che gestisce la sequenza degli stati) e il **datapath** (che memorizza ed elabora i dati). All'interno di un sistema integrato complesso (come il chip di una fotocamera digitale), questi processori operano in parallelo a **microcontrollori**, **coprocessori di pixel**, **preprocessori CCD**, **codec JPEG** e **controllori DMA**.

- **Compromesso (Trade-off) Hardware/Software**:
    - **Hardware Custom (Single-Purpose Processor)**: Garantisce **prestazioni** elevate (velocità di esecuzione), **area** fisicamente contenuta sul silicio e un ridotto **consumo energetico**. Tuttavia, presenta un elevato **costo NRE (Non-Recurring Engineering)**, un lungo tempo di sviluppo (**time-to-market**) e una **flessibilità** nulla, poiché la funzione è cablata nel circuito e non riprogrammabile.
    - **Software (General-Purpose Processor)**: Garantisce massima flessibilità e bassi costi NRE a scapito dell'efficienza energetica e della velocità pura.

---

### 2. Fondamenti di Logica Combinatoria e Sequenziale a Livello RT

La progettazione hardware si basa sui componenti fisici della tecnologia **CMOS**, costituiti da transistor **nMOS** (che conducono quando il gate è a 1) e **pMOS** (che conducono quando il gate è a 0). La combinazione di questi transistor permette di realizzare le **porte logiche fondamentali** (Inverter, NAND, NOR, AND, OR, XOR, XNOR).

#### A. Logica Combinatoria

- **Flusso di sintesi tradizionale**: Si parte dalla descrizione del problema, si costruisce la **tabella della verità**, si ricavano le **equazioni booleane** di output e le si minimizza (ad esempio tramite Mappe di Karnaugh) per ricavare la rete di porte logiche.
- **Componenti combinatori a livello RT**: A livello di registro-trasferimento si lavora con blocchi complessi quali il **multiplexer** (\(m \times 1\)) per la selezione dei dati, il **decoder** (\(\log n \times n\)) per la decodifica degli indirizzi, il **sommatore** (\(n\)-bit) completo di carry-in e flag di carry-out, il **comparatore** (\(n\)-bit) che genera i segnali relazionali di \(less\), \(equal\), \(greater\), e l'**unità aritmetico-logica (ALU)** (\(n\)-bit) in grado di eseguire \(m\) funzioni selezionabili tramite opportuni ingressi di controllo.

#### B. Logica Sequenziale

- **Componenti sequenziali a livello RT**: Comprendono i **registri** a \(n\)-bit con segnali di abilitazione (\(load\)) e reset (\(clear\)), gli **shift register** e i **contatori**.
- **Modello di implementazione**: Un circuito sequenziale è costituito da un blocco di **logica combinatoria** e da un **registro di stato** sincronizzato dal segnale di clock. La progettazione sequenziale formalizza il comportamento tramite un **diagramma di stato** o una **tabella di stato** (modelli di Moore o Mealy) per poi sintetizzare le equazioni della logica combinatoria per lo stato futuro e le uscite.

---

### 3. Metodologia di Progettazione a Livello RT: Modello FSMD, Datapath e Controller

La metodologia di progettazione a livello RT consente di sintetizzare in modo sistematico un algoritmo descritto in codice ad alto livello in un'architettura hardware formata da **Controller** e **Datapath**.

I passi operativi sono illustrati di seguito prendendo come riferimento classico l'algoritmo per il calcolo del **GCD (Greatest Common Divisor - Massimo Comun Divisore)**:

```
┌────────────────────────────────────────────────────────────────────────┐
│                        Algoritmo in Pseudo-Codice                      │
└───────────────────────────────────┬────────────────────────────────────┘
                                    │  Trasformazione mediante Template
                                    ▼
┌────────────────────────────────────────────────────────────────────────┐
│             FSMD (Finite-State Machine with Datapath)                  │
└───────────────────────────────────┬────────────────────────────────────┘
                                    │
                  ┌─────────────────┴─────────────────┐
                  ▼                                   ▼
┌───────────────────────────────────┐ ┌───────────────────────────────────┐
│             DATAPATH              │ │            CONTROLLER             │
├───────────────────────────────────┤ ├───────────────────────────────────┤
│ • Registri (per ogni variabile)   │ │ • FSM con stessa struttura       │
│ • Unità Funzionali (+, -, <, !=)  │ │ • Sostituisce azioni/condizioni   │
│ • Multiplexer (sorgenti multiple) │ │   con segnali di Controllo/Status │
└───────────────────────────────────┘ └───────────────────────────────────┘
```

1. **Specificazione dell'Algoritmo**: L'elaborazione viene descritta in pseudo-codice definendo le variabili (es. \(x, y\)) e la struttura di controllo (cicli `while`, ramificazioni `if-else`).
2. **Conversione in FSMD (Finite-State Machine with Datapath)**: L'algoritmo viene tradotto in una macchina a stati estesa mediante **template di conversione** standard:
    - _Assegnamento (\(a = b\))_: Viene associato a uno stato con la relativa transizione allo stato successivo.
    - _Ciclo (`while (cond) { ... }`)_: Viene convertito in un nodo di decisione che valuta la condizione e indirizza il flusso verso il corpo del ciclo o verso l'uscita.
    - _Biforcazione (`if-else`)_: Viene mappata in stati con transizioni condizionate dalle espressioni booleane.
3. **Sintesi del Datapath**:
    - Si istanzia un **registro** per ciascuna variabile dichiarata nel programma (es. registri per \(x, y\) e per il dato di uscita \(d\)).
    - Si istanzia un'**unità funzionale** per ciascuna operazione aritmetica o relazionale presente nell'algoritmo (es. sottrattori per \(y-x\) e \(x-y\), comparatori per \(x \neq y\) e \(x < y\)).
    - Si collegano registri e unità funzionali inserendo **multiplexer** laddove un registro debba ricevere dati da sorgenti multiple (es. valore iniziale esterno \(x_i\) oppure il risultato di \(x-y\)).
    - Si definiscono i **segnali di controllo** inviati al datapath (es. \(x_sel, y_sel, x_ld, y_ld\)) e i **segnali di status** generati dal datapath verso il controller (es. \(x_neq_y, x_lt_y\)).
4. **Sintesi del Controller (FSM)**:
    - Conserva la medesima struttura di stati e transizioni della FSMD.
    - Le azioni complesse nei singoli stati vengono sostituite dall'attivazione dei corrispondenti **segnali di controllo** per il datapath.
    - Le condizioni sulle transizioni vengono sostituite dai **segnali di status** prodotti dalle unità relazionali del datapath e dai segnali di controllo esterni (es. \(go_i\)).
    - La FSM viene infine formalizzata nella **tabella degli stati del controller**, da cui si ricava la logica combinatoria per il calcolo dello stato futuro e delle uscite di controllo.

- **Progettazione RT diretta**: Quando la temporizzazione al singolo ciclo di clock è vincolante per il corretto funzionamento, si prescinde dal codice e si parte direttamente dalla FSMD. Un esempio è il **Bus Bridge** per la conversione di due ingressi a 4-bit ricevuti in sequenza (`data_in` con protocollo di handshake `rdy_in`) in un'unica uscita a 8-bit (`data_out` con segnale `rdy_out`).

---

### 4. Tecniche di Ottimizzazione Hardware (Algoritmo, FSMD, Datapath e FSM)

L'**ottimizzazione** ha lo scopo di migliorare i valori delle metriche di progetto (area, velocità, consumo energetico, costo NRE) intervenendo su quattro distinti livelli di astrazione:

1. **Ottimizzazione del Programma/Algoritmo Originale**:
    - Si analizza la complessità spaziale e temporale e la tipologia di operazioni. Operazioni come la moltiplicazione e la divisione sono molto onerose in termini di silicio e ritardo.
    - _Esempio nel GCD_: Sostituendo la serie di sottrazioni successive con l'operatore **modulo (\(%\))**, il calcolo di \(\text{GCD}(42, 8)\) passa da **9 iterazioni** di ciclo nell'algoritmo originale a sole **3 iterazioni** nell'algoritmo ottimizzato, riducendo drasticamente il tempo di esecuzione complessivo.
2. **Ottimizzazione della FSMD**:
    - **Eliminazione e fusione degli stati (merge states)**: Si eliminano gli stati i cui passaggi dipendono da condizioni costanti e si fondono gli stati che eseguono operazioni tra loro indipendenti. Nel caso del GCD, è possibile eliminare gli stati di join intermedi (es. \(1\text{-}J, 5\text{-}J, 6\text{-}J\)) e fondere le operazioni di assegnamento in un unico ciclo di clock.
    - **Separazione degli stati (separate states)**: Operazioni matematiche complesse (es. \(a \cdot b \cdot c \cdot d\)) vengono ripartite su più stati in cicli distinti per evitare il prolungamento del periodo di clock o l'impiego di hardware troppo grande.
3. **Ottimizzazione del Datapath**:
    - **Condivisione delle unità funzionali (Functional unit sharing)**: Operazioni identiche presenti in stati **distinti** e mutuamente esclusivi della FSM non richiedono un'unità fisica separata ciascuna, ma possono condividere un'unica unità hardware mediante l'uso di multiplexer d'ingresso.
    - **Uso di unità multifunzione**: Utilizzo di **ALU** programmabili per eseguire operazioni differenti negli stati della FSM in cui vengono richieste.
4. **Ottimizzazione della FSM (Controller)**:
    - **Codifica degli stati (state encoding)**: Scelta dell'assegnamento di bit agli stati (es. Binary, Gray, One-Hot) per minimizzare la dimensione del registro di stato e la complessità della logica combinatoria di transizione.
    - **Minimizzazione degli stati (state minimization)**: Unione di stati equivalenti che, a parità di ingressi, producono le medesime uscite e le stesse transizioni verso lo stato successivo.

---

### 5. Complessità della Progettazione Hardware vs. Software e Diagramma a Y (Gajski-Kuhn)

Per inquadrare la complessità dei sistemi digitali si utilizza la **Y-Chart di Gajski-Kuhn**, che schematizza lo spazio di progettazione su tre domini fondamentali:

- **Dominio Comportamentale (Behavioral Domain)**: Rappresenta la funzione dell'algoritmo (algoritmi, trasferimenti tra registri, equazioni booleane).
- **Dominio Strutturale (Structural Domain)**: Descrive i blocchi fisici e le loro interconnessioni (processori, ALU, registri, porte logiche, transistor).
- **Dominio Fisico (Physical Domain)**: Descrive la realizzazione geometrica e topologica sul silicio (layout dei transistor, celle, floorplan, partizioni fisiche).

#### Perché la Progettazione Hardware è intrinsecamente più complessa della Progettazione Software?

1. **Presenza di vincoli strutturali e fisici**: Nella progettazione **Hardware**, ogni specifica nel dominio comportamentale impone al progettista di affrontare i problemi di **sintesi**, **mapping** e la gestione delle interconnessioni fisiche nei domini strutturali e geometrici sottostanti. Non è possibile prescindere dai livelli di astrazione inferiori.
2. **Architettura fissa nel Software**: Nella progettazione **Software**, fissato il linguaggio di programmazione, l'architettura fisica del processore (**GPP** o **ASIP**) è già realizzata. Il compilatore traduce il codice per un target fisso senza dover riprogettare il datapath o le interconnessioni dell'hardware.
3. **Maturità delle tecniche di riuso**: La riutilizzabilità del codice (librerie, software di sistema, sistemi operativi) è ampiamente consolidata nel software, mentre nel dominio hardware il riuso di **IP Core** presenta maggiori complessità di integrazione e verifica.

---

## Domande di Autovalutazione

1. **Quali sono le fasi principali per convertire un algoritmo espresso in pseudo-codice in un processore custom single-purpose suddiviso in Controller e Datapath?**  
    _Guida alla risposta:_ Descrivere il passaggio dall'algoritmo alla FSMD tramite template, l'allocazione dei registri, delle unità funzionali e dei multiplexer nel Datapath, e la successiva estrazione del Controller (FSM) con la definizione dei segnali di controllo e di status.
    
2. **Qual è la differenza tra l'ottimizzazione dell'algoritmo, l'ottimizzazione della FSMD e l'ottimizzazione del Datapath mediante condivisione delle unità funzionali?**  
    _Guida alla risposta:_ Spiegare che l'ottimizzazione algoritmica riduce la complessità computazionale (es. modulo vs sottrazioni nel GCD); l'ottimizzazione FSMD unisce o separa gli stati per migliorare il parallelismo o ridurre il ritardo; l'ottimizzazione del datapath permette a operazioni in stati distinti della FSM di condividere la stessa unità fisica via multiplexer per risparmiare area.
    
3. **In base al diagramma a Y di Gajski-Kuhn, quali motivi rendono la progettazione di un circuito hardware più complessa rispetto allo sviluppo di software su un processore esistente?**  
    _Guida alla risposta:_ Evidenziare la necessità nell'HW di gestire contemporaneamente i domini comportamentale, strutturale e fisico (sintesi, layout, interconnessioni), mentre nel SW l'architettura del processore è fissa e il compilatore gestisce il mapping su istruzioni senza dover modificare la struttura fisica del chip.
    
4. **Quali sono i principali trade-off di un Custom Single-Purpose Processor rispetto a un General-Purpose Processor in termini di metriche di progetto?**  
    _Guida alla risposta:_ Confrontare l'elevata velocità, la ridotta area e il basso consumo del circuito custom con i suoi svantaggi: elevati costi NRE, time-to-market prolungato e flessibilità nulla rispetto alla riprogrammabilità del GPP.
    

---
