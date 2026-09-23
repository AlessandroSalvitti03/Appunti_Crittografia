Ecco degli appunti completi e dettagliati incentrati sui concetti generali dei sistemi NoSQL, escludendo la parte finale relativa a MongoDB.

### 1. Motivazioni e Limiti dei Database Relazionali

I database relazionali classici si basano su tabelle rigorose (righe e colonne), uno schema prefissato e la normalizzazione dei dati. Le loro prestazioni migliorano principalmente tramite la **scalabilità verticale** (potenziamento dell'hardware del singolo server) e seguono le rigorose **proprietà ACID** (Atomicità, Coerenza, Isolamento, Durabilità) per garantire transazioni affidabili.

Tuttavia, le architetture moderne hanno evidenziato la necessità di nuovi approcci per affrontare tre sfide principali:

- **Gestione dei Big Data (Le 3 V):** Necessità di elaborare un enorme _Volume_ di dati, generati ad altissima _Velocità_ e con grande _Varietà_ di formati (strutturati, semi-strutturati, non strutturati).
- **Disponibilità continua (Availability):** Garantire l'accesso continuo ai dati ed evitare tempi di inattività anche in caso di guasti hardware.
- **Scalabilità Orizzontale (Dynamic Scalability):** Aggiungere dinamicamente nuovi nodi al sistema per bilanciare i carichi di lavoro, invece di dover costantemente aggiornare un singolo super-computer.

### 2. Il Paradigma NoSQL ("Not Only SQL")

Nato alla fine degli anni '90, il movimento NoSQL ha introdotto database progettati per superare le rigidità dei database tradizionali. Il termine "Not Only SQL" indica che non si basano unicamente sul modello relazionale e sulle interrogazioni SQL tradizionali.

**Caratteristiche principali e Vantaggi dei database NoSQL:**

- **Schema flessibile (Schema-less):** Possono immagazzinare dati semi-strutturati o non strutturati, senza vincoli rigidi prefissati.
- **Scalabilità orizzontale ed elevata performance:** Operano nativamente su database distribuiti, garantendo costi inferiori e prestazioni alte tramite l'aggiunta di macchine (nodi).

**Svantaggi del NoSQL:** Mancanza di un linguaggio standardizzato universale, controllo dell'integrità dei dati più debole, parziale abbandono delle garanzie ACID tradizionali e complessità nelle operazioni di scrittura.

### 3. I Database Distribuiti e Il Teorema CAP

Nei database distribuiti entra in gioco un principio architetturale fondamentale: il **Teorema CAP di Brewer**. Questo teorema stabilisce che, in un sistema distribuito, è possibile garantire **al massimo due** delle seguenti tre proprietà contemporaneamente:

1. **Consistency (Coerenza):** Tutti i nodi della rete restituiscono esattamente la stessa visualizzazione dei dati nello stesso istante.
2. **Availability (Disponibilità):** Il sistema è sempre disponibile e ogni richiesta riceve una risposta, anche in caso di down di alcuni nodi.
3. **Partition Tolerance (Tolleranza alle partizioni):** Il sistema continua a funzionare nonostante interruzioni di rete o comunicazioni fallite tra i nodi.

**L'approccio Relazionale vs NoSQL in base al CAP:**

- **I database relazionali** optano generalmente per **CA (Consistency + Availability)**, utilizzando tecniche come il "Two-Phase Commit" (2PC). Questo assicura dati perfetti e senza disallineamenti, ma introduce un'alta latenza e fallisce malamente in scenari ampiamente distribuiti o con partizioni di rete.
- **I database NoSQL**, non usando il protocollo 2PC per evitare rallentamenti, scelgono spesso di dare priorità a **AP (Availability + Partition Tolerance)** o **CP (Consistency + Partition Tolerance)**. Per fare questo, scambiano la coerenza istantanea assoluta con un concetto di coerenza più debole.

**Classificazione pratica dei DBMS secondo il Teorema CAP:** 
* **Sistemi CA (Consistency + Availability):** I dati restano coerenti finché tutti i nodi sono disponibili. Se c'è una partizione di rete, il sistema non può funzionare correttamente. _Esempi: RDBMS tradizionali, Oracle Coherence, Vertica, Aster Data_. 
* **Sistemi CP (Consistency + Partition Tolerance):** Il sistema resta coerente e tollera le partizioni di rete. Tuttavia, in caso di guasto ai nodi (spesso in architetture master/slave), il sistema potrebbe negare l'accesso per preservare la coerenza, compromettendo la disponibilità. _Esempi: BigTable, Redis, e anche MongoDB_.
* **Sistemi AP (Availability + Partition Tolerance):** Il sistema risponde sempre e scala linearmente anche durante le partizioni di rete, a costo di restituire dati temporaneamente disallineati (inconsistenti). I conflitti vengono risolti tramite sincronizzazione una volta ripristinata la rete. _Esempi: Cassandra, CouchDB, Riak, DynamoDB_.

### 4. Dal modello ACID al modello BASE

Poiché non garantiscono rigidamente le proprietà ACID, i database NoSQL seguono comunemente il modello **BASE**, che stabilisce tre principi:

- **BA (Basically Available):** Il sistema risponde a tutte le richieste, garantendo una disponibilità "di base", anche se si verificano errori parziali nel cluster.
- **S (Soft State):** Lo stato del database può variare nel tempo a causa della propagazione differita dei dati. Vengono tollerate delle inconsistenze temporanee tra i nodi.
- **E (Eventual Consistency):** Gli aggiornamenti si propagano gradualmente alla rete. Non c'è sincronia immediata tra i nodi, ma il sistema garantisce che, prima o poi ("eventualmente"), tutto il cluster convergerà verso lo stesso stato finale corretto.

### 5. Denormalizzazione e assenza di JOIN

I database NoSQL **non supportano nativamente le operazioni di JOIN** tra tabelle/collezioni (salvo rarissime eccezioni). Effettuare un JOIN in un sistema frammentato (sharded) su più macchine è un'operazione estremamente pesante che annullerebbe i vantaggi della scalabilità orizzontale e i bassi tempi di latenza di questi DB. Per risolvere questo problema i NoSQL usano **dati denormalizzati**: i dati correlati vengono annidati (o incorporati) all'interno dello stesso documento. Se proprio serve unire informazioni scollegate, è il livello applicativo (il codice dell'applicazione) a doversi far carico di prelevarle e combinarle manualmente.

### 6. Le quattro tipologie di Database NoSQL

Il mondo "Non-relazionale" comprende diversi formati di archiviazione, ognuno utile per specifici scenari:

1. **Key-Value Database:** Immagazzina i dati in semplici coppie chiave-valore. La chiave è **Key-Value Database:** Immagazzina i dati in semplici coppie chiave-valore. La chiave è univoca e il valore è una "scatola nera" (opaco, nessun formato specifico richiesto) e può contenere di tutto (stringhe, binari, JSON). L'accesso (inserimento, lettura, aggiornamento, eliminazione) è possibile unicamente tramite la chiave. _Casi d'uso:_ Caching, session storage, configurazioni semplici. _Esempi: Redis, Riak, DynamoDB_. 
* **Document-oriented Database:** I dati sono inseriti all'interno di documenti strutturati (JSON, BSON, XML, YAML) flessibili, che non richiedono a tutti i documenti di una collezione di avere la stessa struttura. Supportano query nidificate e potenti funzioni di aggregazione dati. _Esempi: MongoDB, Elasticsearch, CouchDB_. 
* **Column-oriented Database:** I dati sono organizzati per righe, ma ogni riga può avere un numero e un tipo di colonne differente. Un concetto chiave è la **Column Family**: un contenitore per un gruppo di colonne correlate, salvato fisicamente in un file separato. Sono ottimali per grossi carichi di scrittura (write-heavy), dati time-series e macro-analisi. _Esempi: Cassandra, Apache HBase_. 
* **Graph-oriented Database:** Usa un approccio geometrico altamente interconnesso. I dati sono Entità modellate in "Nodi", mentre le relazioni sono "Archi" (Edges) tra i nodi. L'interrogazione avviene tramite l'attraversamento del grafo (graph traversal) a partire da uno o più nodi iniziali. _Casi d'uso:_ Raccomandazioni, anti-frode, social network. _Esempi: Neo4j, OrientDB, Amazon Neptune_.

### 7. Scelta del Database in architetture Cloud (Esempio Google Cloud) 

La scelta del DB dipende fortemente dal caso d'uso specifico dell'applicazione: 
* **DB Relazionali (Cloud SQL / Spanner):** Ideali per sistemi ERP, CRM, e-commerce, ledger finanziari e gestione inventari, dove servono alta disponibilità (HA) e garanzie transazionali rigide.
* **Documentale (Firestore):** Ottimo per app mobile/web serverless, cataloghi personalizzati e profili utente, che richiedono sincronizzazione in tempo reale e offline. 
* **Column-oriented (Cloud Bigtable):** Archivi a colonna larga perfetti per elaborazioni su grandissima scala e bassa latenza, come AdTech, feed social, IoT ed eventi di lettura/scrittura pesanti. 
* **In-Memory / Key-Value (Memory Store):** Sistemi gestiti come Redis o Memcached, cruciali per accessi ai dati sotto il millisecondo. Ideali per caching, leaderboard del gaming e motori di raccomandazione.