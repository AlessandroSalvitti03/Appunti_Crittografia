*PDF di riferimento: 01_Introduction.pdf*

**Panoramica della Lezione:**  
Questa lezione introduce i concetti fondamentali dei **sistemi embedded**, analizzando le caratteristiche distintive che li differenziano dai sistemi di elaborazione general-purpose. Vengono esaminate le principali **metriche di progetto** (tra cui costo, prestazioni, consumi e _time-to-market_), e presentate le tre tecnologie chiave per la loro realizzazione: **tecnologie dei processori**, **tecnologie degli IC** e **tecnologie di progettazione**.

---

## Concetti Chiave (Indice)

- **Definizione e Caratteristiche dei Sistemi Embedded**
- **La Sfida Progettuale: Ottimizzazione delle Metriche e Time-to-Market**
- **Tecnologie dei Processori: GPP, SPP e ASIP**
- **Tecnologie degli Circuiti Integrati (IC): Full-Custom, Semi-Custom e PLD/FPGA**
- **Tecnologie di Progettazione: Legge di Moore, Productivity Gap e Co-Design Ladder**

---

## Appunti Dettagliati

### 1. Definizione e Caratteristiche dei Sistemi Embedded

Un **sistema embedded** (o sistema integrato) è un sistema di elaborazione digitale incorporato all'interno di un dispositivo più ampio per svolgere funzioni specifiche di controllo o elaborazione dati. A differenza dei tradizionali computer desktop o server (progettati per un utilizzo generico), i sistemi embedded sono prodotti in miliardi di unità all'anno e si trovano in quasi tutti i settori tecnologici, dall'automotive agli elettrodomestici, dai dispositivi medici all'elettronica di consumo.

Le caratteristiche architetturali e operative fondamentali includono:

- **Single-functioned**: Il sistema esegue ripetutamente un singolo programma o un insieme limitato di funzioni predefinite. Anche nei sistemi embedded ad alte prestazioni (es. **edge computing**), la funzione principale rimane specializzata.
- **Tightly-constrained**: Presenza di vincoli progettuali ed economici estremamente rigidi, come basso **costo unitario**, ridotto **consumo energetico**, dimensioni fisiche contenute e tempi di risposta elevati.
- **Reactive and real-time**: Il sistema deve reagire continuamente agli stimoli provenienti dall'ambiente esterno tramite sensori e attuatori, fornendo risposte entro **scadenze temporali (deadlines)** ben definite senza ritardi.

Un esempio emblematico è la **fotocamera digitale**, in cui convivono un microcontrollore per la logica di controllo generale, coprocessori e acceleratori hardware dedicati (es. **JPEG codec**, **pixel coprocessor**, **CCD preprocessor**) e componenti di interfaccia (UART, display controller, bus ISA). I sistemi embedded consentono inoltre di arricchire prodotti tradizionali con servizi innovativi (es. spazzolini da denti connessi, bicchieri da birra smart con **sensori capacitivi** e processori a 8-bit per il monitoraggio dei fluidi, anelli **NFC** e pacemaker ultra-compatti).

---
### 2. La Sfida Progettuale: Ottimizzazione delle Metriche e Time-to-Market

Il compito primario del progettista è soddisfare i **Requisiti Funzionali (FR)** — ossia _cosa_ il sistema deve fare — rispettando contemporaneamente i **Requisiti Non-Funzionali (NFR)** o vincoli extra-funzionali. L'ottimizzazione concorrente delle **metriche di progetto** (caratteristiche misurabili dell'implementazione) costituisce il principale fattore di complessità.

Le metriche di progetto fondamentali sono:

1. **NRE Cost (Non-Recurring Engineering cost)**: Il costo monetario _una tantum_ richiesto per la progettazione e lo sviluppo iniziale del sistema.
2. **Unit Cost**: Il costo monetario di fabbricazione di ciascuna singola unità del prodotto, escluso il costo NRE.
3. **Size / Area**: Lo spazio fisico occupato dal chip o la percentuale di risorse hardware allocate (es. area di silicio o porte logiche).
4. **Performance**: La velocità di esecuzione, espressa in **throughput**, **latenza** o tempo di risposta.
5. **Power / Energy**: La potenza istantanea assorbita e l'energia totale consumata, critiche nei dispositivi a batteria.
6. **Flexibility**: La capacità di modificare o aggiornare le funzionalità del sistema senza dover sostenere un nuovo costo NRE proibitivo.

#### Modello Matematico del Time-to-Market

Un'ulteriore metrica critica è il **time-to-market** (il tempo necessario per passare dall'idea di prodotto alla commercializzazione). Entrare in ritardo sul mercato comporta una significativa perdita di ricavi totali (_sales volume loss_). Assumendo un modello semplificato di mercato a risposta triangolare con mezza durata di vita pari a \(W\) e un ritardo di ingresso pari a \(D\), la perdita di ricavo \(R_L\) rispetto al ricavo ottimale \(R_0\) è espressa dalla formula:

$$R_L = R_0 \frac{D(3W - D)}{2W^2}$$
#### Compromesso (Trade-off) Hardware/Software

Tutte le metriche di progetto competono tra loro: l'ottimizzazione di una metrica causa spesso il peggioramento di un'altra. Ad esempio, l'aggiunta di componenti hardware dedicati per aumentare le **prestazioni** o ridurre il **consumo energetico** incrementa l'**area** e il **costo NRE**, riducendo la **flessibilità**. Per questo motivo, la progettazione richiede competenze trasversali sia sul versante hardware che su quello software, superando la tradizionale separazione tra le due discipline.

---

### 3. Tecnologie dei Processori: GPP, SPP e ASIP

La **tecnologia del processore** riguarda l'architettura del motore di calcolo utilizzato per realizzare le funzionalità desiderate. Risponde alla domanda: _"Come viene eseguito il calcolo?"_.

I processori si dividono in tre grandi categorie:

```
                     ┌──────────────────────────────────────────┐
                     │          Tecnologie dei Processori       │
                     └────────────────────┬─────────────────────┘
                                          │
         ┌────────────────────────────────┼────────────────────────────────┐
         ▼                                ▼                                ▼
┌──────────────────┐            ┌──────────────────┐            ┌──────────────────┐
│ General-Purpose  │            │ Application-     │            │ Single-Purpose   │
│ Processor (GPP)  │            │ Specific (ASIP)  │            │ Processor (SPP)  │
└────────┬─────────┘            └────────┬─────────┘            └────────┬─────────┘
         │                                │                                │
  • Programmabile                • Programmabile                 • Non programmabile
  • ALU & Register File          • ALU personalizzata            • Solo la logica
    generici                       & Istruzioni dedicate           strettamente necessaria
  • Max Flessibilità             • Compromesso Flessibilità/     • Max Prestazioni/Efficienza
  • Basso NRE / Fast TTM           Prestazioni                   • Alto NRE / Min Flessibilità
```

1. **General-Purpose Processors (GPP / Software)**:
    
    - **Architettura**: Dispositivi programmabili (microprocessori) costituiti da una **memoria di programma**, un **datapath generico** con ampio **register file** e una **ALU generica**.
    - **Vantaggi**: Minimo **costo NRE**, tempo di sviluppo ridotto e massima **flessibilità** (le modifiche avvengono riprogrammando il software).
    - **Svantaggi**: Prestazioni inferiori e consumo energetico più elevato rispetto alle soluzioni hardware dedicate.
2. **Single-Purpose Processors (SPP / Hardware)**:
    
    - **Architettura**: Circuiti digitali progettati per eseguire un'unica ed esclusiva funzione. Contengono solo i registri, la logica di controllo e le unità aritmetiche necessarie; sono del tutto privi di **memoria di programma**.
    - **Vantaggi**: Massima velocità di esecuzione, dimensioni ridotte ed elevata **efficienza energetica**.
    - **Svantaggi**: Assenza totale di flessibilità (hardware statico) e elevati costi NRE per la progettazione custom.
3. **Application-Specific Instruction-Set Processors (ASIP)**:
    
    - **Architettura**: Processori programmabili le cui istruzioni e il cui datapath sono ottimizzati per una specifica classe di applicazioni con caratteristiche comuni (es. **DSP** o acceleratori multimediali).
    - **Trade-off HW/SW**: Rappresentano il punto d'incontro ideale, offrendo un'ottima efficienza e buone prestazioni mantenendo un grado di programmabilità e flessibilità per quella specifica famiglia di algoritmi.

---

### 4. Tecnologie degli Circuiti Integrati (IC): Full-Custom, Semi-Custom e PLD/FPGA

La **tecnologia IC** (Integrated Circuit) descrive la modalità fisica con cui l'architettura digitale viene mappata ed elaborata sul chip di silicio. Risponde alla domanda: _"Come viene realizzato fisicamente il circuito?"_.

È fondamentale evidenziare che **la tecnologia del processore e la tecnologia IC sono tra loro indipendenti**: ad esempio, un GPP o un SPP possono essere realizzati indistintamente su una FPGA o su un ASIC.

Esistono tre principali tipologie di tecnologia IC:

1. **Full-Custom / VLSI**:
    
    - **Caratteristiche**: Tutti i layer del circuito integrato (fino a 10 o più livelli di maschere) vengono progettati e ottimizzati ad-hoc (posizionamento dei transistor, **sizing** delle porte e **routing** delle interconnessioni).
    - **Vantaggi**: Prestazioni ottimali, consumo energetico minimo e area di silicio ridotta al minimo.
    - **Svantaggi**: Costo NRE elevatissimo e **time-to-market** molto lungo. Economicamente vantaggioso solo per volumi di produzione enormi.
2. **Semi-Custom ASIC (Gate Array e Standard Cell)**:
    
    - **Caratteristiche**: I layer inferiori del silicio sono pre-fabbricati o strutturati mediante celle standard pre-validate; al progettista spetta il piazzamento e il routing dei livelli metallici superiori.
    - **Trade-off**: Fornisce buone prestazioni e un'area contenuta con un **costo NRE** e tempi di sviluppo significativamente minori rispetto al full-custom.
3. **Programmable Logic Devices (PLD / FPGA)**:
    
    - **Caratteristiche**: Tutti i layer fisici dell'IC sono pre-fabbricati dal costruttore. Le connessioni logiche interne vengono create o configurate direttamente dall'utente "sul campo" (Field-Programmable Gate Array).
    - **Vantaggi**: **Costo NRE** praticamente nullo, disponibilità immediata del silicio e riduzione drastica del **time-to-prototype**.
    - **Svantaggi**: Dispositivi più grandi, maggiore potenza dissipata, velocità inferiori e un **costo unitario** per singolo chip notevolmente più alto.

---

### 5. Tecnologie di Progettazione, Legge di Moore e la Co-Design Ladder

La **tecnologia di progettazione** definisce la metodologia e gli strumenti impiegati per trasformare una specifica funzionale di alto livello in un'implementazione fisica funzionante. La catena metodologica si avvale di strumenti **EDA (Electronic Design Automation)** strutturati su tre pilastri: **Sintesi/Compilazione** (automazione del passaggio tra livelli di astrazione), **Librerie/IP Cores** (riuso di blocchi HW/SW pre-progettati) e **Test/Verifica** (co-simulazione e convalida).

```
┌─────────────────────────────────────────────────────────────────────────┐
│                      ELECTRONIC SYSTEM LEVEL (ESL)                      │
└────────────────────────────────────┬────────────────────────────────────┘
                                     │
           Sintesi                 Istruzioni /              Simulazione /
          hardware                  Sintesi SW              Co-Simulazione
             │                           │                         │
             ▼                           ▼                         ▼
┌─────────────────────────┐ ┌─────────────────────────┐ ┌─────────────────┐
│ System Specification    │ │ High-Level Languages    │ │ Model Checkers  │
├─────────────────────────┤ ├─────────────────────────┤ ├─────────────────┤
│ Behavioral Spec.        │ │ C / C++ / VHDL          │ │ HW-SW Cosimul.  │
├─────────────────────────┤ ├─────────────────────────┤ ├─────────────────┤
│ RTL Specification       │ │ Assembly / Compilatori  │ │ HDL Simulators  │
├─────────────────────────┤ ├─────────────────────────┤ ├─────────────────┤
│ Logic / Gate Level      │ │ Machine Instructions    │ │ Gate Simulators │
└─────────────────────────┘ └─────────────────────────┘ └─────────────────┘
             │                           │                         │
             └───────────────────────────┼─────────────────────────┘
                                         ▼
                         ┌──────────────────────────────┐
                         │   Implementazione Fisica     │
                         │    (Microprocessore / ASIC)  │
                         └──────────────────────────────┘
```

#### Legge di Moore e il Design Productivity Gap

La pietra angolare della microelettronica è la **Legge di Moore**, enunciata nel 1965 da Gordon Moore: la densità di transistor all'interno di un circuito integrato raddoppia approssimativamente ogni 18 mesi.

Tuttavia, mentre la capacità di integrazione cresce esponenzialmente, la capacità dei progettisti di gestire ed elaborare tali transistor (_designer productivity_) cresce a un ritmo sensibilmente più lento. Questo fenomeno prende il nome di **Design Productivity Gap**.

A peggiorare la situazione interviene la legge nota come **The Mythical Man-Month** (Brooks 1975): l'aggiunta di ulteriori progettisti a un team di sviluppo riduce la produttività individuale a causa della complessità di comunicazione e coordinamento, fino a ritardare la consegna complessiva del progetto.

#### La Co-Design Ladder e la Platform-Based Design

Per colmare il gap di produttività, l'ingegneria dei sistemi embedded ha adottato una **prospettiva unificata tra Hardware e Software (Hardware/Software Co-Design)**, formalizzata nella **Co-Design Ladder**:

> **Principio Fondamentale del Co-Design:**  
> Non esiste alcuna differenza concettuale primaria tra ciò che può essere implementato in hardware o in software. La decisione di assegnare una determinata funzione all'hardware (microprocessore + blocchi VLSI/ASIC/FPGA) o al software (codice eseguito su un microprocessore) è un puro **compromesso (trade-off)** tra le metriche di progetto (prestazioni, potenza, area, costo NRE e flessibilità).

Per innalzare il livello di astrazione e favorire il riuso, si applica l'approccio **Platform-Based Design**. Questo metodo consiste nel definire o riutilizzare architetture di dominio (piattaforme virtuali o reali) costituite da blocchi HW/SW già validati e standardizzati (es. la piattaforma **AUTOSAR** nel settore automotive o **ARINC** nell'aeronautica).

---

## Domande di Autovalutazione

1. **Qual è la differenza concettuale tra una tecnologia del processore e una tecnologia IC, e in che modo sono indipendenti?**  
    _Guida alla risposta:_ La tecnologia del processore definisce l'architettura logica e la modalità di calcolo (GPP, SPP o ASIP). La tecnologia IC descrive la realizzazione fisica sul silicio (Full-Custom, Semi-Custom o PLD/FPGA). L'indipendenza deriva dal fatto che qualsiasi architettura logica può essere mappata su qualsiasi tecnologia di fabbricazione del silicio.
    
2. **In che modo il ritardo nel Time-to-Market impatta i ricavi economici di un prodotto embedded? Qual è la formula matematica usata per stimare la perdita di ricavo?**  
    _Guida alla risposta:_ Il ritardo \(D\) riduce la finestra temporale di vendita e la quota di picco del mercato. La perdita di ricavo è modellata dalla formula \(R_L = R_0 \frac{D(3W - D)}{2W^2}\), dimostrando che anche piccoli ritardi causano perdite finanziarie sproporzionate rispetto all'aumento dei costi NRE.
    
3. **Quali sono i principali trade-off coinvolti nella scelta di implementare una funzionalità in Hardware dedicato (SPP) rispetto al Software (GPP)?**  
    _Guida alla risposta:_ L'hardware dedicato (SPP) garantisce massime prestazioni, minima area e bassi consumi, ma comporta alti costi NRE e flessibilità nulla. Il software su GPP offre massima flessibilità, basso costo NRE e tempi di sviluppo rapidi, al prezzo di minori prestazioni e maggior consumo energetico.
    
4. **Che cos'è il Design Productivity Gap e in che modo l'approccio Platform-Based Design aiuta a superarlo?**  
    _Guida alla risposta:_ Il gap è il divario tra la crescita esponenziale della capacità dei chip (Legge di Moore) e la crescita più lenta della produttività dei progettisti. Il Platform-Based Design mitiga il problema riutilizzando architetture HW/SW di dominio pre-validate (es. AUTOSAR), spostando lo sforzo di progettazione ad alti livelli di astrazione.
    

---
