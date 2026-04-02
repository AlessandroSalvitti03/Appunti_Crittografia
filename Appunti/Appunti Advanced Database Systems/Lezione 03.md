## 1. Introduzione alla Tecnologia dei DBMS

- **Perché studiarla:** Un DBMS offre i suoi servizi in modo trasparente, tanto che gli aspetti implementativi vengono spesso ignorati e il sistema viene visto come una "scatola nera". Capire come funziona al suo interno, tuttavia, è utile per ottenere un utilizzo migliore e perché alcuni tipi di servizi vengono offerti separatamente.
    
- **Caratteristiche dei database:** Sono collezioni di dati grandi (di dimensioni maggiori rispetto alla memoria principale del computer utilizzato) , persistenti (con un ciclo di vita indipendente dall'esecuzione del singolo programma che li usa) e condivisi (utilizzati da diverse applicazioni e diversi utenti).
    
- **Garanzie del DBMS:** Il sistema deve assicurare efficacia (aumentando la produttività delle attività dell'utente) , efficienza (gestione ottimizzata di tempi e memoria) , affidabilità (resistenza ai malfunzionamenti hardware e software) e privacy (regolamentazione del controllo e degli accessi).
    

## 2. Affidabilità e Condivisione (Gestione delle Transazioni)

- **Affidabilità:** I database sono una risorsa e devono essere preservati anche in presenza di malfunzionamenti. Ad esempio, in un trasferimento di fondi tra due conti bancari che subisce un guasto, le transazioni devono essere atomiche (tutte le azioni devono essere eseguite o nessuna) e finali (dopo l'azione, le modifiche non scompaiono). Mantenere l'affidabilità è un compito impegnativo a causa dei frequenti aggiornamenti e della necessità di gestire il buffer.
    
- **Condivisione e Concorrenza:** Essendo una risorsa integrata e condivisa tra diverse applicazioni e utenti , il DBMS genera la presenza di diverse attività su dati condivisi. Questo richiede meccanismi di autorizzazione e controlli di concorrenza. Ad esempio, due prelievi quasi simultanei sullo stesso conto sarebbero corretti se eseguiti in modo seriale. Tuttavia, nei sistemi reali, l'efficienza sarebbe troppo penalizzata se le transazioni fossero seriali: i controlli di concorrenza e affidabilità consentono un compromesso ragionevole.
    

## 3. Memoria Principale vs Memoria Secondaria

- **Differenze chiave:** I programmi possono fare riferimento solo ai dati in memoria principale. I database devono risiedere nella memoria secondaria per due motivi: dimensione e persistenza. I dati in memoria secondaria possono essere utilizzati solo se trasferiti alla memoria principale.
    
- **Prestazioni e Latenza:** I dispositivi di memoria secondaria sono strutturati in blocchi di lunghezza fissa (ordine di grandezza dei KB). Le uniche operazioni disponibili sono solo la lettura e la scrittura di una pagina (i termini blocco e pagina sono sinonimi). L'accesso è lento: il tempo di posizionamento della testina (10-50 ms), la latenza (5-10 ms) e il tempo di trasferimento (1-2 ms) rendono l'accesso medio non inferiore a 10 ms. Il costo di accesso è quattro o più ordini di grandezza maggiore rispetto a un'operazione in memoria principale. Nelle applicazioni "I/O bound", il costo dipende esclusivamente dal numero di accessi alla memoria secondaria, e l'accesso a blocchi "vicini" (contiguità) ha un impatto di costo inferiore.
    

## 4. Gestione del Buffer (Buffer Management)

- **Cos'è il Buffer:** È una grande area della memoria principale, organizzata dal DBMS (pre-allocata) e condivisa tra le transazioni. È organizzata in pagine di dimensione uguale o maggiore ai blocchi di memoria secondaria (1KB-100KB). Lo scopo è ridurre il numero di accessi alla memoria secondaria, potendo anche decidere di differire la scrittura fisica.
    
- **Buffer Manager e Directory:** Gestisce il caricamento e lo scaricamento delle pagine tra memoria principale e secondaria. Mantiene una directory che, per ogni pagina caricata, traccia il file fisico, il numero del blocco e due variabili di stato: un contatore (che indica quanti programmi usano la pagina) e un "dirty bit" (che indica se la pagina è "sporca", cioè modificata).
    
- **Le 4 Primitive principali:**
    
    - `fix`: richiesta per una pagina; richiede una lettura solo se la pagina non è nel buffer e incrementa il contatore.
        
    - `setDirty`: informa il Buffer manager che la pagina è stata modificata.
        
    - `unfix`: indica che la transazione ha terminato l'utilizzo della pagina, diminuendone il contatore.
        
    - `force`: trasferisce in modo sincrono una pagina alla memoria secondaria (su richiesta del Reliability manager).
        
- **Gestione dello spazio ("Steal" vs "No-Steal"):** Durante un'operazione `fix`, se non ci sono pagine libere (con contatore a zero), si valutano due alternative : "steal" (viene selezionata una "vittima", i suoi dati vengono scritti in memoria secondaria se modificati, e viene letta la pagina di interesse) oppure "no-steal" (l'operazione viene messa in attesa). Le scritture possono avvenire sia in modo sincrono (con `force`) sia asincrono per sfruttare la disponibilità dei dispositivi. Le politiche si basano sulla "data locality" e sulla "legge 80-20".
    

## 5. DBMS e File System

- Il file system è la componente del sistema operativo che gestisce la memoria secondaria. I DBMS ne usano le funzionalità in modo limitato solo per creare e cancellare file e leggere e scrivere singoli blocchi o sequenze.
    
- L'organizzazione interna dei file, inclusa la distribuzione dei record e la struttura all'interno dei singoli blocchi, è gestita direttamente dal DBMS.
    
- Il DBMS crea file di grandi dimensioni e costruisce in quello spazio le strutture fisiche utili a implementare le relazioni. Spesso, ogni singolo blocco è interamente dedicato alle tuple di una singola relazione.
    

## 6. Organizzazione logica e fisica (Blocchi, Record, Tuple)

- **Dimensioni e Fattore di Blocco:** I blocchi ("componenti fisici") e i record ("componenti logiche") presentano dimensioni diverse. La dimensione del blocco dipende dal file system, mentre quella del record dipende dall'applicazione. Il Fattore di Blocco indica il numero di record dentro un blocco. Lo spazio residuo $\lfloor L_{B}/L_{R} \rfloor$ può essere usato per record frazionati ("spanned") o ignorato ("unspanned").
    
- **Struttura della Pagina:** All'interno di una pagina troviamo il dizionario della pagina (con i vari puntatori `*t`), la parte utile della pagina (le tuple effettive), le informazioni di controllo del metodo di accesso e le informazioni di controllo del file system (es. checksum).
    
- **Primitive del Page Manager:**
    
    - _Inserimento/Aggiornamento:_ Se lo spazio è sufficiente, l'inserimento è diretto. Altrimenti l'operazione deve essere preceduta da una riorganizzazione della pagina (che ha costi limitati in memoria principale) o dalla necessità di accedere/allocare nuovi blocchi.
        
    - _Cancellazione:_ È sempre possibile e spesso effettuata senza riorganizzare le informazioni, limitandosi a marcare la tupla come "invalida" per non ridurre lo stack della parte utile.
        
    - _Accesso:_ Si accede a una tupla (o ai suoi campi) tramite il valore della chiave o in base all'offset (la sua posizione) registrato nel dizionario.