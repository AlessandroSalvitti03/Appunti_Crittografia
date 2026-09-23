### **Definizione di Minimum Spanning Tree** 

Dato un grafo non orientato e connesso, un _albero di copertura_ (spanning tree) è un sottografo che mantiene la struttura di un albero e include tutti i vertici del grafo originale. Se agli archi è associato un costo (o peso), il **Minimum Spanning Tree (MST)** è l'albero di copertura che possiede il costo totale minimo, calcolato come la somma dei pesi dei suoi archi. È importante ricordare che per un dato grafo, l'MST non è necessariamente unico.

![[Pasted image 20260506110917.png]]

Per analizzare gli algoritmi su questi alberi, si introducono due concetti topologici:

- **Taglio (Cut):** Una partizione dei vertici in due insiemi distinti; un arco "attraversa" il taglio se i suoi estremi si trovano uno in un insieme e uno nell'altro.

- **Ciclo (Cycle):** Un cammino in cui il primo e l'ultimo vertice coincidono.

### **L'Approccio Greedy e le Regole di Colorazione**

Gli algoritmi per calcolare l'MST adottano tutti una strategia **Greedy** (golosa), effettuando a ogni passo la scelta che appare localmente più conveniente. L'algoritmo generico costruisce l'MST un arco alla volta usando due regole di colorazione (blu per gli archi inclusi nella soluzione, rosso per quelli esclusi):

- **Regola del Taglio (Regola Blu):** Dato un taglio che non contiene archi blu, si prende l'arco non colorato di **costo minimo** che attraversa il taglio e lo si colora di blu, includendolo nell'albero.

- **Regola del Ciclo (Regola Rossa):** Dato un ciclo che non contiene archi rossi, si prende l'arco non colorato di **costo massimo** e lo si colora di rosso, scartandolo definitivamente (altrimenti non si otterrebbe un albero).

### **Algoritmo di Kruskal**

- **Strategia:** Ordina inizialmente tutti gli archi del grafo in senso crescente di costo. Mantiene una "foresta" di alberi blu inizialmente disgiunti. Analizzando gli archi uno ad uno, se un arco unisce due vertici dello _stesso_ albero blu, creerebbe un ciclo, quindi viene scartato (regola rossa). Se unisce due vertici di alberi _diversi_, viene colorato di blu unendo i due sottoalberi (regola blu).

![[Pasted image 20260506111220.png]]
![[Pasted image 20260506111313.png]]

- **Complessità:** L'approccio base costa O(mn). L'efficienza migliora drasticamente appoggiandosi al problema **Union/Find**, che gestisce set disgiunti tramite operazioni di _MakeSet_, _Find_ e _Union_. Implementando Union/Find con strutture dati ottimali, Kruskal ordina gli archi ed esegue le sue operazioni in un tempo totale pari a O(mlogn).

![[Pasted image 20260506111449.png]]
### **Algoritmo di Prim**

- **Strategia:** A differenza di Kruskal, Prim mantiene un **singolo** albero blu in continua crescita, partendo da un vertice sorgente scelto arbitrariamente. A ogni iterazione (per n−1 volte), applica la regola del taglio individuando l'arco di costo minimo incidente sull'albero e lo aggiunge espandendo la struttura.

- **Complessità:** Anche l'implementazione base di Prim richiede O(mn). Per ottimizzarla, si usa una **Coda di Priorità** per tenere traccia degli archi incidenti all'albero e poterne estrarre velocemente il minimo ad ogni passo:
    - Implementato con un _Binary Heap_, il costo totale scende a O(mlogn).
    - Implementato con un _Fibonacci Heap_, la complessità raggiunge il valore di O(m+nlogn), che rappresenta la **migliore soluzione nota in assoluto** per il problema del Minimum Spanning Tree.