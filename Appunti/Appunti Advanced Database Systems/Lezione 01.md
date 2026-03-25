## 1. Introduzione ai Sistemi Informativi

Un **sistema informativo** è la componente di un'organizzazione che gestisce (raccoglie, elabora, memorizza e comunica) le informazioni di interesse per l'organizzazione stessa.

- Ogni organizzazione possiede un sistema informativo, anche se non esplicitato nella sua struttura.

- Il concetto di sistema informativo è indipendente dalla sua **informatizzazione**, sebbene i sistemi moderni siano in gran parte computerizzati.

- Le informazioni possono essere gestite in vari modi: linguaggio naturale, disegni, numeri o codici.


---

## 2. Dati vs. Informazioni

È fondamentale distinguere tra questi due concetti:

- **Dato:** Fatti grezzi che devono essere interpretati e correlati per fornire informazioni. Ad esempio, "Mario Rossi" e "12345" sono solo una stringa e un numero.

- **Informazione:** Il risultato dell'interpretazione dei dati in risposta a una specifica richiesta. Sapere che "Mario Rossi" è il capufficio con l'interno "12345" trasforma i dati in informazione.


---

## 3. Database e DBMS

## Definizioni

- **Database (Base di dati):** Una collezione di dati utilizzata per rappresentare le informazioni di interesse per un sistema informativo.

- **DBMS (Database Management System):** Un sistema software in grado di gestire collezioni di dati che siano:

    - **Grandi:** Spesso molto più capienti della memoria principale disponibile.

    - **Condivise:** Utilizzabili da diverse applicazioni e utenti contemporaneamente.

    - **Persistenti:** Con una vita che non si limita alla singola esecuzione dei programmi che le utilizzano.


## Caratteristiche del DBMS

Un DBMS deve garantire:

- **Affidabilità:** Preservare la base di dati anche in caso di guasti hardware o software.

- **Privatezza:** Controllo degli accessi e delle autorizzazioni.

- **Efficienza:** Utilizzo ottimale di tempo e spazio.

- **Efficacia:** Capacità di supportare la produttività degli utenti.


---

## 4. Modelli di Dati: Schemi e Istanze

Un **modello di dati** è un insieme di costrutti usati per organizzare i dati.

- **Schema:** Descrive la struttura della base di dati (componente _intensionale_) ed è stabile nel tempo. Esempio: le intestazioni di una tabella.

- **Istanza:** I valori effettivi contenuti nel database (componente _estensionale_), che possono cambiare rapidamente.


## Tipologie di Modelli

1. **Modelli Logici:** Utilizzati dai DBMS per astrarre dalle strutture fisiche (es. relazionale, reticolare, gerarchico, a oggetti).

2. **Modelli Concettuali:** Rappresentano i concetti del mondo reale in modo indipendente dal sistema (es. modello Entity-Relationship), usati nelle prime fasi della progettazione.


---

## 5. Architettura e Indipendenza dei Dati

L'architettura standard ANSI/SPARC prevede tre livelli per un DBMS:

1. **Schema Logico:** Descrizione dell'intera base di dati tramite il modello logico del DBMS.

2. **Schema Esterno:** Descrizione di una porzione del database ("viste") per specifici utenti.

3. **Schema Fisico:** Descrizione dell'implementazione tramite strutture di memorizzazione fisica.


Questa architettura garantisce l'**indipendenza dei dati**:

- **Fisica:** I livelli logico ed esterno non dipendono dall'implementazione fisica.

- **Logica:** Il livello esterno è indipendente dai cambiamenti dello schema logico.


---

## 6. Linguaggi e Ruoli

## Linguaggi per Database

- **DDL (Data Definition Language):** Usato per definire gli schemi e le autorizzazioni di accesso.

- **DML (Data Manipulation Language):** Usato per interrogare (query) e aggiornare le istanze della base di dati. Il linguaggio testuale più diffuso è **SQL**.


## Attori (Persone)

- Progettisti e implementatori del DBMS.

- **DBA (Database Administrator):** Amministratori e progettisti del database.

- Sviluppatori di applicazioni.

- **Utenti finali:** Usano transazioni predefinite.

- **Utenti casuali:** Utilizzano linguaggi interattivi per query personalizzate.