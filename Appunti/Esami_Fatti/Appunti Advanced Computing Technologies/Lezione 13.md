### 1. Il Concetto di ILP e l'Illusione della Crescita Infinita

Il Parallelismo a Livello di Istruzioni (ILP) consiste nella potenziale sovrapposizione dell'esecuzione di istruzioni indipendenti per massimizzare le prestazioni. L'hardware estrae l'ILP approfondendo la pipeline (più stadi) o inviando in esecuzione più istruzioni nello stesso ciclo di clock tramite la **parallelizzazione dinamica o statica dell'esecuzione (architetture superscalari e VLIW)**.

Tuttavia, estrarre ILP in quantità illimitate è impossibile. La **Legge di Amdahl** (o dei rendimenti decrescenti) ci insegna che, migliorando e parallelizzando enormemente una singola fase dell'esecuzione del programma, inevitabilmente si fa emergere un'altra parte del sistema come nuovo e insormontabile collo di bottiglia.

### 2. Il Muro del Controllo (Branching e Speculazione)

Per mantenere piene e attive le complesse pipeline moderne, il processore è obbligato a decodificare e avviare istruzioni che si trovano molto dopo i salti condizionati, ancor prima che si sappia se i salti verranno presi o meno. Questo avviene tramite la **speculazione** e l'utilizzo di aggressivi predittori dei salti (Branch Prediction).

- **Il problema intrinseco:** I salti condizionati complessi sono difficili da predire in modo perfetto.
- **L'impatto sulle prestazioni:** Se la predizione hardware si rivela errata, il processore deve scartare tutto il lavoro speculativo (eseguendo un _flush_ o svuotamento della pipeline) e ricaricare lo stato corretto, causando stalli critici. Ad esempio, nel processore ad altissime prestazioni Intel Core i7, sbagliare una singola predizione comporta una severa **penalità di circa 17 cicli di clock persi**.
- _(Conoscenza esterna): L'insieme di questi fattori impone un limite matematico alla cosiddetta "Instruction Window" (la finestra di istruzioni analizzabile). Estendere la speculazione su troppi rami di salto condizionato consecutivi fa crollare la probabilità di indovinarli tutti, rendendo controproducente l'estrazione di ulteriore ILP._

### 3. Il Muro della Memoria (Memory Wall)

Un calcolatore estremamente veloce nell'eseguire le istruzioni divora i dati a un ritmo molto superiore rispetto alla velocità con cui la memoria può fornirglieli. Le perdite che scaturiscono dall'imperfezione della **gerarchia delle memorie** (le "miss" nelle cache) limitano pesantemente la capacità del processore di mantenere la pipeline costantemente piena.

- Nei processori con esecuzione fuori ordine, l'hardware cerca di eseguire altre istruzioni non dipendenti mentre attende i dati mancanti, ma se le miss (specialmente nel livello L2 o L3 della cache) costringono ad accedere alla memoria principale, il processore incorrerà in ritardi gravissimi, costringendolo allo **stallo totale**.
- _(Conoscenza esterna): A causa delle centinaia di cicli di clock persi durante un miss nella memoria DRAM, strutture hardware preziose come il Reorder Buffer si riempiono fino al limite di istruzioni bloccate, impedendo il fetch di nuove istruzioni._

### 4. Dipendenze Vere sui Dati (RAW) e Lunghe Latenze

Mentre le **false dipendenze** o dipendenze nominali (WAW e WAR) possono essere eliminate brillantemente dall'hardware tramite il meccanismo della **Ridenominazione dei Registri (Register Renaming)**, il processore non può violare le leggi della causalità dei dati.

- **Le lunghe dipendenze di esecuzione:** Sono tipicamente causate da istruzioni che richiedono molto tempo per essere calcolate dalle Unità Funzionali (come divisioni in virgola mobile). Qualsiasi operazione matematica successiva che dipenda realmente (dipendenza vera - RAW) dal risultato di quell'istruzione lenta dovrà obbligatoriamente aspettare.

### 5. Il Cambio di Paradigma: Dal singolo Thread ai Multicore

_(Conoscenza esterna integrata con i capitoli finali del testo)_: Oltre un certo limite, investire milioni di transistor per estrarre minime frazioni aggiuntive di ILP da un singolo flusso di programma ha smesso di essere vantaggioso dal punto di vista dell'efficienza e del consumo energetico (il cosiddetto "Power Wall"). Per questo motivo, l'ingegneria dei calcolatori ha compiuto una radicale metamorfosi: ha smesso di concentrarsi solo sull'ILP, introducendo architetture in grado di gestire un **parallelismo esplicito**. Oggi si predilige estrarre:

- **Thread-Level Parallelism (TLP):** Creando microprocessori **multicore** o processori dotati di multithreading hardware simultaneo, permettendo al processore di eseguire veri e propri processi distinti in parallelo.
- **Data-Level Parallelism (DLP):** Evolvendo le architetture vettoriali e impiegando massicciamente le GPU (Graphics Processing Units), che riescono a compiere calcoli paralleli operando vettorialmente su vasti flussi di dati in maniera simultanea ed enormemente più efficiente.