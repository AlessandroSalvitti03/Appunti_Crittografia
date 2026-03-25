Questa seconda lezione approfondisce la **tecnologia alla base dei server database**, spiegando come il DBMS gestisce fisicamente i dati tra memoria principale e secondaria e come organizza i record all'interno dei blocchi.

---

## 1. Architettura e Gestione delle Query

Il DBMS non è una "scatola nera": per utilizzarlo al meglio, occorre capire come elabora le richieste. L'architettura di gestione segue una gerarchia precisa che va dal linguaggio ad alto livello (SQL) alla memorizzazione fisica:

1. **Queries Management:** Traduce l'SQL in operazioni di scansione, accesso diretto o ordinamento.

2. **Access Methods Management:** Gestisce le modalità di accesso ai dati.

3. **Buffer Management:** Si occupa del trasferimento dei dati tra disco e RAM.

4. **Secondary Memory Management:** Gestisce la scrittura fisica sui dispositivi di memorizzazione.


---

## 2. Memoria Principale vs. Memoria Secondaria

I database sono **Grandi** e **Persistenti**, il che rende necessaria la memoria secondaria (disco).

- **Limiti del Disco:** L'accesso alla memoria secondaria è circa **10.000 volte più lento** rispetto alla RAM. Un accesso medio richiede almeno 10 ms (posizionamento testina, latenza, trasferimento).

- **Unità di misura:** La memoria secondaria è strutturata in **blocchi** (o pagine) di lunghezza fissa (solitamente KB).


---

## 3. Gestione del Buffer

Il **Buffer** è un'area della memoria centrale pre-allocata dal DBMS e condivisa tra le transazioni. Il suo scopo principale è **ridurre il numero di accessi al disco**.

Primitive del Buffer Manager

- **fix:** Richiede una pagina; se non è nel buffer, la legge dal disco.

- **unfix:** Indica che la transazione ha finito di usare la pagina.

- **setDirty:** Segnala che la pagina è stata modificata e dovrà essere scritta su disco.

- **force:** Forza la scrittura immediata e sincrona della pagina su disco (usato per l'affidabilità).


## Politiche di rimpiazzamento (Steal vs. No-Steal)

Quando il buffer è pieno e serve una nuova pagina:

- **Steal:** Il manager sceglie una "vittima" (una pagina con contatore a zero), ne scrive il contenuto su disco se necessario e libera lo spazio.

- **No-Steal:** L'operazione viene messa in attesa finché non si libera spazio naturalmente.


---

## 4. Organizzazione Fisica dei Record

Il **Block Factor** indica quanti record possono stare in un singolo blocco. Se rimane spazio residuo, questo può essere usato (record _spanned_) o lasciato vuoto (_unspanned_).

## Strutture di Accesso Primarie

Le tabelle possono essere organizzate fisicamente in diversi modi:

|**Struttura**|**Descrizione**|**Pro / Contro**|
|---|---|---|
|**Seriale (Heap)**|Record inseriti senza un ordine logico.|Inserimento rapido, ma la ricerca richiede una scansione sequenziale completa.|
|**Sequenziale Ordinata**|Record ordinati fisicamente in base a un campo.|Permette ricerche binarie, ma gli aggiornamenti sono molto costosi.|
|**Hash File**|Usa una funzione hash per calcolare l'indirizzo del blocco in base a una chiave.|**Ottima per ricerche puntuali** (uguaglianza), ma inefficiente per ricerche su intervalli.|

---

## 5. Il Problema delle Collisioni nell'Hash

Poiché lo spazio delle chiavi è molto più grande di quello degli indirizzi, due chiavi diverse possono finire nello stesso blocco (**collisione**).

- Nei **file hash**, la probabilità di collisione è mitigata dal fatto che ogni "indirizzo" corrisponde a un intero blocco (che può contenere più record), riducendo drasticamente gli accessi extra.

- Se un blocco è pieno, si usano solitamente **blocchi di overflow** concatenati.