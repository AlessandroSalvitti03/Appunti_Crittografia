### **Concetti Fondamentali su Algoritmi e Strutture Dati** 
Un algoritmo è una procedura definita passo dopo passo e formata da una sequenza finita di istruzioni che trasforma i dati di input in dati di output per risolvere un problema computazionale. Per poter essere compreso ed eseguito da un computer, un algoritmo deve possedere tre caratteristiche fondamentali:

- **Non ambiguo:** Ogni operazione deve avere un'interpretazione univoca.
- **Eseguibile:** Ogni operazione deve poter essere portata a termine con le risorse disponibili.
- **Finito:** L'esecuzione deve terminare in un tempo limitato per qualsiasi configurazione di dati in ingresso.

Inoltre, gli algoritmi operano quasi sempre su **strutture dati**, ovvero metodi specializzati e sofisticati per organizzare le informazioni nella memoria. Un algoritmo di successo deve essere **corretto** (restituire il risultato esatto senza fallire) ed **efficiente** (minimizzare l'impiego di CPU e memoria RAM).

### **Perché analizzare gli algoritmi?** 
L'analisi teorica (e matematica) è cruciale e preferibile a quella puramente sperimentale perché:

- Si applica a tutte le possibili istanze di input, senza dipendere dalla specifica tecnologia o compilatore.
- Offre garanzie matematiche per prevedere le performance prima ancora di implementare il codice.
- Aiuta a scartare subito gli approcci inadeguati, focalizzandosi sul progettare programmi in grado di gestire enormi moli di dati efficientemente.

### **La Notazione Asintotica (Big-O)** 
Per valutare la velocità di esecuzione, si utilizza un approccio indipendente dalla macchina: si stima il numero di istruzioni o righe di codice eseguite in base alla grandezza dell'input (n). Tramite la **notazione asintotica** O(g(n)), è possibile definire l'ordine di grandezza dei costi computazionali tralasciando costanti irrilevanti e dettagli inessenziali. In questo modo, l'algoritmo viene classificato matematicamente in base al suo comportamento per quantità di dati molto elevate.

### **Un Caso di Studio: Il calcolo dei Numeri di Fibonacci** 
Il documento esplora la serie di Fibonacci (Fn​=Fn−1​+Fn−2​) per illustrare l'impatto drammatico dell'efficienza algoritmica attraverso vari approcci progressivi:

1. **fibonacci1 (Algoritmo Numerico):** Usa direttamente una formula algebrica basata sulla Sezione Aurea. È veloce ma pecca in **correttezza** a causa dell'accuratezza decimale, restituendo numeri errati per indici alti.

2. **fibonacci2 (Algoritmo Ricorsivo):** Implementa in modo diretto e naturale la definizione matematica chiamando sé stesso. Tuttavia, è estremamente inefficiente perché continua a ricalcolare le stesse soluzioni parziali sovrapposte; il suo costo computazionale esplode in un tempo asintotico esponenziale pari a O(2n).

3. **fibonacci3 (Algoritmo Iterativo):** Risolve le ripetizioni inutili del metodo ricorsivo archiviando i risultati calcolati all'interno di un array di grandezza n. Abbassa il tempo di esecuzione a un valore lineare O(n), ma consuma una considerevole quantità di memoria O(n) per salvare l'intera sequenza.

4. **fibonacci4 (Algoritmo Ottimizzato per la Memoria):** Migliora il processo iterativo riconoscendo che bastano le ultime due variabili calcolate per trovare la successiva. Mantiene il tempo eccellente O(n) ma riduce il consumo di memoria a O(1), che equivale a spazio costante.

5. **fibonacci5 e fibonacci6 (Algoritmo con Matrici):** Sfrutta una proprietà matematica sulle matrici per calcolare le potenze. Con `fibonacci6`, basandosi sul principio del calcolo ricorsivo delle potenze intermedie, si ottiene un miglioramento enorme raggiungendo tempi logaritmici O(logn) ed essendo "esponenzialmente più veloce" delle versioni standard iterative.