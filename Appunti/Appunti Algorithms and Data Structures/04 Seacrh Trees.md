### **Alberi e Metodi di Visita**
Un albero è una struttura dati ideale per rappresentare informazioni gerarchiche, composta da nodi e archi. Nei calcolatori, gli alberi con un numero arbitrario di figli vengono spesso salvati con una rappresentazione ottimizzata in spazio O(n) del tipo "primo figlio - fratello successivo". Per scorrere i nodi in modo sistematico si usano algoritmi di visita:

- **Visita in Ampiezza (BFS):** Esplora l'albero livello per livello partendo dalla radice. Viene implementata utilizzando una struttura dati a coda (Queue).
- **Visita in Profondità (DFS):** Scende di figlio in figlio fino a raggiungere una foglia prima di tornare indietro. Si avvale di uno stack (o della naturale ricorsione delle funzioni). Entrambe visitano un albero di n nodi in tempo O(n).

### **Alberi Binari di Ricerca (BST) e il Problema del Dizionario**
I BST sono strutture fondamentali per risolvere il "problema del dizionario" (eseguire efficientemente inserimenti, cancellazioni e ricerche). In un BST, per ogni nodo v, **tutte le chiavi nel sottoalbero sinistro sono minori** della chiave di v, e **tutte le chiavi nel sottoalbero destro sono maggiori**.

- La ricerca e l'inserimento seguono intuitivamente questa proprietà scendendo a destra o sinistra.
- La cancellazione è più complessa: se si elimina un nodo con due figli, lo si deve rimpiazzare fisicamente con il suo **predecessore** (il nodo massimo del suo sottoalbero sinistro) per mantenere le proprietà di ricerca. Il costo di tutte queste operazioni dipende dall'altezza h dell'albero (O(h)). Il difetto dei BST classici è che, in caso di inserimenti sbilanciati, **l'altezza può degenerare fino a diventare pari al numero dei nodi, portando a tempi pessimi** O(n).

### **Alberi Bilanciati: Alberi AVL** 
Per garantire un tempo asintotico sempre pari a O(logn), si utilizzano strutture che si auto-bilanciano. Gli **Alberi AVL** sono BST in cui, per ogni nodo, le altezze del sottoalbero destro e sinistro **differiscono al massimo di 1**. Ad ogni nodo è associato un _fattore di bilanciamento_ (+1, 0, o -1). È matematicamente dimostrato, tramite lo studio degli "alberi di Fibonacci", che l'altezza di un AVL si mantiene sempre O(logn). Quando un inserimento o una cancellazione corrompe questo equilibrio, l'albero esegue delle **rotazioni** in tempo costante O(1) per ristabilirlo. Esistono 4 casi di sbilanciamento (SS, DD, SD, DS), risolvibili con rotazioni singole o doppie sui nodi colpiti.

### **Alberi 2-3** 
Un'alternativa per garantire operazioni in tempo O(logn) sono gli Alberi 2-3. A differenza dei BST:

- Ogni nodo interno può avere **2 o 3 figli**.
- Tutti i percorsi dalla radice alle foglie hanno esattamente la **stessa lunghezza** (perfetto bilanciamento in altezza).
- Le chiavi del dizionario si trovano ordinatamente collocate solo nelle foglie, mentre i nodi interni tengono traccia dei valori massimi dei loro sottoalberi. Per assorbire inserimenti e cancellazioni senza perdere il bilanciamento perfetto, l'albero gestisce i cambiamenti espandendosi o contraendosi tramite le operazioni di **split** (divisione di un nodo con troppi figli) e **fuse** (fusione di nodi con pochi figli).

