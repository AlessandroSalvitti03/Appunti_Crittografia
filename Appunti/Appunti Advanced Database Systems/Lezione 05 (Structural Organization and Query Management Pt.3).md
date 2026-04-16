Ecco i tuoi appunti integrati e definitivi per la quinta lezione, in cui ho unito in modo armonico il riassunto concettuale di NotebookLM con le definizioni tecniche precise e le regole logiche presenti nelle slide originali del corso.

---

## 1. Introduzione agli Indici

- **Cos'è un indice:** È una struttura ausiliaria progettata per consentire un accesso efficiente e di tipo associativo ai record di un file. Si basa sui valori di un campo specifico (o di una concatenazione di campi) chiamato "chiave" o "pseudo-chiave", poiché non deve essere necessariamente un identificatore univoco.
    
- **Analogia:** L'idea di base è simile a quella dell'indice analitico di un libro: una lista di coppie (termine, pagina) ordinate alfabeticamente, poste alla fine del testo in modo separato da esso.
    
- **Struttura:** Fisicamente, un indice è un file a sé stante, composto da record a due campi: il valore della chiave e l'indirizzo (il puntatore al blocco relativo o al singolo record del file principale) ordinati in base alla chiave.
    
- **Tipi di Puntatori:** Si possono usare puntatori ai blocchi (più compatti) o puntatori ai record. I puntatori ai record permettono di semplificare alcune operazioni eseguibili interamente sull'indice, senza dover accedere al file principale se non strettamente necessario.
    

---

## 2. Tipologie di Indici

Gli indici si dividono in due macro-categorie principali in base all'ordinamento fisico del file su disco:

- **Indice Primario:** Definito sul campo utilizzato per il processo di archiviazione fisica e ordinamento del file (noti anche come _cluster indexes_). Ogni file può avere al massimo un solo indice primario. Da notare che i file basati su hash non possono avere un indice primario.
    
- **Indice Secondario:** Definito su un campo il cui ordinamento è diverso da quello di archiviazione fisica del file. Un file può avere innumerevoli indici secondari su campi diversi (es. una guida turistica può avere un indice per i luoghi e uno per gli artisti) e offrono vantaggi prestazionali estremamente importanti.
    

In base al numero di chiavi effettivamente indicizzate (densità), gli indici si dividono in:

- **Indice Denso:** Contiene un record per ogni singolo valore presente nel campo chiave del file sparso. Regola fondamentale: un indice secondario deve essere obbligatoriamente denso.
    
- **Indice Sparso:** Contiene meno record rispetto al numero di valori univoci presenti nel campo chiave. A differenza di quello secondario, un indice primario può essere sparso.
    

---

## 3. Vantaggi e Problemi degli Indici

- **Pro:** Garantiscono un accesso diretto molto efficiente (sia puntuale che per intervalli) e permettono una scansione sequenziale ordinata estremamente rapida (con un numero di accessi pari al numero di record del file).
    
- **Contro:** Poiché si basano su strutture ordinate, risultano poco flessibili e inefficienti in caso di alta dinamicità dei dati, ad esempio frequenti inserimenti, cancellazioni o modifiche delle chiavi.
    
- **Soluzioni:** Per attenuare questi problemi si adottano tecniche specifiche come file/blocchi di overflow, marcatura per eliminazione logica, riempimento parziale, blocchi concatenati (non contigui) e riorganizzazioni periodiche.
    

---

## 4. Indici Multilivello

- Poiché gli indici sono a tutti gli effetti dei file, è possibile (e corretto) creare "indici sugli indici" per velocizzare ulteriormente la ricerca ed evitare di dover analizzare blocchi diversi.
    
- Di solito, il numero di livelli è piuttosto contenuto perché i record degli indici sono molto piccoli e l'indice principale è già ordinato. L'obiettivo architettonico è arrivare ad avere il livello più alto formato da un singolo blocco.
    

---

## 5. Strutture ad Albero (B-tree e B+ tree)

Le normali strutture indicizzate ordinate soffrono in ambienti molto dinamici. Per ovviare a questo, i DBMS moderni utilizzano indici dinamici multilivello basati su alberi di ricerca bilanciati:

- **Alberi di ricerca P-ordinati:** Ogni nodo dell'albero ha fino a $P$ discendenti e fino a $P-1$ chiavi al suo interno, che suddividono lo spazio di ricerca (nell'i-esimo sottoalbero ci sono chiavi maggiori della chiave precedente e minori di quella attuale). Fisicamente, ogni nodo corrisponde a un blocco.
    
- **B-tree:** È un albero di ricerca che viene mantenuto dinamicamente bilanciato.
    
    - L'occupazione spaziale media si aggira intorno al 70%.
        
    - Il bilanciamento è garantito tramite operazioni locali: quando si inserisce un dato e la foglia è piena, il nodo viene diviso in due (**split**) e si aggiunge un puntatore al padre. Viceversa, se un'eliminazione svuota troppo un nodo, i nodi vengono uniti (**merge**). In questo modo l'occupazione resta sempre sopra il 50%.
        
    - Nei B-tree, anche i nodi intermedi possono possedere puntatori diretti ai dati.
        
- **B+ tree:** È la variante più ampiamente utilizzata nei DBMS. La differenza cruciale è che solo i nodi foglia puntano ai dati e, soprattutto, le foglie sono concatenate tra loro in una lista (linked list). Questa struttura li rende eccezionalmente validi per le ricerche per intervalli.
    

---

## 6. Organizzazione Fisica nei DBMS Reali

Le teorie discusse vengono implementate commercialmente con varie sfaccettature:

- **Strutture Primarie:** Possono essere non ordinate (heap/unclustered), ordinate (clustered) anche su pseudo-chiavi, basate su hash, o anche basate sul clustering di relazioni multiple.
    
- **Indici Secondari:** I DBMS offrono indici statici (ISAM, che opera su strutture ordinate) e indici dinamici (i B-tree).
    
- **Esempi pratici:**
    
    - _Oracle:_ Usa file heap o "hash cluster" come struttura primaria, e B-tree densi, bit-map o funzioni come indici secondari.
        
    - _DB2:_ Usa struttura primaria heap o ordinata supportata da un B-tree denso (l'indice sulla primary key è automatico). Gli indici secondari sono B-tree densi.
        
    - _SQL Server:_ Struttura primaria heap o ordinata supportata da un B-tree sparso, con indici secondari gestiti tramite B-tree densi.