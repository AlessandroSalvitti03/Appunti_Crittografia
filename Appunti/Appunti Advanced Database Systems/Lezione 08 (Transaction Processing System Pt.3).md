**1. Il Controllo della Concorrenza nella Pratica**

- L'algoritmo teorico per verificare la _conflict-serializability (CSR)_ non è utilizzabile nei sistemi reali perché richiederebbe di conoscere l'intero grafo dei conflitti fin dall'inizio e di ricalcolarlo a ogni singola operazione.
- Inoltre, le teorie si basano sull'ipotesi impraticabile della _commit-projection_.
- Di conseguenza, i DBMS usano tecniche in grado di **garantire la conflict-serializability in modo incrementale**, senza mai dover costruire o controllare il grafo e senza aver bisogno della commit-projection.

**2. La Tecnica Two-Phase Locking (2PL)**

- **Principio base:** Ogni operazione di lettura deve essere preceduta dalla richiesta di un **r_lock** (lock condiviso) e ogni scrittura da un **w_lock** (lock esclusivo), seguiti poi da un comando di sblocco (**unlock**).
- **Lock escalation:** Una transazione può richiedere prima un lock in lettura e poi "promuoverlo" a lock esclusivo per la scrittura.
- **Lock Manager:** È il componente che accetta o rifiuta le richieste basandosi su una _tabella dei conflitti_. Se si richiede un `r_lock` su una risorsa già bloccata da un altro `r_lock`, l'accesso è consentito; se è presente un `w_lock`, qualsiasi altra richiesta viene respinta.
- **Gestione:** Se una risorsa è occupata, la transazione richiedente viene messa **in attesa** (spesso in una coda) finché la risorsa non si libera. Un contatore tiene traccia del numero di "lettori" simultanei.

**3. Le Due Fasi del 2PL**

- La regola fondamentale per garantire la serializzabilità è: **una transazione, dopo aver rilasciato un lock, non può acquisirne di nuovi**.
- Questo divide l'esecuzione di ogni transazione in due fasi ben distinte:
    1. **Growing phase (Fase di crescita):** Vengono acquisiti progressivamente i lock sulle risorse. La _lock escalation_ può avvenire solo in questa fase.
    2. **Shrinking phase (Fase di restringimento):** I lock acquisiti vengono via via rilasciati.

**4. 2PL e Serializzabilità (CSR)**

- Ogni schedule che rispetta le regole della classe 2PL è automaticamente anche _conflict-serializable (CSR)_. Questo perché, ordinando le transazioni in base al momento in cui iniziano a rilasciare i lock, si ottiene uno schedule seriale equivalente.
- Il viceversa non è sempre vero: esistono schedule che sono CSR ma che violano il protocollo 2PL (quindi l'insieme 2PL è un sottoinsieme del CSR).

**5. Metodo basato sui Timestamp (TS)**

- È una tecnica alternativa al 2PL. Si basa sull'assegnazione di un _Timestamp_ a ogni transazione, che rappresenta il suo istante di inizio (definendo così un ordine cronologico totale).
- Lo scheduler mantiene per ogni dato due contatori: **RTM** (Read Timestamp, l'ultima lettura) e **WTM** (Write Timestamp, l'ultima scrittura).
- **Le regole:** In pratica, nessuna transazione può leggere o scrivere un dato che è stato già scritto da una transazione più "giovane" (con timestamp maggiore), e non può scrivere su un dato già letto da una transazione più giovane.
- Se queste regole vengono violate, la richiesta viene respinta e **la transazione viene uccisa (killed) e fatta ripartire**.

**6. Confronto: 2PL vs Timestamp**

- **Incomparabilità:** Dal punto di vista matematico, ci sono sequenze valide per il TS ma non per il 2PL e viceversa.
- **Gestione dei conflitti:** Nel 2PL le transazioni vengono messe _in attesa_, il che può causare degli stalli critici (deadlock). Nel TS, invece, le transazioni vengono direttamente _uccise e riavviate_.
- **Conclusione pratica:** Poiché i riavvii forzati continui sono molto costosi in termini di prestazioni, l'approccio **2PL risulta generalmente più adatto** e preferito.