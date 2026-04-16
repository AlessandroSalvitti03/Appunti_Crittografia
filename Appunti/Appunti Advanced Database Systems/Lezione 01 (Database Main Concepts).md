## 1. Sistemi Informativi, Dati e Informazioni

- **Sistema Informativo:** È la componente di un'organizzazione che gestisce (acquisisce, elabora, memorizza e comunica) le informazioni di interesse. Ogni organizzazione possiede un sistema informativo, anche se non reso esplicito nella sua struttura. Sebbene il concetto stesso di sistema informativo sia parzialmente indipendente dalla sua informatizzazione, oggigiorno l'interesse è rivolto principalmente ai sistemi ampiamente computerizzati.
    
- **Gestione delle Informazioni:** Le informazioni vengono gestite e registrate attraverso diverse tecniche: idee informali, linguaggio naturale (scritto o parlato), disegni, diagrammi, numeri e codici. Con la sistematizzazione delle attività, sono state ideate forme appropriate di organizzazione e codifica (es. l'introduzione di struttura per il nome, data e luogo di nascita, e codici fiscali per l'identificazione univoca).
    
- **Dati vs Informazioni:** Nella maggior parte dei sistemi informatici, le informazioni sono rappresentate per mezzo di dati. I dati sono "fatti grezzi" che devono essere interpretati e correlati per fornire informazioni. Ad esempio, "John Smith" e "25755" sono due dati distinti (una stringa e un numero); ma se forniti in risposta alla domanda "Chi è il capo dipartimento e qual è il suo interno?", ne ricaviamo un'informazione.
    

![Data Information Knowledge Wisdom hierarchy, generata con l'AI](https://encrypted-tbn3.gstatic.com/licensed-image?q=tbn:ANd9GcSS5nTs8pC05NUGHpih2ywLsxMihe1iKzXx6JvhvubaFlbBy6tjPO5MUTy9NQArs-o9svXkz29CIr-qqn2PHEgzU9Qge66SGFm9EFCqH4AmcC3YS9U)

- **Il Valore dei Dati:** I dati sono una risorsa preziosa con un ciclo di vita molto lungo: le applicazioni bancarie, ad esempio, hanno dati con la stessa struttura da secoli, ben prima dell'invenzione dei computer.
    

## 2. Database e DBMS (DataBase Management System)

- **Database (Base di Dati):**
    
    - _Definizione generica:_ Una collezione di dati usata per rappresentare informazioni di interesse per un sistema informativo.
    - _Definizione tecnica:_ Una collezione di dati gestita da un DBMS.
        
- **DBMS:** È un sistema software capace di gestire collezioni di dati che sono:
    
    - **Grandi:** Spesso molto più grandi della memoria principale disponibile.
    - **Condivise:** Usate da diverse applicazioni e utenti.
    - **Persistenti:** Con una durata di vita non limitata alle singole esecuzioni dei programmi che le usano.
        
- **Obiettivi del DBMS:** Assicurare l'affidabilità (preservando il database in caso di guasti hardware o software), la privacy (controllando accessi e autorizzazioni), l'efficienza (usando la giusta quantità di risorse, come tempo e spazio) e l'efficacia (supportando la produttività degli utenti).
    
- **Condivisione dei Dati:** I dati di interesse dei vari dipartimenti di un'organizzazione spesso si sovrappongono; un database è una risorsa integrata e condivisa che permette di ridurre la ridondanza e la conseguente possibilità di incoerenza. La condivisione richiede il supporto per la privacy dei dati, le autorizzazioni di accesso e tecniche di controllo della concorrenza per organizzare accessi multipli.
    
- **DBMS vs File System:** I file system offrono strumenti più semplici per gestire dati grandi e persistenti, con un supporto grezzo per la condivisione. La differenza cruciale è che nei programmi tradizionali basati su file, ogni programma include una descrizione dell'organizzazione del file (rischiando incoerenze tra file e descrizioni), mentre nei DBMS esiste una porzione del database condivisa (chiamata dizionario o catalogo) che descrive il database stesso.

## 3. Modelli, Schemi e Istanze

- **Modelli dei Dati:** Sono insiemi di costrutti utilizzati per organizzare i dati. Si basano su meccanismi di strutturazione (costruttori di tipo), come il costruttore di relazione nel modello relazionale, che organizza i dati come insiemi di record omogenei (tabelle).
    
    - **Modelli Logici:** Usati nei DBMS per l'organizzazione dei dati a un livello che astrae dalle strutture fisiche (es. relazionale, a rete, gerarchico, a oggetti).
    - **Modelli Concettuali:** Usati per descrivere i dati in modo completamente indipendente da qualsiasi sistema, con l'obiettivo di rappresentare i concetti del mondo reale (es. il modello Entità-Relazione, usato nelle prime fasi di progettazione).
        
- **Schemi e Istanze:** All'interno di un database distinguiamo:
    
    - **Schema:** È piuttosto stabile nel tempo e descrive la struttura (componente _intensionale_); ad esempio, le intestazioni delle tabelle.
    - **Istanza:** Sono i valori effettivi, che variano anche molto rapidamente (componente _estensionale_); ad esempio, i corpi delle tabelle.
        

## 4. Architettura e Indipendenza dei Dati

- **Architettura ANSI/SPARC:** Un'architettura standard a tre livelli per i DBMS:
    
    1. **Schema Logico:** Descrizione dell'intero database tramite il modello logico adottato dal DBMS.
    2. **Schema Esterno:** Descrizione di una porzione del database in un modello logico ("viste", possibilmente in modelli diversi).
    3. **Schema Fisico (Interno):** Descrizione dell'implementazione dello schema logico mediante strutture di memorizzazione fisica.
        
- **Indipendenza dei Dati:** Garantita dall'architettura multilivello, che consente l'accesso solo tramite il livello esterno.
    
    - **Fisica:** I livelli logico ed esterno sono indipendenti da quello fisico; si fa riferimento a una relazione sempre nello stesso modo, indipendentemente dalla sua implementazione fisica (che può variare nel tempo).
    - **Logica:** Il livello esterno è indipendente da quello logico; l'aggiunta o la modifica di viste non richiede modifiche allo schema logico, e le modifiche allo schema logico non devono influenzare gli schemi esterni.
        

## 5. Linguaggi e Utenti

- **Linguaggi per Database:** Esistono in varie forme per contribuire all'efficacia:
    
    1. Linguaggi testuali interattivi, come SQL.
        
    2. Comandi interattivi incorporati (embedded) in un linguaggio host (Pascal, C, Cobol, Java, ecc.).
        
    3. Comandi incorporati in linguaggi di sviluppo ad-hoc (per produrre form, menu, report).
        
    4. Interfacce user-friendly non testuali (es. Microsoft Access).
        
- **Distinzione fondamentale dei linguaggi:**
    
    - **DDL (Data Definition Language):** Usato per definire gli schemi (logici, esterni e fisici) e le autorizzazioni di accesso.
    - **DML (Data Manipulation Language):** Usato per interrogare (query) e aggiornare le istanze del database.
        
- **Persone coinvolte:**
    
    - Progettisti e implementatori del DBMS.
        
    - Progettisti del database e amministratori di database (DBA).
        
    - Progettisti e sviluppatori di applicazioni.
        
    - **Utenti finali:** Usano transazioni predefinite (es. prenotazione voli o operazioni bancarie).
        
    - **Utenti casuali:** Emettono query tramite linguaggi interattivi o interfacce.
        

## 6. Vantaggi e Svantaggi dei DBMS

- **Pro (Vantaggi):**
    
    - I dati possono essere gestiti come una risorsa comune; il database è un modello del mondo reale.
    - Gestione centralizzata ed economia di scala.
    - Disponibilità di servizi integrati.
    - Riduzione delle ridondanze e delle incoerenze.
    - Indipendenza dei dati (un vantaggio nello sviluppo e nella manutenzione delle applicazioni).
        
- **Contro (Svantaggi):**
    
    - Costo del prodotto (e degli strumenti associati) e costo della migrazione.
    - Difficoltà nel separare funzionalità e servizi (con possibile mancanza di efficienza per specifiche necessità).