### **Perché Analizzare gli Algoritmi e il Modello Computazionale** 
L'analisi teorica è molto più affidabile di quella sperimentale perché si applica a tutte le possibili istanze di input, garantendo una stima matematica delle performance senza dover scrivere o testare codice reale. Per analizzare i costi, ci si basa su un modello astratto indipendente dalla macchina (simile alla _Register machine_ di von Neumann) e sul linguaggio. Le regole di base assumono che:

- Un'istruzione semplice costa **1**.
- Il costo di un'istruzione condizionale (_if-else_) è il massimo tra i costi dei due rami.
- Il costo di un ciclo è il costo del suo corpo moltiplicato per il numero di iterazioni.

### **Dimensione dell'Input ed Efficienza Asintotica**
Il tempo di esecuzione di un algoritmo T(n) si calcola in funzione della **dimensione dell'input** n (es. numero di elementi in un array o nodi in un grafo). Per input molto grandi, si analizza l'**efficienza asintotica**, prendendo in considerazione solo l'**ordine di grandezza** del tempo di esecuzione (il termine di grado massimo), ignorando i dettagli inessenziali come i fattori costanti e i termini di grado inferiore.

### **Le Tre Notazioni Asintotiche Principali** 
Per classificare matematicamente le funzioni T(n), si utilizzano tre notazioni:

1. **Notazione** O **(Big-O):** Definisce un **limite superiore** (upper bound). Indica il costo massimo dell'algoritmo nel crescere di n.

2. **Notazione** Ω **(Omega):** Definisce un **limite inferiore** (lower bound). Viene usata per stabilire la _complessità intrinseca_ di un problema: significa che ogni algoritmo che risolve quel problema dovrà fare almeno quel numero di operazioni.

3. **Notazione** Θ **(Theta):** Definisce un **limite stretto** (strict bound) tra limite superiore e inferiore.

### **Algoritmo Ottimo e Operazione Dominante** 
Un algoritmo si definisce **ottimo** quando il suo costo di esecuzione O(f(n)) è uguale alla complessità intrinseca del problema Ω(f(n)), per cui avrà complessità Θ(f(n)). Per calcolare velocemente il limite superiore O guardando uno pseudocodice, si cerca l'**operazione dominante**, ovvero l'istruzione eseguita il maggior numero di volte (di solito quella situata nei cicli più annidati).

### **Metodi di Analisi: Caso Peggiore, Medio e Migliore** 
Poiché le performance variano in base a come sono disposti i dati in ingresso, si studiano tre scenari:

- **Caso Peggiore (Worst Case):** Il tempo massimo richiesto. È l'analisi più comune perché fornisce una garanzia assoluta che l'algoritmo non impiegherà mai più tempo di così.
- **Caso Medio (Average Case):** Il tempo richiesto su istanze "tipiche", calcolato in base alla probabilità statistica. Spesso il caso medio ha la stessa complessità del caso peggiore (ad esempio, nella _ricerca sequenziale_ entrambi sono O(n)).
- **Caso Migliore (Best Case):** Il tempo minimo assoluto, che si verifica con input altamente favorevoli.

_(Nota: un miglioramento enorme si ottiene ad esempio usando la_ **Ricerca Binaria** _su array già ordinati, in cui lo spazio di ricerca si dimezza a ogni passo, abbassando il tempo del caso peggiore e medio a_ O(logn)_)._

### **L'Analisi degli Algoritmi Ricorsivi**
I metodi ricorsivi usano la strategia **"Divide et Impera"** (Dividi, Risolvi, Combina). Il loro tempo non può essere calcolato con cicli standard, ma tramite **Equazioni di Ricorrenza**. Per risolverle, si utilizzano tre metodi:

1. **Metodo Iterativo:** Si "srotola" la ricorsione fino a ottenere una sommatoria esplicita.
2. **Metodo di Sostituzione:** Si "indovina" la soluzione e si usa l'induzione matematica per dimostrarne la validità.
3. **Master Theorem:** È il metodo più potente e diretto per analizzare algoritmi divide-et-impera la cui equazione è nella forma T(n)=aT(n/b)+f(n). Confronta matematicamente il costo di divisione/combinazione f(n) con il numero di sottoproblemi nlogb​a per fornire la classe di complessità in 3 casi specifici.

### **Complessità Spaziale** 
Oltre al tempo, si valuta lo spazio (la memoria RAM occupata). Se per algoritmi iterativi è semplice (variabili e dati salvati), per gli algoritmi ricorsivi (come la ricerca binaria) bisogna calcolare anche **tutta la memoria occupata dalle chiamate ricorsive rimaste attive** in esecuzione contemporaneamente.