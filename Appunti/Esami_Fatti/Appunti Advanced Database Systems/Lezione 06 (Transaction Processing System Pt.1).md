### **1. Il Concetto di Transazione**

- **Definizione:** Una transazione è una porzione di programma delimitata da un inizio (`begin transaction` o `start transaction`) e una fine, all'interno della quale deve essere eseguito uno e un solo comando di terminazione: `commit work` (per completare l'operazione con successo) o `rollback work` (per interromperla o abortire).

- **Applicazione vs Transazione:** Un'applicazione (es. un programma informatico) può contenere al suo interno diverse transazioni. I sistemi OLTP (On Line Transaction Processing) hanno il compito di gestire l'esecuzione di transazioni per conto di svariate applicazioni concorrenti.

### **2. Le Proprietà "ACID"** 
Una transazione è un'unità di elaborazione che deve garantire quattro proprietà fondamentali:

- **Atomicity (Atomicità):** La transazione è un'unità indivisibile (tutto o niente). Non può lasciare il database in uno stato intermedio. Un guasto prima del commit richiede l'annullamento delle operazioni fatte (_UNDO_), mentre un guasto dopo il commit richiede, se necessario, la loro ripetizione (_REDO_). L'abort (l'annullamento) può essere volontario ("suicidio") o imposto dal sistema per violazioni o problemi ("omicidio").

- **Consistency (Coerenza):** La transazione deve rispettare i vincoli di integrità del database. Se lo stato di partenza è corretto, anche quello finale dovrà esserlo.

- **Isolation (Isolamento):** L'esecuzione di una transazione non deve subire influenze dalle altre transazioni concorrenti. L'esecuzione concorrente deve portare allo stesso risultato di un'esecuzione puramente sequenziale. Non si devono esporre stati intermedi, evitando così l'"effetto domino".

- **Durability (Durabilità/Persistenza):** Gli effetti di una transazione confermata col commit ("impegno") sono permanenti e non vanno persi nemmeno in presenza di guasti di sistema.

### **3. Sistema di Controllo dell'Affidabilità e Memorie** 
Il modulo del database che si occupa del controllo di affidabilità garantisce l'atomicità e la durabilità gestendo l'esecuzione dei comandi di transazione e le procedure di ripristino in caso di guasto (warm e cold restart). L'architettura prevede tre tipologie di memoria:

- **Memoria Principale:** Veloce ma non persistente (volatile).
- **Memoria di Massa (Secondaria):** Persistente ma vulnerabile ai danni fisici.
- **Memoria Stabile:** Una memoria concettuale "invulnerabile", che si ottiene fisicamente tramite tecniche di ridondanza (es. dischi replicati, nastri).

### **4. Il Log: La "Scatola Nera" del Database**
Il sistema si affida al **Log**, un file sequenziale memorizzato nella memoria stabile che funge da "giornale di bordo". Esso traccia le operazioni delle transazioni (begin, insert, update, delete, commit, abort) registrando il valore del dato prima della modifica (_Before State_ o _BS_) e dopo la modifica (_After State_ o _AS_), insieme alle operazioni di sistema come _dump_ e _checkpoint_. Ci sono due regole cruciali per la sua scrittura:

- **Write-Ahead-Log (WAL):** Impone che la porzione _Before-State_ sia scritta nel log **prima** di effettuare l'operazione sui dati nel database; questo assicura la possibilità di fare un'azione di _UNDO_.

- **Commit-Precedence:** Impone che la porzione _After-State_ venga scritta nel log **prima** di eseguire il commit; questo assicura la possibilità di fare un'azione di _REDO_.

### **5. Modalità di Scrittura nel Database** 
Il momento in cui i dati vengono effettivamente trasferiti nel DB varia:

- **Modo Immediato:** Il database può contenere valori da transazioni non ancora "committate". In caso di guasto è indispensabile fare **Undo**, ma non serve il Redo.

- **Modo Differito:** Il database viene aggiornato solo dopo il commit. Non serve fare Undo, ma in caso di guasto è indispensabile il **Redo**.

- **Modo Misto:** Unisce le due tecniche per ottimizzare le operazioni e richiede sia Undo che Redo (che ricordiamo essere operazioni _idempotenti_, ovvero si ottiene sempre lo stesso risultato sia se eseguite una che più volte).

### **6. Checkpoint e Dump**

- **Checkpoint:** È un'operazione periodica (simile a una "chiusura dei conti" contabile). Sospende l'accettazione di nuove operazioni, salva nella memoria di massa tutte le pagine modificate da transazioni concluse, registra in modo sincrono le transazioni attualmente attive ("a metà") e poi riprende l'attività. Semplifica notevolmente i tempi di recupero dai guasti.
- **Dump:** È una copia di backup completa del database, generalmente creata quando il sistema non è operativo, e conservata in memoria stabile.

### **7. Guasti e Procedure di Ripristino (Restart)** 
Basandosi su un modello di guasto "Fail-stop", si distinguono:

- **Soft failures (Guasti leggeri):** Come crash del sistema o cali di tensione, dove si perde solo la memoria principale. Si risolvono con il **Warm Restart**, strutturato in 4 fasi:
    1. Si cerca l'ultimo checkpoint risalendo all'indietro nel log.
    2. Si costruiscono gli insiemi UNDO (transazioni da annullare) e REDO (transazioni completate da ripetere).
    3. _Fase UNDO_: Si ripercorre il log all'indietro, annullando le azioni delle transazioni nell'insieme UNDO.
    4. _Fase REDO_: Si ripercorre il log in avanti, rieseguendo le azioni delle transazioni nell'insieme REDO.

- **Hard failures (Guasti gravi):** Danni alla memoria di massa, dove sopravvive solo la memoria stabile (e quindi il log). Richiedono il **Cold Restart**: si ripristina il database dall'ultimo _dump_ (backup), si riapplicano in avanti tutte le operazioni scritte nel log fino al momento del guasto, per poi completare con la procedura di _Warm Restart_ per sistemare lo stato esatto.

___________________________________________________________________

## Riepilogo Regole d'Oro

Indipendentemente dalla linea temporale, il DBMS rispetta sempre queste due regole critiche:

1. **WAL (Write-Ahead-Log)**: Il record di log ($U$) deve sempre precedere la scrittura nel DB ($w$) per permettere l'UNDO.
    
2. **Commit-Precedence**: Tutti i record di log ($AS$) devono essere scritti prima di considerare valido il Commit, per permettere il REDO.


![[Pasted image 20260326123203.png]]
## La Sequenza del Log (Esempio delle Slide)

Immaginiamo che il Log contenga le seguenti operazioni cronologiche prima del crash:

1. $B(T1), B(T2)$ (Inizio transazioni)
2. $U(T2, ...), I(T1, ...)$ (Operazioni sui dati)
3. $B(T3), C(T1)$ (**Commit di T1**)
4. $B(T4), U(T3, ...), U(T4, ...)$
5. **$CK(T2, T3, T4)$ (Punto di Checkpoint)**: registra che $T2, T3$ e $T4$ sono attive.
6. $C(T4)$ (**Commit di T4**)
7. $B(T5), U(T3, ...), U(T5, ...), D(T3, ...)$
8. $A(T3)$ (**Abort di T3**)
9. $C(T5)$ (**Commit di T5**)
10. $I(T2, ...)$ (Ultima operazione di T2 prima del Crash)
11. **CRASH**.

---

## Fase 1: Ricerca dell'ultimo Checkpoint

Il sistema ripercorre il Log a ritroso partendo dalla fine per trovare l'ultimo checkpoint registrato. In questo caso, trova $CK(T2, T3, T4)$. Grazie al checkpoint, sappiamo che al momento della sua registrazione le transazioni $T2, T3$ e $T4$ erano in corso.

## Fase 2: Costruzione degli insiemi UNDO e REDO

Il sistema analizza il log dal checkpoint in avanti per classificare le transazioni:

- **Inizializzazione**: L'insieme **UNDO** contiene inizialmente le transazioni attive al checkpoint $\{T2, T3, T4\}$. Il **REDO** è vuoto.
    
- **Scansione in avanti**:
    
    - $C(T4)$: $T4$ ha completato il commit, quindi viene spostata da UNDO a **REDO**.
        
    - $B(T5)$: $T5$ inizia, viene aggiunta a **UNDO**.
        
    - $A(T3)$: $T3$ ha fallito (abort), quindi rimane in **UNDO** per essere annullata.
        
    - $C(T5)$: $T5$ ha completato il commit, quindi viene spostata da UNDO a **REDO**.
        
- **Risultato finale**:
    
    - **$UNDO = \{T2, T3\}$**: transazioni mai confermate o fallite.
        
    - **$REDO = \{T4, T5\}$**: transazioni confermate dopo il checkpoint.
        
    - Nota: T1 viene ignorata perché il commit è avvenuto prima del checkpoint, quindi i suoi dati sono già sicuri su disco.
        

---

## Fase 3: Esecuzione dell'UNDO (All'indietro)

Il sistema ripercorre il log all'indietro fino alla prima azione della transazione più vecchia in UNDO. Per ogni operazione di $T2$ e $T3$, scrive nel database il valore precedente (**Before State - BS**):

1. **Undo $I(T2, O6)$**: cancella l'oggetto $O6$ inserito.
    
2. **Undo $D(T3, O5)$**: ripristina $O5$ con il valore $B7$.
    
3. **Undo $U(T3, O3)$**: ripristina $O3$ con il valore $B5$.
    
4. **Undo $U(T3, O2)$**: ripristina $O2$ con il valore $B3$.
    
5. **Undo $U(T2, O1)$**: ripristina $O1$ con il valore $B1$.
    

## Fase 4: Esecuzione del REDO (In avanti)

Infine, il sistema ripercorre il log in avanti e ripete tutte le azioni delle transazioni in REDO per assicurarsi che siano scritte permanentemente. Scrive nel database il valore finale (**After State - AS**):

1. **Redo $U(T4, O3)$**: imposta $O3$ al valore $A4$.
    
2. **Redo $U(T5, O4)$**: imposta $O4$ al valore $A6$.
    

Al termine di queste fasi, il database è di nuovo in uno stato **coerente**.

![[Pasted image 20260326123351.png|391]]
