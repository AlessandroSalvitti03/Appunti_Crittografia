*PDF di riferimento: 01_IC_Technologies.pdf*

**Panoramica della Lezione:**  
Questa lezione analizza le **tecnologie dei circuiti integrati (IC)** che costituiscono il supporto fisico dei sistemi embedded. Viene illustrato il **processo planare** e la fabbricazione del silicio, per poi approfondire la tassonomia delle implementazioni hardware: dalle soluzioni custom su misura (**ASIC**: **Full-Custom**, **Standard Cell**, **Gate Array**) ai dispositivi programmabili e riconfigurabili sul campo (**PLD**, **CPLD** e **FPGA**). Infine, si esaminano le architetture interne delle **FPGA** e la matrice dei **trade-off** tra costo, prestazioni, flessibilità e tempi di sviluppo.

---

## Concetti Chiave (Indice)

- **Il Processo Planare e la Fabbricazione dei Circuiti Integrati (IC)**
- **Tecnologie ASIC: Full-Custom, Standard Cell e Gate Array / Sea of Gates**
- **Modalità di Programmazione e Tipologie di Interconnessione Hardware**
- **Dispositivi Logici Programmabili a 2 e Multi-Livello: ROM, PLA, PAL, GAL e CPLD**
- **Field Programmable Gate Arrays (FPGA): Architetture, Logica LUT/MUX e Confronto Tecnologico**

---

## Appunti Dettagliati

### 1. Il Processo Planare e la Fabbricazione dei Circuiti Integrati (IC)

Tutti i componenti fisici dei moderni sistemi embedded sono realizzati sotto forma di **circuiti integrati (IC)**, tecnologia affermatasi a partire dalla fine degli anni '50 con l'invenzione del **processo planare**. Il processo planare consente di fabbricare contemporaneamente milioni o miliardi di componenti elementari (transistor, diodi, resistori, condensatori) su un unico substrato di materiale **semiconduttore** (tipicamente silicio, o storicamente germanio e arseniuro di gallio).

Il processo chimico-fisico si articola nei seguenti aspetti fondamentali:

- **Doping del Silicio**: Modifica delle proprietà elettromagnetiche del silicio puro mediante **diffusione** o **implantazione ionica** per creare **silicio tipo-n** (arricchito di elettroni di conduzione) e **silicio tipo-p** (arricchito di lacune).
- **Isolamento e Conduzione**: Creazione di strati isolanti in **biossido di silicio (\(SiO_2\))** per ossidazione e deposizione di strati metallici conduttori (alluminio o rame) per la realizzazione delle interconnessioni interne.
- **Fotolitografia e Mascheramento (Masking)**: Il processo si sviluppa attraverso una sequenza ripetuta di fasi di stesura di resina fotosensibile (**photoresist**), esposizione a radiazione tramite **maschere (masks)**, attacco chimico (**etching**) e lavaggio. Il grande vantaggio economico risiede nel fatto che ogni singola fase del processo lavora contemporaneamente su tutte le zone dell'intero **silicon wafer**.

Dal wafer vengono intagliati i singoli **die** che, una volta incapsulati nel **package**, costituiscono il **chip** finale. La presenza di difetti di fabbricazione determina la percentuale di chip funzionanti (**yield**). Tra i trend evolutivi più recenti si colloca la transizione dal processo planare 2D alle strutture **3D VLSI**, con l'integrazione di **Chiplets** e **Multi-die** interconnessi tramite **Through-Silicon Vias (TSV)** e **Interposer** per superare i limiti fisici del silicio.

---

### 2. Tecnologie ASIC: Full-Custom, Standard Cell e Gate Array / Sea of Gates

I circuiti integrati per applicazioni specifiche (**ASIC - Application-Specific Integrated Circuit**) si dividono in tre metodologie realizzative a seconda del grado di libertà concesso al progettista e della regolarità delle strutture:

1. **Full-Custom (VLSI Custom)**:
    
    - **Caratteristiche**: La geometria, la forma, la dimensione dei transistor (**sizing**) e la topologia dei collegamenti vengono progettate manualmente a livello di layout.
    - **Vantaggi**: Massima **densità di integrazione**, frequenze di clock elevate (**prestazioni** massime), ottimizzazione d'area e minimizzazione dei consumi; è l'unica tecnologia che consente l'integrazione di **circuiti analogici**.
    - **Svantaggi**: **Costo NRE (Non-Recurring Engineering)** sproporzionatamente alto, strumenti EDA estremamente complessi e **time-to-market** molto lungo.
2. **Standard Cell**:
    
    - **Caratteristiche**: Il circuito viene sintetizzato posizionando celle pre-progettate e modellate all'interno di **librerie tecnologiche** fornite dai produttori di silicio (contenenti da 500 a 2000 tipi di celle: porte logiche, flip-flop, sommatori, memorie). Le celle hanno **altezza standardizzata** e vengono disposte in file parallele.
    - **Routing**: Il cablaggio avviene nei canali di **routing** posizionati tra le file di celle mediante geometrie ortogonali (**Manhattan geometry**).
    - **Trade-off**: Aumenta il livello di astrazione riducendo i gradi di libertà rispetto al full-custom, consentendo di ridurre i costi di progettazione per circuiti medio-grandi preservando buone prestazioni.
3. **Gate Array / Mask Programmable Gate Array (MPGA)**:
    
    - **Caratteristiche**: Tutti i transistor sono pre-fabbricati sul silicio in posizioni fisse, lasciando i terminali scollegati. Il progettista deve unicamente definire la funzione personalizzando gli ultimi livelli metallici di interconnessione tramite **maschere di metallizzazione**.
    - **Varianti**: Architetture a canali (**Channel-based**) oppure a matrice continua di transistor senza canali dedicati (**Sea of Gates**).
    - **Trade-off**: Riduce drasticamente il numero di maschere custom da produrre (abbassando il costo NRE e velocizzando la fabbricazione) al prezzo di una minore densità di logica utile e prestazioni inferiori rispetto alle Standard Cell.

---

### 3. Modalità di Programmazione e Tipologie di Interconnessione Hardware

I dispositivi programmabili offrono risorse logiche e reti di interconnessione riconfigurabili. Si classificano principalmente in base alla tecnologia con cui vengono fissate le connessioni e alla struttura delle reti:

#### A. Tecnologie di Programmazione

- **One-Time Programmable (OTP)**:
    - **Fuse**: Le connessioni sono normalmente chiuse all'uscita dalla fabbrica; la programmazione consiste nel "bruciare" in modo irreversibile le connessioni superflue applicando una tensione superiore a quella operativa.
    - **Antifuse**: Le reti sono nativamente aperte (isolate da sottili strati di \(SiO_2\)); la programmazione applica un'alta tensione per perforare l'isolante e creare una giunzione conduttiva permanente.
- **Riprogrammabili (Reprogrammable)**:
    - **\(E^2PROM\) / Flash**: Dispositivi non volatili che utilizzano un **floating gate** per intrappolare carica elettrica e controllare la conduzione del canale del transistor. Possono essere riprogrammati elettricamente in modo non distruttivo (Flash consente la riprogrammazione in-circuit).
- **Riprogrammabili e Riconfigurabili (SRAM-based)**:
    - Le connessioni sono controllate dallo stato (0/1) di celle di memoria **SRAM**. La tecnologia è **volatile** (richiede una memoria esterna di boot all'avvio), ma permette la riconfigurazione sia a dispositivo non operativo sia in tempo reale durante l'esecuzione (**dynamic/on-line reconfiguration**).

#### B. Gerarchia delle Interconnessioni

- **Connessioni Globali**: Linee lunghe che attraversano l'intero chip condivise da molti moduli. Presentano ritardi elevati e bassa flessibilità.
- **Connessioni Locali e Distribuite**: Linee brevi dedicate al collegamento di blocchi adiacenti. Riducono i ritardi di propagazione e la potenza dissipata, ma richiedono matrici di commutazione complesse (**Switch Matrix**).

---

### 4. Dispositivi Logici Programmabili a 2 e Multi-Livello: ROM, PLA, PAL, GAL e CPLD

Per sintetizzare funzioni booleane in forma di somma di prodotti (**Sum of Products - SOP**), l'evoluzione dei dispositivi **PLD (Programmable Logic Devices)** ha introdotto diverse architetture a livelli:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           TASSONOMIA DEI PLD 2L                            │
└──────────────────────────────────────┬──────────────────────────────────────┘
                                       │
         ┌─────────────────────────────┼─────────────────────────────┐
         ▼                             ▼                             ▼
┌──────────────────┐          ┌──────────────────┐          ┌──────────────────┐
│     ROM/PROM     │          │    PLA (Array)   │          │    PAL (Logic)   │
├──────────────────┤          ├──────────────────┤          ├──────────────────┤
│ • Piano AND Fisso│          │ • Piano AND Prog.│          │ • Piano AND Prog.│
│   (Decodificatore│          │ • Piano OR Prog. │          │ • Piano OR Fisso │
│   mintermini $2^n$)│        │                  │          │                  │
│ • Piano OR Prog. │          │ Max Flessibilità │          │ Minimo Ritardo / │
└──────────────────┘          └──────────────────┘          │ Struttura Semplice│
                                                            └──────────────────┘
```

#### A. PLD a 2 Livelli (2L-PLD)

Consistono in un **piano AND** (per la generazione dei mintermini o prodotti) e un **piano OR** (per la somma logica):

- **ROM / PROM**: **Piano AND fisso** (realizzato come un decodificatore d'indirizzi completo che genera tutti i \(2^n\) mintermini) e **piano OR programmabile**.
- **PLA (Programmable Logic Array)**: **Piano AND programmabile** (genera solo i mintermini strettamente necessari all'algoritmo) e **piano OR programmabile**. Offre la massima flessibilità di condivisione dei prodotti.
- **PAL (Programmable Array Logic)**: **Piano AND programmabile** e **piano OR fisso**. Riduce la complessità del chip e incrementa la velocità, imponendo tuttavia un vincolo rigido al numero massimo di termini di prodotto sommabili su ciascuna uscita.

#### B. PLD Multi-Livello, GAL e CPLD

Per superare l'incapacità dei PLD a 2 livelli di sintetizzare funzioni complesse o macchine a stati finiti (FSM), sono stati introdotti re-instradamenti in retroazione (**feedback loops**) ed elementi di memoria (**Flip-Flop**):

- **GAL (Generic Array Logic)**: Evoluzione delle PAL/PLA dotata di celle di uscita riconfigurabili formate da **OLMC (Output Logic Macro Cell)**, abilitando la selezione via multiplexer tra uscite combinatorie o registrate (sincrone).
- **CPLD (Complex Programmable Logic Devices)**: Strutture ad alta integrazione costituite da molteplici blocchi logici tipo-PAL interconnessi da una **matrice di commutazione centrale ad interconnessione globale**. Adatte per logiche di controllo concentrate ad alte prestazioni.

---

### 5. Field Programmable Gate Arrays (FPGA): Architetture, Logica LUT/MUX e Confronto Tecnologico

Le **FPGA (Field Programmable Gate Arrays)** rappresentano l'apice dei dispositivi riconfigurabili ad altissima densità. A differenza delle CPLD, le FPGA adottano una logica distribuita basata su una matrice bidimensionale di **blocchi logici programmabili (CLB o Logic Modules)** immersi in una rete di interconnessione locale e gerarchica.

#### A. Componenti e Architetture FPGA

- **Blocchi Logici (CLB / LM)**: L'unità di calcolo elementare può essere realizzata mediante:
    1. **Look-Up Table (LUT)**: Una piccola memoria SRAM (o un MUX \(2^n \to 1\) dove gli ingressi selezionano il valore memorizzato) in grado di sintetizzare qualsiasi funzione booleana arbitraria a \(n\) ingressi.
    2. **Multiplexer-Based**: Reti di multiplexer in grado di realizzare porte logiche fondamentali (AND, OR, XOR, NAND) configurando opportunamente i segnali d'ingresso.
    3. **Celle Combinatorie e Sequenziali**: Integrazione di LUT/MUX con elementi di memorizzazione (**Flip-Flop D**) e multiplexer programmabili di bypass.
- **Architetture Generali**: Vanno dalle matrici simmetriche (**Symmetric Array** tipiche di Xilinx) alle strutture a file (**Row-based** di Actel/Microsemi) e a PLD gerarchici (**Hierarchical PLD** di Altera/Intel).

#### B. Trade-off e Matrice Comparativa delle Tecnologie IC

La scelta della tecnologia IC per un sistema embedded è guidata da precisi compromessi tra le metriche di progetto:

|Metrica di Progetto|FPGA|Gate Array (MPGA)|Standard Cell|Full-Custom (VLSI)|
|:--|:-:|:-:|:-:|:-:|
|**Densità Logica**|Bassa|Media|Media-Alta|Massima|
|**Flessibilità Hardware**|Alta (Riconfigurabile)|Bassa|Media (Solo in fase HW)|Massima (In fase di design)|
|**Supporto Analogico**|No|No|No|**Sì**|
|**Prestazioni (Clock/Speed)**|Basse-Medie|Medie|Elevate|**Massime**|
|**Costo NRE / Design Cost**|**Minimo / Nullo**|Medio|Medio-Alto|Massimale|
|**Unit Cost (Singolo Chip)**|Alto|Medio|Basso (su alti volumi)|**Minimo** (su altissimi volumi)|
|**Time-to-Market / Design Time**|**Immediato / Minimo**|Medio|Medio-Lungo|Molto Lungo|
|**Volume Economico Ottimale**|Bassi-Medi volumi / Prototipi|Medi volumi|Alti volumi|Elevatissimi volumi (Miliardi)|

- **Trade-off Fondamentale FPGA vs. ASIC**: Le FPGA eliminano i costi NRE di fonderia e azzerano i tempi di fabbricazione fisica, risultando ideali per la **prototipazione rapida (fast prototyping)**, la verifica hardware e la produzione a volumi ridotti. Di contro, per la produzione di massa su altissima scala, un ASIC (Standard Cell o Full Custom) garantisce un **costo unitario**, un **consumo energetico** e una **dimensione fisica** nettamente inferiori.

---

## Domande di Autovalutazione

1. **Quali sono le differenze strutturali tra Full-Custom, Standard Cell e Gate Array, e come impattano sul costo NRE e sul Time-to-Market?**  
    _Guida alla risposta:_ Illustrare che il Full-Custom richiede la progettazione di tutte le maschere da zero (massimo NRE e TTM); le Standard Cell utilizzano celle di libreria ad altezza fissa disposte su file (NRE e TTM medi); i Gate Array richiedono solo la personalizzazione delle maschere metalliche superiori per connettere transistor prefabbricati (NRE ridotto e TTM rapido).
    
2. **Come si differenziano la ROM, la PLA e la PAL nella struttura dei loro piani logici AND e OR?**  
    _Guida alla risposta:_ Evidenziare che la ROM ha un piano AND fisso (decodificatore) e un piano OR programmabile; la PLA ha sia il piano AND sia il piano OR programmabili (massima flessibilità); la PAL ha un piano AND programmabile e un piano OR fisso (struttura più semplice e veloci prestazioni).
    
3. **In che modo una Look-Up Table (LUT) a \(n\) ingressi basata su MUX o SRAM riesce a sintetizzare una qualsiasi funzione booleana combinatoria?**  
    _Guida alla risposta:_ Spiegare che una LUT ad \(n\) ingressi contiene \(2^n\) bit di configurazione (celle SRAM) corrispondenti alla tabella della verità della funzione; la combinazione delle \(n\) variabili d'ingresso agisce sui segnali di selezione di un multiplexer \(2^n \to 1\) aprendo il percorso verso il valore booleano corrispondente.
    
4. **Analizzando la matrice dei trade-off delle tecnologie IC, in quali scenari operativi ed economici è preferibile utilizzare una FPGA rispetto a un chip ASIC Standard Cell?**  
    _Guida alla risposta:_ L'FPGA è vantaggiosa per l'assenza di costi NRE, per la riprogrammabilità in-circuit, per la prototipazione rapida e per bassi volumi di produzione. L'ASIC Standard Cell diventa conveniente su volumi di scala elevati, grazie al basso costo unitario per chip, alla maggiore densità, alle superiori prestazioni di clock e al minor consumo energetico.
    

---
