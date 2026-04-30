### **Definizioni Fondamentali** 
Un grafo G=(V,E) è un modello matematico fondamentale per rappresentare relazioni binarie, composto da un insieme di **vertici** V (o nodi) e un insieme di **archi** E. I grafi si dividono in due categorie principali:

- **Non orientati:** Le relazioni sono simmetriche (es. persone che si stringono la mano); l'arco (u,v) è identico a (v,u).
- **Orientati (Direzionali):** Le relazioni non sono simmetriche (es. l'invio di un'email da un mittente a un destinatario).

All'interno di un grafo si possono individuare **cammini** (sequenze di vertici collegati da archi) e **cicli** (cammini in cui il vertice di partenza coincide con quello di arrivo). Un grafo non orientato si dice connesso se esiste un cammino tra ogni coppia di vertici, definendo così le sue "componenti connesse".

### **Rappresentazione in Memoria** 
Il modo in cui un grafo viene memorizzato ha un impatto drastico sui tempi di esecuzione degli algoritmi. Le due strutture più importanti sono:

- **Liste di adiacenza:** Ottimali per i grafi "sparsi". Il costo per eseguire una visita con questa struttura è lineare rispetto alla dimensione del grafo, pari a O(n+m) (dove n sono i vertici e m gli archi).
- **Matrice di adiacenza:** Una griglia in cui le celle indicano la presenza o meno di un arco. Con questa struttura, il costo di una visita sale a O(n2).

### **Algoritmi di Visita: Concetti Generali** 
Una "visita" è un meccanismo sistematico per esplorare tutti i vertici di un grafo partendo da un nodo sorgente. Per evitare di rimanere bloccati all'infinito nei cicli, l'algoritmo "marca" i vertici appena li incontra per la prima volta. Durante la visita, si mantiene una **frontiera** (F) di nodi scoperti ma non ancora interamente esplorati, e si genera un **albero di copertura (spanning tree)** che include tutti i nodi raggiunti.

Le due visite più importanti si differenziano proprio per come gestiscono questa frontiera:

**1. Visita in Ampiezza (BFS - Breadth-First Search)**

- **Struttura:** La frontiera è gestita tramite una **Coda (Queue)**.
- **Logica:** L'esplorazione procede a raggiera, in modo uniforme. Scopre tutti i vertici a distanza k (numero di archi) dalla sorgente prima di passare a quelli a distanza k+1.
- **Proprietà Chiave:** In un albero BFS, **il livello in cui si trova un nodo equivale esattamente alla sua distanza minima dal nodo sorgente**.
- **Complessità:** O(n+m) utilizzando le liste di adiacenza.

**2. Visita in Profondità (DFS - Depth-First Search)**

- **Struttura:** La frontiera è gestita tramite uno **Stack** (nella versione iterativa) o sfruttando la naturale **ricorsione** delle funzioni.
- **Logica:** Esplora un singolo ramo andando **"il più in profondità possibile"**. Quando raggiunge un nodo che non ha più archi inesplorati, fa _backtracking_ (torna indietro) per esplorare le vie alternative rimaste in sospeso.
- **Classificazione degli archi:** La DFS è eccellente per studiare la topologia del grafo. Oltre agli archi dell'albero, permette di classificare archi "all'indietro" (tra discendenti e antenati) o archi "trasversali a sinistra" (nei grafi orientati).
- **Complessità:** O(n+m).

### **Applicazioni Pratiche (Risoluzione di problemi)** 
Il documento propone l'utilizzo di queste visite per risolvere algoritmicamente alcuni problemi specifici:

- **Verificare se un grafo è connesso:** Si lancia una visita generica da un nodo qualsiasi; se l'albero di visita finale contiene esattamente n nodi, il grafo è connesso (costo O(n+m)).
- **Individuare un ciclo in un grafo non orientato:** Si usa una visita DFS. Se durante l'esplorazione si incontra un **arco all'indietro** (backward edge), significa che è presente un ciclo. Sorprendentemente, questo algoritmo richiede un **tempo di esecuzione pari a** O(n): in un grafo senza cicli (che quindi è una foresta/albero) ci possono essere al massimo n−1 archi, perciò se la visita deve esplorare n o più archi, l'algoritmo può interrompersi subito, avendo la certezza matematica della presenza di un ciclo.