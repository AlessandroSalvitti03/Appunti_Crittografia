### **Il Problema dell'Ordinamento e Proprietà**
Ordinare un array è fondamentale perché permette, ad esempio, di effettuare ricerche molto più veloci (in tempo logaritmico O(logn)). Gli algoritmi si differenziano anche in base a due importanti proprietà:

- **In loco (In-place):** Durante l'esecuzione, richiedono al massimo un numero costante di variabili aggiuntive in memoria al di fuori dell'array originale.
- **Stabile:** Preservano l'ordine iniziale degli elementi che hanno la stessa chiave di ordinamento (ad esempio, a parità di voto, chi ha consegnato prima il compito rimane primo).

### **Il Limite Inferiore (Lower Bound) per gli algoritmi basati su confronti**
Utilizzando un modello in cui l'ordinamento avviene solo confrontando e scambiando elementi, è possibile dimostrare matematicamente che **il tempo minimo necessario nel caso peggiore è** Ω(nlogn). Questa dimostrazione si avvale degli **Alberi di Decisione**: l'albero rappresenta tutte le possibili sequenze di confronti per le n! (n-fattoriale) possibili permutazioni dell'array di partenza. Poiché l'altezza minima di questo albero corrisponde al numero di confronti nel caso peggiore, usando la formula di Stirling si ottiene proprio Ω(nlogn).

### **Algoritmi a Tempo Quadratico** 

O(n2) Basati su approcci incrementali, sono i più semplici ma inefficienti per grandi dataset:

- **Selection Sort:** Cerca a ogni passo il minimo tra gli elementi non ordinati e lo mette nella sua posizione definitiva.
- **Insertion Sort:** Prende l'elemento successivo e lo fa "scivolare" all'indietro finché non trova la sua posizione corretta rispetto alla parte di array già ordinata.
- **Bubble Sort:** Scorre ripetutamente l'array confrontando e scambiando elementi adiacenti se sono fuori ordine, facendo "emergere" i valori più alti verso destra.

### **Algoritmi Ottimi** O(nlogn)

- **Merge Sort:** Utilizza la tecnica _Divide et Impera_. Divide l'array a metà, ordina ricorsivamente le due porzioni e poi le unisce ("merge"). È molto efficiente ma non è un algoritmo "in loco".
- **Heap Sort:** Usa un approccio simile al Selection Sort, ma estrae il massimo in tempo O(logn) invece che O(n) sfruttando una struttura dati chiamata **Max-Heap** (un albero binario quasi completo dove ogni nodo padre è maggiore o uguale ai suoi figli). È un algoritmo ottimo e funziona "in loco".

### **Quick Sort** 
Un algoritmo _Divide et Impera_ dove il passo critico è la fase di _Divide_: si sceglie un elemento chiamato **pivot** e si separano gli elementi più piccoli da una parte e quelli più grandi dall'altra, procedendo poi ricorsivamente. Al contrario del Merge Sort, nel Quick Sort l'operazione di combinazione è banale. Sebbene il tempo asintotico nel **caso medio sia ottimo** O(nlogn), se l'array viene diviso in modo molto sbilanciato (es. si pesca sempre il minimo o il massimo come pivot), il tempo nel **caso peggiore degrada a** O(n2).

### **Algoritmi a Tempo Lineare** 
O(n) **(Non basati su confronti)** Per superare il limite matematico Ω(nlogn), questi algoritmi non confrontano i valori tra loro ma "contano" o raggruppano chiavi intere che rientrano in un range predefinito [1,k]:

- **Integer Sort:** Usa un array di supporto grande k per contare le occorrenze di ogni numero. Il tempo è lineare O(n+k), ma funziona solo per interi e non è in loco.
- **Bucket Sort:** Utile quando le chiavi possono essere duplicate tra record. Al posto di contatori, crea un array di k "liste" dove inserisce i record interi. È stabile e ha sempre costo O(n+k).
- **Radix Sort:** Risolve il problema dell'ordinamento per chiavi composte da più cifre (es. date o numeri molto grandi). Ordina l'array cifra per cifra in modo "controintuitivo", partendo dalla **cifra meno significativa fino alla più significativa**, avvalendosi di un algoritmo stabile per ogni passata (come il Bucket Sort). Se il numero di cifre d è costante e k=O(n), il tempo di esecuzione è garantito a O(n).