### 1. Il Contesto e le Innovazioni dell'Algoritmo di Tomasulo

Ideato per il mainfame IBM 360/91 (tre anni dopo l'introduzione dello Scoreboard nel CDC 6600), l'algoritmo di Tomasulo è uno schema di schedulazione dinamica dell'hardware con l'obiettivo di ottenere altissime prestazioni permettendo l'esecuzione fuori ordine senza fare affidamento su compilatori speciali. Questo algoritmo è diventato il fondamento di architetture moderne di grandissimo successo (dalla famiglia Alpha e PowerPC, fino ai moderni Intel Core).

Mentre lo Scoreboard limitava pesantemente il parallelismo bloccando l'emissione in presenza di conflitti (hazard strutturali e nominali), Tomasulo rivoluziona l'architettura attraverso tre innovazioni fondamentali:

1. **Controllo e buffer distribuiti:** La logica di controllo non è più un unico blocco centralizzato. Le code di istruzioni in attesa, chiamate **Reservation Stations (Stazioni di Prenotazione)**, sono posizionate direttamente in ingresso alle varie Unità Funzionali (FU).
2. **Register Renaming in Hardware:** I registri nominati nelle istruzioni vengono rimpiazzati da valori effettivi oppure da "puntatori" alle Reservation Station che li produrranno. Essendoci fisicamente più stazioni di prenotazione che registri nel calcolatore, l'hardware applica ottimizzazioni di rinomina (renaming) che un compilatore non potrebbe fare, **evitando totalmente i falsi hazard WAW (Write-After-Write) e WAR (Write-After-Read)**.
3. **Common Data Bus (CDB):** I risultati non transitano in modo sequenziale attraverso i registri. I dati vengono trasmessi ("in broadcast") dalle FU al Common Data Bus e prelevati "al volo" da tutte le Stazioni di Prenotazione in attesa, abilitando un forwarding hardware formidabile.

### 2. Struttura delle Reservation Stations e dei Buffer

Ogni istruzione in corso di elaborazione occupa una riga (entry) all'interno di una stazione di prenotazione. Ogni elemento è costituito dai seguenti campi chiave:

- **Op:** L'operazione da eseguire.
- **Vj, Vk:** I valori effettivi degli operandi sorgente se sono già pronti.
- **Qj, Qk:** I puntatori alle RS che stanno attualmente calcolando gli operandi mancanti. _Nota: Per ogni operando, solo uno tra il campo V (valore) o il campo Q (puntatore) è valido_.
- **Busy:** Indica se la stazione è occupata.

Anche le istruzioni di memoria (`Load` e `Store`) vengono gestite come se fossero normali unità funzionali dotate di propri buffer (con un campo A per l'indirizzo di memoria).

### 3. Le 3 Fasi dell'Algoritmo

Il nucleo dell'algoritmo di Tomasulo classico si divide in 3 stadi operativi:

1. **Issue (Emissione In-Order):** L'istruzione viene prelevata. Se c'è una Reservation Station libera (nessun hazard strutturale), l'istruzione viene emessa. Qui avviene la risoluzione degli hazard nominali: il Register File viene dinamicamente scollegato dai vecchi riferimenti e collegato alla nuova istruzione in ingresso (evitando WAW), mentre eventuali operandi necessari vengono immediatamente copiati nella RS (evitando WAR).
2. **Execution (Esecuzione Out-of-Order):** Se entrambi gli operandi sono pronti (campi V validi), l'istruzione viene eseguita. Se non lo sono, la RS monitora il Common Data Bus aspettando il risultato (ritardando l'esecuzione ed evitando gli hazard RAW). Le Load/Store richiedono un processo a due step: calcolo dell'indirizzo effettivo e poi accesso alla memoria.
3. **Write Back (Scrittura dei Risultati):** Quando il risultato è calcolato, viene trasmesso in broadcast sul CDB e memorizzato simultaneamente nel Register File e in tutte le RS o Store Buffer in attesa di quel dato. Infine, la stazione di prenotazione si libera.

**Esempio e Vantaggi Pratici:** Analizzando il ciclo di esecuzione mostrato nel materiale del corso, Tomasulo è in grado di completare una complessa sequenza di codice in **57 cicli**, mentre l'algoritmo Scoreboard richiederebbe **62 cicli**. Questo risparmio di tempo è dato dal fatto che Tomasulo non soffre dei lunghi stalli causati da hazard strutturali e dall'assenza del meccanismo di forwarding nello Scoreboard. Un altro grandissimo vantaggio (mostrato nello scheduling) risiede nella capacità delle veloci istruzioni intere (come la gestione dei cicli `for` e dei puntatori) di superare le istruzioni di salto (branch). Questo permette al processore di caricare le istruzioni in virgola mobile per le iterazioni successive prima ancora che le prime siano terminate, ottenendo uno **srotolamento dinamico del ciclo (Loop Unrolling in HW)** impossibile per lo Scoreboard.

### 4. L'Integrazione nei Processori Moderni: Il Reorder Buffer (ROB)

L'implementazione originale IBM dell'algoritmo di Tomasulo effettua scritture (commit) fuori ordine. Questo comporta un enorme problema con le eccezioni e i salti: se l'hardware "indovina" male la direzione di un branch o si verifica una divisione per zero, è quasi impossibile ritornare a uno stato consistente.

Per questo motivo, la variante moderna dell'architettura Tomasulo utilizzata oggi (approfondita nei testi di _Computer Organization and Design_) prevede l'aggiunta di una **Commit Unit (Unità di Consegna)** e di un **Reorder Buffer (Buffer di Riordino)**.

- **In-Order Commit:** L'emissione (issue) avviene in ordine, l'esecuzione avviene fuori ordine (sfruttando le RS descritte sopra), ma **la consegna dei risultati (commit) è costretta ad avvenire in ordine**.
- **Meccanismo:** Quando una Reservation Station calcola un risultato, non lo scrive più in maniera permanente nel Register File né in memoria. Lo deposita nel _Reorder Buffer_.
- Solo quando l'istruzione diventa non-speculativa (cioè giunge alla testa del ROB e si è sicuri che non ci sono stati salti sbagliati o eccezioni), la _Commit Unit_ permette al risultato di "ritirarsi", aggiornando finalmente e in modo permanente i registri visibili al programmatore o la memoria.

Se il processore si rende conto di aver speculato male, semplicemente svuota le istruzioni temporanee dal ROB e l'esecuzione riparte in modo pulito dal percorso corretto.