### **Che cos'è una Coda di Priorità?**
Una coda di priorità è un tipo di dato astratto utilizzato per descrivere un set di elementi in cui ciascun elemento è associato a una **priorità** (o chiave). Le operazioni fondamentali che questa struttura deve poter eseguire sono:

- `findMin`: restituisce l'elemento con la priorità più bassa (il valore minimo).
- `insert`: inserisce una nuova chiave nel set.
- `deleteMin`: rimuove e restituisce l'elemento con priorità più bassa. Le implementazioni elementari basate su Array (ordinati o meno) o Liste Concatenate risultano inefficienti, in quanto richiedono inevitabilmente un tempo lineare O(n) per completare almeno una delle operazioni di inserimento o cancellazione. Per ottenere tempi migliori, si passa alle strutture **Heap**.

### **d-Heap e Binary Heap**
Un d-heap è un albero d-ario (o binario se d=2) che si mantiene completo fino al penultimo livello. Possiede un'importantissima proprietà strutturale chiamata **Heap sorting** (Ordinamento a Heap): la chiave di ogni nodo è maggiore o uguale alla chiave del padre, garantendo così che l'elemento minimo assoluto si trovi sempre nella radice dell'albero. Se l'albero ha una "struttura rafforzata" in cui le foglie dell'ultimo livello sono tutte compattate a sinistra, l'intero heap **può essere rappresentato implicitamente tramite un array** posizionale, senza l'uso di puntatori.

_Le operazioni sui d-Heap:_

- Poiché un d-heap con n nodi ha un'**altezza pari a** O(logd​n), tutte le procedure per ripristinare l'ordine a seguito di modifiche (`moveUp` se un nodo è risalito e `moveDown` se un nodo deve scendere) hanno un costo asintotico logaritmico O(logd​n).
- Di conseguenza, le operazioni di `insert`, `delete`, `increaseKey` e `decreaseKey` impiegano un tempo proporzionale all'altezza dell'albero, ovvero O(logd​n).

### **Gli Alberi e gli Heap Binomiali** 
Per ottenere implementazioni ancora più avanzate, si passa a strutture basate sugli **Alberi Binomiali (**Bh​**)**.

- **Costruzione:** Un albero binomiale si definisce in modo ricorsivo. L'albero di base B0​ è composto da un solo nodo. Per ogni i≥0, un albero Bi+1​ si crea fondendo due alberi Bi​, dove la radice dell'uno diventa "figlia" della radice dell'altro.
- **Proprietà:** Un albero binomiale di ordine h ha esattamente n=2h nodi, mentre il grado della radice e l'altezza dell'albero valgono esattamente log2​n.

Un **Heap Binomiale** è una foresta di questi alberi binomiali. Presenta una proprietà di **unicità**: nella foresta può esserci al massimo un solo albero Bi​ per ogni ordine. Di conseguenza, in un heap di n nodi ci saranno al massimo log2​n alberi. Per mantenere l'unicità dopo le operazioni che fondono o scompongono alberi, viene eseguita regolarmente una procedura di `rebuild()`.

### **L'Heap di Fibonacci** 
Rappresenta l'evoluzione più sofisticata illustrata nel documento:

- Si parte dal concetto di **Heap binomiale rilassato**, che abbandona il vincolo di "unicità" degli alberi.
- Questo permette di adottare un **approccio "lazy" (pigro)** durante l'operazione di `insert`: invece di ricostruire immediatamente e ordinatamente la foresta ad ogni singolo inserimento (fondendo gli alberi), il sistema posticipa il lavoro pesante ad altre operazioni.
- L'**Heap di Fibonacci** vero e proprio rilassa non solo l'unicità, ma anche la struttura stessa, rendendo i suoi elementi non più strettamente alberi binomiali.
- Grazie a un'analisi sofisticata (basata sui _costi ammortizzati_), si dimostra che l'Heap di Fibonacci riesce a eseguire `findMin`, `insert`, `decreaseKey` e `merge` in uno straordinario **tempo costante** O(1).