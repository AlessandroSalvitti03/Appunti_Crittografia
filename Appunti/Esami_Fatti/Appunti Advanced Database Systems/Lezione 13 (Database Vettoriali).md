
### 1. I limiti dei Database Tradizionali e la Necessità dei Vector DB

I database tradizionali (SQL e NoSQL) sono stati progettati per gestire dati strutturati o semi-strutturati, con schemi ben definiti e interrogazioni di tipo deterministico (come _matching_ esatto e aggregazioni). Mostrano però profondi limiti in contesti moderni:

- **Gestione di dati non strutturati:** Testi lunghi, immagini, log e dati multimodali vengono forzati in strutture rigide come tabelle o JSON, causando perdita di struttura e inefficienze di archiviazione.
- **Curse of dimensionality (Maledizione della dimensionalità):** Le applicazioni moderne usano vettori con centinaia o migliaia di dimensioni, causando il degrado prestazionale dei tradizionali indici B-Tree o tabelle Hash.
- **Scarsa scalabilità nella ricerca:** Senza indici specializzati, le ricerche complesse richiedono scansioni complete (full scan) del dataset, con una complessità temporale di $O(n \cdot d)$, rendendoli inutilizzabili per sistemi in tempo reale e latenze altissime.
- **Incapacità di ricerca semantica:** L'_exact matching_ si basa solo sulla sintassi e fallisce in presenza di sinonimi, parafrasi o ambiguità. I sistemi di intelligenza artificiale richiedono invece un _semantic matching_ che catturi significato, contesto e approssimazione.

### 2. Embeddings e Ricerca Vettoriale

Per cercare attraverso il "significato", è necessario trasformare i dati grezzi (simboli testuali, pixel di immagini, segnali audio) in uno spazio numerico comune.

- **Embeddings:** Sono funzioni ($f: X \rightarrow R^d$) che mappano i dati all'interno di uno spazio vettoriale ad alta dimensionalità. Attraverso modelli come i Transformers, gli embeddings imparano a posizionare input di significato simile vicini nello spazio metrico.
- **Dipendenza dal contesto:** Le rappresentazioni imparano dal contesto, permettendo di differenziare parole uguali con significati diversi (es. "bank" come riva del fiume vs. istituto finanziario).

Con gli embeddings, il problema informatico passa dal "data retrieval" al **"vector search"**. La somiglianza equivale alla prossimità nello spazio e si misura comunemente in due modi:

1. **Distanza Euclidea (L2):** Misura la distanza in linea retta tra due punti. È sensibile alla magnitudo (grandezza) ed è indicata quando contano le differenze assolute e geometriche.
2. **Similarità del Coseno (Cosine Similarity):** Misura l'angolo tra due vettori, ignorandone la lunghezza. È invariante rispetto alla scala ed è la metrica ideale e più comune per codificare le relazioni semantiche.
3. 
**Proprietà chiave degli Embeddings e dimensionalità:** 
* Gli embeddings sono vettori densi (non sparsi) e continui. 
* Sono appresi dai modelli (non definiti manualmente), sono dipendenti dal task e forniscono rappresentazioni approssimate (non esatte). 
* Esiste un trade-off fondamentale: una maggiore dimensionalità garantisce una rappresentazione più ricca, ma rende la ricerca computazionalmente più difficile (curse of dimensionality).

### 3. Algoritmi per la ricerca di Vettori: Da Esatta ad Approssimata

Cercare il vettore perfetto calcolando la distanza tra la query e l'intero dataset (_Brute-force search_) restituisce risultati ottimali ma richiede un calcolo insostenibile $O(n \cdot d)$, rendendolo impraticabile per limiti di latenza.

La soluzione consiste negli algoritmi **ANN (Approximate Nearest Neighbour)**, i quali esaminano solo un sottoinsieme di dati, restituendo un risultato sub-ottimale ma enormemente più veloce. I tre approcci ANN principali sono:

- **HNSW (Hierarchical Navigable Small World):** È l'indice a grafo dominante. I dati diventano nodi del grafo, e le connessioni simulano la vicinanza spaziale sfruttando la proprietà delle reti "Small World" (percorsi brevi). Ha una struttura gerarchica su più livelli: la ricerca usa un algoritmo _greedy_ che scende dall'alto verso i dettagli, garantendo una ricerca con complessità logaritmica $O(log n)$.
- **Parametri di configurazione HNSW:** 
	* **M**: definisce le connessioni massime per nodo e controlla il grado del grafo. Un valore più alto migliora il recall ma aumenta l'uso di memoria , portando la complessità spaziale a $O(n \cdot M)$. 
	* **ef_construction**: definisce la dimensione della lista dei candidati durante la fase di costruzione dell'indice, influenzandone la qualità. 
	* **ef_search**: definisce la dimensione della lista dei candidati durante la query, permettendo di bilanciare dinamicamente il trade-off tra recall e latenza.
- **IVF (Inverted File Index):** Approccio basato sul clustering (es. k-means). Divide lo spazio in celle di Voronoi; a tempo di esecuzione cerca solo nei cluster rilevanti. È memory-efficient e scalabile, ma richiede una fase di addestramento e dipende dalla qualità dei cluster.
- **Trade-off di IVF:** 
	* _Pro:_ È scalabile per dataset di dimensioni enormi, efficiente in termini di memoria e funziona bene in combinazione con la compressione. 
	* _Contro:_ Dipende fortemente dalla qualità del clustering, rischia di mancare i veri nearest neighbours e richiede una fase di addestramento preliminare del modello.
- **Disk-Based ANN:** Creato da Microsoft, immagazzina l'indice su memoria SSD mantenendone in RAM solo piccole porzioni, ideale per dataset immensi ma penalizzato un po' dalla latenza del disco.

### 4. Vector DB in Produzione e Architetture di Sistema

Un moderno Database Vettoriale non si limita a immagazzinare vettori, ma salva anche identificativi, metadati (es. data, fonte), carichi (payload) e indici. Combinare la ricerca vettoriale con il filtraggio sui metadati è essenziale per ottimizzare la precisione e supportare regole di business.

Oggi esiste una varietà di DBMS specializzati, ognuno con determinati trade-off:

- **Qdrant:** Motore open-source molto forte nel filtering dei payload, ad alte performance con HNSW.
- **Weaviate:** Piattaforma DB modulare pensata per il knowledge retrieval enterprise, ottima per ricerche ibride.
- **Pinecone:** DB completamente gestito in Cloud, perfetto per deployment rapidi senza gestire l'infrastruttura.
- **Milvus:** Database scalabile e distribuito, disegnato per la ricerca su miliardi di vettori in ambienti enterprise.
- **Pgvector:** Un'estensione che porta la ricerca vettoriale all'interno di PostgreSQL, perfetta per chi usa già questo ecosistema e non ha moli enormi di dati vettoriali.
- **Chroma:** Strumento leggero e developer-friendly, ideale per la prototipazione rapida e la sperimentazione locale.
Non esiste un Vector DB migliore in assoluto in modo universale. La scelta architetturale dipende da vincoli specifici del progetto come: la dimensione del dataset, i requisiti di latenza, la complessità dei filtri applicati, la frequenza di aggiornamento dei dati, i vincoli operativi, l'infrastruttura già esistente e il modello di costo desiderato.

### 5. Strategie Avanzate (Trend Moderni)

- **Ricerca Ibrida (Hybrid Search):** I vettori densi eccellono per i concetti semantici, ma falliscono su codici, acronimi e ID. La ricerca ibrida combina i risultati delle matrici dense con quelli sparsi/BM25 (esatto match delle parole chiave) fondendo i due rank per ottenere il meglio da entrambi.
- **Reranking:** Consiste nell'estrarre un alto numero di candidati (es. i migliori 100) dal DB vettoriale, e in seguito ri-classificarli usando un _reranker_ più accurato ma più costoso in termini di calcolo, prima di passare il contesto (es. i top-5) all'LLM.
- **Late Interaction Retrieval:** Anziché riassumere un intero lungo documento in un solo vettore denso (dove perde dettagli), il documento viene convertito in "tanti" vettori per quanti sono i token, preservandone le specificità, benché questo aumenti notevolmente i requisiti di memoria e i costi di calcolo.
- **Multimodal Retrieval:** I database vettoriali non sono limitati al solo testo. Possono processare e restituire risultati combinati indicizzando simultaneamente query testuali, immagini, didascalie, documenti completi e record di prodotti, permettendo ricerche incrociate.

Queste tecnologie formano l'infrastruttura per sviluppare oggi Motori di Ricerca Semantici, applicazioni RAG (Retrieval-Augmented Generation), sistemi di raccomandazione e ricerche di intelligenza multimodale.