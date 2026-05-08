### 1. Introduzione: La Metamorfosi verso i Multicore

Fino a poco tempo fa, i programmatori potevano fare affidamento sul fatto che i progettisti hardware avrebbero reso i processori sequenziali sempre più veloci. Tuttavia, a causa dei limiti fisici legati alla dissipazione del calore (_Power Wall_) e all'impossibilità di estrarre quantità illimitate di parallelismo dalle singole istruzioni (limiti dell'ILP), l'industria è stata costretta a compiere una svolta radicale, passando dalle architetture uniprocessore ai **multiprocessori multicore**.

Oggi, per ottenere prestazioni elevate, l'hardware richiede che il software venga esplicitamente scritto per sfruttare il parallelismo su più livelli.

--------------------------------------------------------------------------------

### 2. L'Evoluzione del Parallelismo (Integrazione Capitoli 3 e 4)

Prima di arrivare a replicare interi processori su un singolo chip, i progettisti hanno inserito il parallelismo direttamente all'interno delle unità di calcolo e della pipeline.

**Dal Capitolo 3 (Aritmetica): Il Parallelismo a livello di Parola (SIMD)** La forma di parallelismo più semplice da costruire si realizza direttamente nell'Unità Aritmetico-Logica (ALU). Invece di eseguire un'operazione su un singolo dato, il **parallelismo a livello di parola (Data-Level Parallelism)** permette all'hardware di operare su più operandi simultaneamente.

- **Esempio/Approfondimento:** Questo approccio è noto come architettura vettoriale o **SIMD (Single Instruction, Multiple Data)**. Nei processori Intel x86, questo ha portato alla nascita delle estensioni multimediali (come SSE e AVX). Grazie ad AVX, i registri interni sono stati allargati fino a 512 bit, permettendo al processore di eseguire, per esempio, 8 somme in virgola mobile a doppia precisione _con una singola istruzione_ nello stesso ciclo di clock.

**Dal Capitolo 4 (Il Processore): Dalla Pipeline al Multithreading (TLP)** Nel Capitolo 4 si è studiato come estrarre il **Parallelismo a livello di Istruzioni (ILP)** tramite la pipeline, l'esecuzione fuori ordine e l'esecuzione speculativa basata sulla predizione dei salti. Tuttavia, quando l'ILP di un singolo programma scarseggia, la pipeline si riempie di "bolle" (stalli). La soluzione architetturale è passare al **Thread-Level Parallelism (TLP)** tramite il **Multithreading hardware**. Un _thread_ è un processo leggero (con un proprio Program Counter e i propri registri). Il processore può mantenere attivi più thread contemporaneamente per nascondere gli stalli:

- **Multithreading a grana fine:** Il processore passa da un thread all'altro a ogni singolo ciclo di clock, mascherando efficacemente le latenze.
- **Multithreading a grana grossa:** Il processore passa da un thread all'altro solo quando si verifica un evento che causerebbe un lungo stallo (come una miss nella cache).
- **Simultaneous Multithreading (SMT):** Tipico dei processori superscalari moderni ad emissione multipla (come l'Hyper-Threading di Intel), permette di prelevare e inviare in esecuzione micro-istruzioni provenienti da _thread diversi_ nello stesso esatto ciclo di clock, saturando al massimo le risorse della pipeline.

--------------------------------------------------------------------------------

### 3. I Multiprocessori a Memoria Condivisa (SMP)

Quando si integrano più processori completi all'interno dello stesso chip, l'architettura dominante è quella dei **Multiprocessori a memoria condivisa (SMP - Shared Memory Multiprocessors)**. In questo modello, tutti i processori (core) condividono un **unico spazio di indirizzamento fisico**. Questo significa che tutte le variabili del programma possono essere rese disponibili in qualsiasi momento a qualsiasi core. Per facilitare la programmazione su queste architetture, sono stati creati modelli come _OpenMP_, che permette di parallelizzare cicli for e distribuire il carico di lavoro con semplici direttive al compilatore.

--------------------------------------------------------------------------------

### 4. Il Collo di Bottiglia della Memoria (Integrazione Capitolo 5)

Costruire un multiprocessore a memoria condivisa solleva una sfida titanica legata alla gerarchia delle memorie, trattata in modo approfondito nel Capitolo 5. Poiché ogni core moderno è dotato di proprie **memorie cache L1 e L2 private** per velocizzare l'accesso ai dati, sorge il gravissimo problema della **Coerenza della Cache (Cache Coherence)**.

**Il Problema della Coerenza:** Cosa succede se due processori leggono e scrivono la stessa variabile condivisa? Senza precauzioni hardware, la CPU A potrebbe modificare il valore di una variabile `X` nella sua cache locale, ma la CPU B continuerebbe a leggere il vecchio valore di `X` presente nella propria cache locale, causando il collasso logico del programma.

Un sistema di memoria si definisce coerente solo se garantisce tre regole:

1. Se un processore P scrive un dato in `X` e poi lo rilegge, deve ottenere il dato che ha appena scritto (nessun altro ha modificato `X` nel frattempo).
2. Se la CPU A scrive un nuovo valore in `X`, una successiva lettura della CPU B su `X` deve restituire il nuovo valore scritto da A.
3. **Serializzazione delle scritture:** Due scritture effettuate sulla stessa locazione da due processori diversi devono essere percepite nello stesso ordine temporale da tutti gli altri processori.

**La Soluzione: Protocolli di Snooping** Per mantenere la coerenza, le architetture moderne adottano protocolli basati sulla diffusione delle informazioni (_Snooping_ o protocolli spioni). In questo schema, tutti i controllori delle cache "ascoltano" costantemente il bus dati condiviso.

- Se un processore ha bisogno di scrivere un dato in memoria, richiede l'accesso esclusivo al bus e invia un segnale di **invalidazione (write-invalidate)**.
- Questo segnale forza tutte le altre cache che contengono una copia di quel blocco a marcarlo come non valido (invalidato). Se in seguito gli altri core avranno bisogno di quel dato, registreranno una _miss_ nella loro cache e saranno costretti a scaricare la copia aggiornata dalla memoria principale.

**Approfondimento / Trabocchetto: La Falsa Condivisione (False Sharing)** Un effetto collaterale distruttivo dei protocolli di snooping è la **falsa condivisione**. Questo fenomeno si verifica quando due variabili completamente distinte e indipendenti vengono caricate _all'interno dello stesso blocco di cache_ (che solitamente è di 64 byte). Se il Core 1 scrive in continuazione la prima variabile e il Core 2 scrive la seconda, i due processori finiranno per invalidare reciprocamente l'intero blocco di cache dell'altro a ogni ciclo, anche se stanno lavorando su dati diversi. Questo causa un rimbalzo continuo dei dati sul bus della memoria, azzerando i benefici prestazionali dell'avere due core. I programmatori devono fare molta attenzione all'allineamento delle strutture dati in memoria per evitare questo trabocchetto.

--------------------------------------------------------------------------------

### 5. Il Futuro: Architetture Specifiche e GPU

Come si evince analizzando le tendenze attuali dei multiprocessori, non tutte le elaborazioni parallele avvengono sui classici core della CPU.

- Le **GPU (Graphics Processing Units)** estremizzano l'approccio parallelo, implementando migliaia di core elementari e organizzando l'esecuzione raggruppando blocchi di thread hardware per massimizzare la larghezza di banda.
- Le **Architetture Specifiche di Dominio (DSA)**, come le Tensor Processing Units (TPU) sviluppate da Google per il machine learning, eliminano le complessità della CPU (come la predizione dei salti avanzata e le gerarchie di cache complesse), investendo tutti i transistor guadagnati in enormi matrici di unità aritmetiche (ALU) e memorie scratchpad controllate direttamente dal software. Queste soluzioni rappresentano la frontiera del parallelismo ad altissima densità per compiti specifici.