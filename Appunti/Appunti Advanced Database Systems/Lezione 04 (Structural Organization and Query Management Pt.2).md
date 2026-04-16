## 1. Strutture Fisiche di Accesso

Le strutture fisiche per l'organizzazione dei file stabiliscono come i record vengono memorizzati fisicamente nei blocchi del database per ottimizzarne l'accesso e la gestione. Si dividono principalmente in due categorie: **Strutture Sequenziali** e **Strutture basate su Hash**.

---

## 2. Strutture Sequenziali

Nelle strutture sequenziali esiste un preciso ordine tra le tuple, il quale può essere rilevante per le finalità di gestione. Si suddividono in tre tipologie principali:

- **Struttura Seriale (Heap o file disordinato):**
    
    - Presenta un ordinamento fisico ma non logico. È conosciuta anche come "Entry sequenced" o file heap.
        
    - È un'organizzazione molto diffusa nei database relazionali, specialmente in relazione agli indici secondari.
        
    - L'inserimento avviene "in linea" (in coda al file, con necessità di riorganizzazioni periodiche) o sfruttando lo spazio lasciato dai record cancellati.
        
    - **Ricerca:** Richiede necessariamente una scansione sequenziale di tutto il file, risultando poco efficiente.
        
    - Eliminazioni e modifiche sono rapide, ma tendono a produrre spreco di spazio (waste).
        
- **Struttura Sequenziale ad Array:**
    
    - Le posizioni delle tuple sono identificate tramite degli indici.
        
    - Può essere utilizzata **solo** quando le tuple in una tabella hanno una dimensione fissa (fixed size).
        
    - Sebbene sia efficiente per la ricerca, non viene quasi mai utilizzata nei DBMS.
        
- **Struttura Sequenziale Ordinata:**
    
    - L'archiviazione e l'ordinamento fisico dei record seguono coerentemente l'ordinamento di un campo specifico del database (es. ordine alfabetico).
        
    - Questa struttura permette di effettuare ricerche binarie, perlomeno fino a un certo punto (es. trovare la metà esatta del file).
        
    - Nei DBMS viene usata prevalentemente in combinazione con gli indici (ad esempio, file ISAM o file ordinati con indice primario).
        
    - Presenta dei problemi in caso di aggiornamenti (upgrades) e l'eliminazione dei record causa spreco di spazio.
        

---

## 3. Tabelle e Funzioni Hash

L'organizzazione hash permette un accesso diretto molto efficiente, ispirandosi alla tecnica delle "hash table" usate in memoria centrale.

- **Il problema dell'Accesso Diretto:** L'obiettivo è accedere direttamente a un set di record basandosi sul valore di un campo chiamato "chiave".
    
    - Se i valori _possibili_ della chiave sono numericamente paragonabili ai record effettivi (es. 1000 studenti con matricole da 1 a 1000), si può usare un semplice array.
        
    - Se invece i valori possibili sono molti di più di quelli realmente utilizzati (es. 40 studenti, ma matricole a 6 cifre che generano un milione di combinazioni possibili), l'uso dell'array diventa un enorme spreco di memoria.
        
- **Funzione Hash:** Per risolvere questo problema e risparmiare spazio, la funzione hash trasforma il valore della chiave associandogli un "indirizzo" la cui dimensione è paragonabile (o solo leggermente superiore) allo spazio strettamente necessario.
    
- **Collisioni:** Poiché lo spazio delle chiavi possibili è molto più grande dello spazio degli indirizzi, la funzione non è iniettiva: chiavi diverse possono corrispondere allo stesso indirizzo, generando le collisioni.
    
    - Una buona funzione hash distribuisce i dati in modo casuale e uniforme per ridurre la probabilità di collisioni.
        
    - Le collisioni, tuttavia, sono quasi sempre presenti. Possono essere gestite usando posizioni consecutive disponibili, tabelle di overflow (gestite in forma di liste concatenate) o funzioni hash alternative.
        
    - La probabilità di collisioni multiple decresce al crescere della molteplicità, mantenendo la media molto bassa.
        

---

## 4. File Hash nei DBMS

- **Dalla memoria centrale al disco:** L'idea del _file hash_ deriva dalla tabella hash, ma è basata su un'organizzazione a blocchi. Questo si rivela un ottimo metodo per "ammorbidire" la probabilità di collisioni.
    
    - _Esempio numerico:_ Con 40 record in una tabella hash da 50 posizioni si ha in media 1,425 accessi a causa delle collisioni. Spostandosi su un file hash con fattore di blocco pari a 10 (ovvero 5 blocchi da 10 posizioni ciascuno), si verificano solo 2 overflow e il numero medio di accessi crolla a **1,05**.
        
- **Vantaggi:** È l'organizzazione più efficiente in assoluto per l'accesso diretto basato su condizioni di uguaglianza (ricerche puntuali). Il costo medio è solo leggermente superiore a 1 accesso (il caso peggiore è molto costoso, ma talmente raro da poter essere ignorato).
    
- **Limiti del File Hash:**
    
    - Le collisioni (overflow) sono solitamente gestite concatenando blocchi (linked blocks).
        
    - Il limite principale è che **non è adeguata** per le ricerche basate su intervalli (es. da A a C) o per le ricerche basate su attributi diversi dalla chiave usata per l'hash.
        
    - Questa struttura degenera se lo spazio sovrabbondante viene ridotto: di conseguenza, funziona bene solo per i file la cui dimensione totale non varia molto nel tempo.