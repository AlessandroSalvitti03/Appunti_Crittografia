## 1. Cos'è una Transazione?

Una **transazione** è un'unità di programma caratterizzata da un inizio (start/begin) e una fine, all'interno della quale deve essere eseguito uno dei seguenti comandi per determinare l'esito:

- **Commit work:** Indica che l'operazione è terminata con successo e le modifiche diventano permanenti.
    
- **Rollback work (o Abort):** Indica il fallimento dell'operazione; il sistema deve annullare ogni modifica fatta per riportare il database allo stato iniziale.
    

## Esempio Pratico

Un classico esempio è il trasferimento di fondi tra conti bancari: se il sistema aggiorna il primo conto (sottraendo denaro) ma crasha prima di aggiornare il secondo, la transazione deve essere annullata interamente per evitare la perdita di denaro.

---

## 2. Le Proprietà ACID

Il TPS deve garantire quattro proprietà fondamentali, note con l'acronimo **ACID**:

- **Atomicità:** La transazione è un'unità indivisibile; o viene eseguita interamente ("tutto") o non viene eseguita affatto ("nulla").
    
- **Coerenza (Consistency):** L'esecuzione di una transazione deve portare il database da uno stato corretto a un altro stato corretto, rispettando tutti i vincoli di integrità.
    
- **Isolamento (Isolation):** Ogni transazione deve essere eseguita come se fosse l'unica nel sistema; gli effetti di una transazione in corso non devono essere visibili alle altre finché non avviene il commit.
    
- **Durabilità (Persistence):** Una volta confermata (commit), i cambiamenti devono essere permanenti e sopravvivere anche a successivi guasti del sistema.
    

---

## 3. Il Controllo di Affidabilità e il Log

Il sistema di controllo di affidabilità assicura l'atomicità e la durabilità gestendo il recupero dopo i guasti (warm o cold restart). Lo strumento principale utilizzato è il **Log**.

## Il Log (Il "Giornale di Bordo")

È un file sequenziale scritto in **memoria stabile** (dischi replicati che non possono essere danneggiati) che registra cronologicamente tutte le operazioni effettuate:

- **Operazioni di transazione:** Inizio ($B$), Inserimento ($I$), Cancellazione ($D$), Aggiornamento ($U$), Commit ($C$) o Abort ($A$).
    
- **Record di sistema:** Operazioni di checkpoint e dump.
    

Per ogni modifica, il log registra lo stato dell'oggetto prima (**Before State - BS**) e dopo (**After State - AS**) l'operazione.

---

## 4. Operazioni di Recupero: Undo e Redo

In caso di guasto, il sistema utilizza il log per riportare il database in uno stato coerente tramite due operazioni:

- **Undo:** Annulla le operazioni di una transazione che non ha completato il commit, scrivendo il _Before State_ (BS) sugli oggetti modificati.
    
- **Redo:** Ripete le operazioni di una transazione che aveva già fatto commit ma le cui modifiche potrebbero non essere state scritte fisicamente su disco, usando l' _After State_ (AS).
    

Queste operazioni sono **idempotenti**: eseguirle più volte produce lo stesso risultato di eseguirle una sola volta, garantendo la sicurezza durante il ripristino.

---

## 5. Checkpoint e Dump

Per evitare di dover rileggere l'intero log dall'inizio dei tempi dopo ogni crash, si usano due procedure:

- **Checkpoint:** Un'operazione periodica che sospende temporaneamente l'accettazione di nuove operazioni, forza la scrittura su disco di tutte le pagine "sporche" (modificate) delle transazioni già confermate e registra quali transazioni sono ancora attive in quel momento. Questo accorcia drasticamente i tempi di recupero.
    
- **Dump:** Una copia completa (backup) dell'intero database salvata in memoria stabile, solitamente eseguita quando il sistema non è operativo.