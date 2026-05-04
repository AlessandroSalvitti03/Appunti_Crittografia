### **1. Classificazione delle Transazioni e Problema dell'Atomicità** 

Le operazioni in un database distribuito si classificano in base a complessità e destinazione:

- **Remote requests:** Query di sola lettura rivolte a un singolo DBMS remoto.
- **Remote transactions:** Comandi SQL (select, insert, delete, update) indirizzati a un singolo DBMS remoto (scrive su un solo DBMS).
- **Distributed transactions:** Comandi SQL indirizzati a più DBMS remoti, ma ogni singolo comando si riferisce a un solo DBMS per volta. Aggiornano più database e richiedono il protocollo _Two-Phase Commit_.
- **Distributed requests:** Transazioni arbitrarie dove un singolo comando SQL può coinvolgere più DBMS simultaneamente; richiedono un ottimizzatore distribuito.
- _L'importanza dell'Atomicità:_ In una transazione distribuita (es. un bonifico bancario tra due filiali diverse), eseguire una modifica senza completare l'altra è una violazione inaccettabile dell'atomicità.

### **2. Le Proprietà ACID nell'Esecuzione Distribuita** 
La distribuzione dei dati non influisce direttamente su molte delle proprietà ACID, poiché i sistemi locali offrono le loro garanzie, ma l'atomicità globale resta una sfida:

- **Coerenza (Consistency):** Se ogni sotto-transazione preserva l'integrità locale, i dati sono globalmente coerenti.
- **Durabilità (Durability):** Ogni DBMS garantisce la persistenza locale tramite i propri meccanismi (log, checkpoint, dump).
- **Isolamento (Isolation):** Se ogni sotto-transazione usa le due fasi (es. 2PL), la transazione è globalmente serializzabile.
- **Atomicità:** È il **problema principale** delle transazioni distribuite e richiede un protocollo di controllo dedicato.

### **3. Ottimizzazione delle Query e Concorrenza Globale**

- **Ottimizzatore Distribuito:** Scompone una richiesta distribuita in sotto-query, costruendo una strategia di esecuzione coordinata. Il costo totale deve tenere conto della quantità di dati trasmessi sulla rete, oltre ai normali costi di I/O e CPU.
- **Serializzabilità Globale:** La serializzabilità locale non è sufficiente a garantire quella globale (due nodi potrebbero avere conflitti in ordine inverso). Per ottenerla, occorre che ogni nodo utilizzi il metodo _Strict Two-Phase Locking (2PL)_ o che ogni transazione riceva un _Timestamp_ ad ordinamento globale.
- **Deadlock Distribuiti:** Possono sorgere situazioni di attesa circolare tra due o più nodi. Vengono solitamente risolti tramite il metodo del **time-out**.

Nei database distribuiti, a differenza di quelli centralizzati, il calcolo non si limita alle consuete operazioni su disco o processore locale, ma deve includere in modo critico la **quantità di dati trasmessi sulla rete**.

La formula per calcolare il costo totale presentata nei documenti è la seguente:

	Ctotal =CI/O x nI/O + Ccpu x ncpu + Ctrx ntr

Ecco nel dettaglio i tre fattori di costo che la compongono:

- **Fattore I/O (**CI/O​×nI/O​**)**: Rappresenta il costo delle operazioni fisiche di lettura/scrittura su disco, calcolato moltiplicando il costo unitario di Input/Output (CI/O​) per il numero di operazioni effettuate (nI/O​).
- **Fattore CPU (**Ccpu​×ncpu​**)**: Rappresenta il costo di elaborazione del processore locale, calcolato moltiplicando il costo unitario per la CPU (Ccpu​) per il numero di operazioni eseguite (ncpu​).
- **Fattore di Rete (**Ctr​×ntr​**)**: È il **fattore di costo cruciale e specifico** per i sistemi distribuiti. Indica il costo di trasferimento dei dati, dove ntr​ rappresenta la quantità di dati che vengono effettivamente trasmessi sulla rete e Ctr​ è il costo unitario di questa trasmissione.

### **4. Gestione dei Guasti di Rete** 

I sistemi distribuiti devono affrontare tre tipi di problemi di comunicazione:

1. _Guasti ai nodi_ (hardware o software).
2. _Perdita di messaggi_, che lascia mittente e destinatario in uno stato incerto sulla ricezione (ogni messaggio inviato richiede di norma un messaggio di "ack" di ritorno).
3. _Partizionamento della rete_, che divide la rete in due o più sotto-reti che non riescono a comunicare tra loro.

### **5. Il Protocollo Two-Phase Commit (2PC)** 

È il protocollo che permette di raggiungere una decisione unanime (commit o abort) in tutti i nodi. Funziona come un "matrimonio": i server partecipanti sono i **Resource Managers (RM)**, mentre il coordinatore è il **Transaction Manager (TM)**. Si articola in due fasi:

- **Prima Fase (Preparazione):** Il TM scrive il record _prepare_ sul log e chiede ai partecipanti se sono pronti. I RM recuperabili, dopo aver preso i lock sulle risorse, scrivono _ready_ sul log e rispondono positivamente. Se anche solo un RM non è pronto, invia _not-ready_ e il TM deciderà per il _global abort_.

- **Seconda Fase (Decisione):** Se tutti gli RM hanno risposto _ready_, il TM scrive _global commit_ e ordina l'esecuzione. Gli RM eseguono, scrivono nel proprio log e inviano una conferma (_ack_). Quando riceve tutti gli _ack_, il TM scrive il record finale _complete_.

- **Finestra di Incertezza:** È l'intervallo di tempo (che si cerca di rendere minimo) tra il momento in cui l'RM si dichiara pronto (_ready_) e il momento in cui riceve la decisione finale. In questa fase l'RM perde la sua autonomia e le sue risorse rimangono bloccate.

### **6. Recovery (Ripristino) nel Protocollo 2PC**

- **Ripristino del Partecipante (RM):** Tramite _Warm Restart_, se l'RM trova un'azione "sporca" o un abort fa _Undo_; se trova un commit fa _Redo_. Se il log si interrompe col record _ready_, la transazione è "in dubbio": l'RM deve interrogare il TM per scoprire la decisione finale.

- **Ripristino del Coordinatore (TM):** Se il TM cade dopo il _prepare_, decide per l'abort globale o ripete la prima fase. Se cade dopo la _decisione globale_, ma prima del _complete_, ripete l'invio della decisione (fase 2) per i nodi bloccati.

- **Time-out per perdita messaggi:** Il TM usa i time-out per capire se si è perso un messaggio o se un nodo è caduto. Nella fase 1 un time-out porta all'abort; nella fase 2 porta a ripetere l'invio della decisione.

### **7. Ottimizzazioni del 2PC**

- **Protocollo "Presumed Abort":** Se il TM viene interrogato su una transazione che non ha in memoria, risponde di default "global abort". Questo permette di evitare di dover scrivere obbligatoriamente su disco in modo sincrono i log di _prepare_ e _global abort_, risparmiando risorse.
- **Ottimizzazione "Read-only":** Se un RM ha effettuato solo letture, risponde al _prepare_ con un messaggio "read-only" ed esce direttamente dal protocollo, venendo ignorato dal TM nella fase 2.



### **8. Extra**
**Blocking, Uncertainty e Recovery Protocols** nel contesto delle transazioni distribuite e del Two-Phase Commit (2PC):

**1. Blocco (Blocking) e Stato di Incertezza (Uncertainty)**

- **Perdita di autonomia:** Durante il protocollo 2PC, quando un partecipante (Resource Manager - RM) si dichiara pronto e scrive il record `ready` sul log, perde la propria autonomia decisionale e deve attendere la decisione finale del coordinatore (Transaction Manager - TM).
- **Blocco delle risorse:** Se il TM subisce un guasto in questa specifica fase, l'RM viene lasciato in uno "stato di incertezza" (uncertain state). Di conseguenza, tutte le risorse del database su cui l'RM aveva acquisito i lock rimangono bloccate, impedendo ad altre transazioni di accedervi.
- **Finestra di incertezza (Window of uncertainty):** Viene definito così l'intervallo di tempo che intercorre tra la scrittura del record `ready` sul log dell'RM e la scrittura della decisione finale (`commit` o `abort`). Il protocollo 2PC è progettato appositamente per mantenere questo intervallo il più breve possibile.

**2. Protocolli di Ripristino (Recovery Protocols)** I protocolli di ripristino vengono eseguiti dal TM o dall'RM a seguito di un guasto (fallimento di un nodo o perdita di messaggi) per recuperare uno stato finale coerente basato sulla decisione globale del TM.

**3. Ripristino dei Partecipanti (RM) tramite Warm Restart** La procedura di ripristino di un RM dipende dall'ultimo record che è riuscito a scrivere nel proprio log prima del guasto:

- **Ultimo record = Azione generica o Abort:** Significa che il guasto è avvenuto _prima_ dell'inizio del protocollo di commit. Le azioni della transazione vengono annullate (Undo).
- **Ultimo record = Commit:** Il guasto è avvenuto _dopo_ la decisione finale. Le azioni della transazione vengono ripetute (Redo) per garantirne la persistenza.
- **Ultimo record = Ready:** Il guasto è avvenuto nel pieno del protocollo 2PC e l'RM è "in dubbio" (in doubt) sul risultato della transazione. Durante il riavvio, gli identificatori di queste transazioni in dubbio vengono raccolti nel cosiddetto _ready set_. Per sbloccarsi, l'RM deve chiedere l'esito finale direttamente al TM (tramite una richiesta di _remote recovery_) oppure attendere che il TM ripeta autonomamente la seconda fase del protocollo.

**4. Ripristino del Coordinatore (TM)** Anche il ripristino del TM dipende dall'ultimo record presente nel suo log:

- **Ultimo record = Prepare:** Il guasto del TM potrebbe aver lasciato alcuni RM in uno stato bloccato. Il TM al riavvio ha due opzioni: scrivere `global abort` sul log e avviare la seconda fase per liberare tutti, oppure ripetere la prima fase cercando di ottenere un `global commit`.
- **Ultimo record = Global Decision (Commit/Abort):** La decisione è stata presa, ma essendoci stato un guasto prima del record finale `complete`, alcuni RM potrebbero non essere stati informati ed essere rimasti bloccati. Il TM deve obbligatoriamente ripetere la trasmissione della decisione (seconda fase).

**5. Gestione della Perdita di Messaggi e Partizionamento di Rete**

- **Perdita di messaggi Prepare o Ready:** Il TM non è in grado di distinguere quale dei due messaggi si sia perso; in entrambi i casi il time-out della prima fase scade e il TM prende autonomamente la decisione di `global abort`.
- **Perdita di messaggi Decision o Ack:** Anche qui il TM non distingue la differenza; allo scadere del time-out della seconda fase, si limiterà a ripetere l'invio della decisione per assicurarsi che tutti i nodi la ricevano.
- **Partizionamento della Rete:** Se la rete si divide isolando dei nodi, la transazione potrà concludersi con successo solo ed esclusivamente se il TM e _tutti_ gli RM coinvolti si trovano all'interno della stessa partizione comunicante.