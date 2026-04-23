### 1. Introduzione: Il limite delle Pipeline Sequenziali

L'Instruction-Level Parallelism (ILP) mira a sovrapporre l'esecuzione di istruzioni non correlate per migliorare le prestazioni. Nelle pipeline tradizionali con esecuzione _in-order_ (in ordine), il problema principale risiede nelle dipendenze sui dati (Data Hazards) che non possono essere nascoste o risolte tramite la semplice tecnica della propagazione (bypassing/forwarding). Quando si verifica una situazione del genere, l'hardware è costretto a inserire degli stalli (bolle) che bloccano l'intera pipeline.

La soluzione a questo collo di bottiglia è la **Schedulazione Dinamica** (Dynamic Scheduling): si permette alle istruzioni che si trovano "dietro" allo stallo di procedere, lasciando che sia l'hardware stesso a riorganizzare (riordinare) l'esecuzione delle istruzioni a runtime per minimizzare i tempi morti.

### 2. Esecuzione Fuori Ordine e Nuovi Hazard

Permettere all'hardware di riordinare il codice abilita l'**esecuzione e il completamento fuori ordine** (Out-of-order execution e Out-of-order commit). Tuttavia, abbandonare la rigida esecuzione sequenziale introduce nuove problematiche. Mentre nelle pipeline semplici avevamo solo dipendenze vere (RAW - Read After Write), l'esecuzione fuori ordine introduce la possibilità di incappare in **dipendenze per nome** (Name Dependencies), che si traducono in due nuovi hazard:

- **WAR (Write After Read - Anti-dipendenza):** Si verifica quando un'istruzione successiva _j_ scrive un registro che un'istruzione precedente _i_ deve ancora leggere. L'ordine deve essere preservato per evitare che _i_ legga il dato sovrascritto.
- **WAW (Write After Write - Dipendenza di output):** Si verifica quando due istruzioni _i_ e _j_ scrivono nello stesso registro. Se finiscono fuori ordine, il registro potrebbe contenere il valore dell'istruzione sbagliata.

### 3. Il primo approccio: Lo Scoreboard (CDC 6600)

La prima implementazione storica della schedulazione dinamica fu introdotta nel supercalcolatore CDC 6600 nel 1963, tramite un'unità chiamata **Scoreboard**.

Lo Scoreboard centralizza la gestione degli hazard: **ogni singola istruzione deve passare attraverso lo Scoreboard**, il quale determina quando l'istruzione può leggere i suoi operandi, quando può iniziare l'esecuzione e quando può scrivere i risultati. Per fare questo, lo stadio di decodifica (ID) viene diviso in due sotto-stadi:

1. **Issue (Emissione):** Decodifica l'istruzione e controlla che non ci siano hazard strutturali (es. l'unità funzionale richiesta deve essere libera) e hazard WAW.
2. **Read Operands (Lettura Operandi):** Aspetta attivamente finché non ci sono più hazard sui dati (RAW), dopodiché legge i registri.

**Il paradigma dello Scoreboard:** L'emissione delle istruzioni avviene rigidamente **in-order** (in ordine), ma la lettura degli operandi e l'esecuzione avvengono **out-of-order** (fuori ordine) per le istruzioni che non presentano dipendenze.

**Approfondimento sui controlli:** Prima che lo stadio di Issue possa inviare un'istruzione, lo Scoreboard consulta una complessa struttura dati che tiene traccia dello stato di tutte le unità funzionali (Functional Units). Effettua i seguenti controlli hardware:

- L'unità funzionale richiesta è disponibile? (Check colonna _busy_).
- I dati di input sono pronti? (Check RAW).
- È sicuro scrivere sul registro destinazione senza sovrascrivere dati utili ad altri? (Check WAR e WAW).
- C'è un conflitto strutturale nello stadio di Write-Back (WB)?.

### 4. Architetture Superscalari Moderne (Oltre lo Scoreboard)

I processori superscalari moderni (dotati di _Dynamic Multiple Issue_) evolvono il concetto dello Scoreboard introducendo strutture più raffinate per gestire contemporaneamente l'esecuzione fuori ordine e mantenere un comportamento corretto del programma, specie in caso di eccezioni.

La pipeline viene tipicamente divisa in tre macro-unità principali:

1. **Fetch e Decodifica (In-order Issue):** Le istruzioni vengono prelevate, decodificate e inviate in ordine alle unità funzionali.
2. **Esecuzione Fuori Ordine (Out-of-order Execution):** Ciascuna unità funzionale possiede dei buffer chiamati **Stazioni di Prenotazione (Reservation Stations)**. Un'istruzione viene parcheggiata qui insieme ai suoi operandi. Appena tutti gli operandi sono pronti, l'operazione viene eseguita.
3. **Consegna in Ordine (In-order Commit):** Per garantire che il programma si comporti come se fosse sequenziale (specialmente in caso di eccezioni o salti errati), i risultati calcolati fuori ordine non vengono subito scritti nei registri finali. Vengono parcheggiati in un **Buffer di Riordino (Reorder Buffer)**. La scrittura finale (commit) nei registri visibili al programmatore o in memoria avviene _rigorosamente nell'ordine originale_ del programma.

**Esempio/Approfondimento (Register Renaming Hardware):** Nelle architetture moderne, l'uso combinato delle Reservation Stations e del Reorder Buffer permette di implementare via hardware la **ridenominazione dei registri (Register Renaming)**. Quando un'istruzione è in attesa, invece di bloccare la pipeline per una falsa dipendenza (WAW o WAR), l'hardware assegna dinamicamente un registro temporaneo interno al posto del registro architetturale. Questo elimina di netto gli hazard WAR e WAW, esponendo un parallelismo (ILP) vastissimo.

### 5. La Speculazione Hardware

Per mantenere la pipeline piena di istruzioni da eseguire, la schedulazione dinamica fa un uso aggressivo della **Speculazione**, ovvero tenta di "indovinare" l'esito di un'operazione (ad esempio l'esito di un salto condizionato) per iniziare in anticipo l'esecuzione delle istruzioni successive.

Nei processori hardware, i risultati di queste esecuzioni "indovinate" vengono tenuti nel Buffer di Riordino finché non si ha la certezza matematica che la speculazione fosse corretta.

- **Se corretta:** L'esecuzione viene completata scrivendo i risultati permanentemente.
- **Se errata:** L'hardware elimina ("flush") semplicemente il contenuto di questi buffer speculativi e riprende l'esecuzione lungo il ramo corretto di istruzioni. Questo approccio permette anche di gestire le eccezioni in modo "preciso".

### 6. Caso Reale: Il processore Intel Core i7

Il processore Intel Core i7 è un eccellente esempio di queste tecnologie combinate. Nonostante utilizzi l'antico e complesso set di istruzioni x86, l'hardware interno traduce al volo le istruzioni x86 in istruzioni semplici in stile RISC, chiamate **micro-operazioni**. La complessa microarchitettura del Core i7 ha una pipeline di 14 stadi che sfrutta la ridenominazione dei registri (avendo a disposizione internamente molti più registri fisici dei 16 registri visibili dall'architettura x86-64), la schedulazione dinamica e la speculazione. Questo motore di esecuzione fuori ordine utilizza una stazione di prenotazione centralizzata a 97 elementi e un enorme Buffer di Riordino a 224 elementi, potendo sostenere l'esecuzione e il ritiro in ordine (commit) di un massimo di 6 micro-operazioni per singolo ciclo di clock. L'errata predizione di un salto, tuttavia, costa molto cara: comporta una penalità di scarto che ammonta a circa 17 cicli di clock persi.