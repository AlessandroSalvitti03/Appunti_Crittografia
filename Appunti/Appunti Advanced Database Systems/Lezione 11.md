### **1. Motivazioni e Limiti dei Database Relazionali**

- **Database Relazionali (SQL):** Si basano su tabelle strettamente strutturate, uno schema predefinito rigido e sul processo di normalizzazione per ridurre le ridondanze. Supportano la scalabilità verticale (potenziamento hardware del singolo nodo) e garantiscono le rigorose proprietà ACID per l'affidabilità delle transazioni.
- **Le motivazioni verso NoSQL:** Nascono per gestire la natura dei moderni sistemi informatici: necessità di database distribuiti, gestione di **Big Data** (caratterizzati dalle 3 V: elevato Volume, Velocità e Varietà), e l'esigenza di una **scalabilità dinamica e orizzontale** (aggiungere più server invece di potenziarne uno solo).

### **2. Il Teorema CAP e le Proprietà BASE** 

Nei sistemi distribuiti è impossibile garantire la perfezione su tutti i fronti.

- **Teorema CAP:** Afferma che un sistema distribuito può garantire al massimo due di queste tre proprietà:
    - _Consistency_ (Coerenza): Tutti i nodi vedono gli stessi dati contemporaneamente.
    - _Availability_ (Disponibilità): Il sistema risponde sempre a ogni richiesta.
    - _Partition Tolerance_ (Tolleranza alle partizioni): Il sistema funziona anche se la rete si divide o ci sono guasti di comunicazione tra i nodi.
    - I sistemi SQL scelgono CA (usando il costoso Two-Phase Commit), mentre i NoSQL scelgono CP (es. MongoDB, Redis) o AP (es. Cassandra, CouchDB).

- **Proprietà BASE:** Invece di ACID, i sistemi NoSQL adottano un approccio più "morbido":
    - **BA (Basically Available):** Il sistema risponde sempre, tollerando fallimenti parziali.
    - **S (Soft state):** Lo stato del database può variare nel tempo a causa della propagazione asincrona.
    - **E (Eventual consistency):** I dati diventeranno coerenti in tutti i nodi alla fine, sebbene non lo siano nell'immediato.

### **3. Caratteristiche e Modelli dei Database NoSQL**

- **Assenza di JOIN:** I NoSQL favoriscono modelli di dati denormalizzati e non supportano le JOIN, poiché richiederebbero complesse e lente operazioni di coordinamento tra nodi distribuiti. Spetta all'applicazione unire i dati quando necessario.

- **I 4 Modelli Dati NoSQL principali:**
    1. **Key-Value** (es. Redis, DynamoDB): Dati memorizzati come semplici coppie `chiave-valore`. Il valore è trattato come una "scatola nera" non interrogabile per la ricerca; si accede solo tramite la chiave.
    2. **Document-oriented** (es. MongoDB, CouchDB): I dati sono organizzati in documenti flessibili (come JSON/XML) raccolti in collezioni. Supportano interrogazioni complesse sui campi nidificati.
    3. **Column-oriented** (es. Cassandra): Dati salvati per righe, ma ogni riga può avere colonne diverse senza uno schema fisso. Ottimo per serie storiche e big data.
    4. **Graph-oriented** (es. Neo4j): Dati strutturati in Nodi (entità) e Archi (relazioni). Ideali per dati altamente connessi come i social network.