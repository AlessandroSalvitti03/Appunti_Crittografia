### 1. Il Concetto di Speculazione

Per sfruttare al massimo il Parallelismo a Livello di Istruzioni (ILP) ed evitare che la pipeline si svuoti in attesa della risoluzione di un salto condizionato, i processori moderni fanno ricorso alla **Speculazione**. La speculazione è un approccio all'esecuzione in cui il processore (o il compilatore, nel caso di speculazione software) tenta di "indovinare" il risultato di un'istruzione per poterne rimuovere la dipendenza e continuare a inviare istruzioni in esecuzione prematuramente.

La speculazione basata sull'hardware si fonda sull'interazione di tre idee chiave:

1. **Predizione dinamica dei salti (Branch Prediction):** Per scegliere quale percorso di istruzioni seguire.
2. **Esecuzione fuori ordine (Out-of-order execution):** La situazione in cui le istruzioni libere da dipendenze continuano a essere eseguite anche se un'istruzione precedente risulta bloccata.
3. **Esecuzione speculativa:** L'esecuzione vera e propria delle istruzioni lungo il percorso predetto, _prima_ che si sappia se il salto doveva essere preso o meno.
___
### 2. Il Problema dello Stato Architetturale e il Completamento in Ordine

Se l'hardware specula e indovina, le prestazioni migliorano drasticamente. Ma cosa succede se la predizione si rivela errata? Il processore deve avere la capacità di scartare le istruzioni calcolate per sbaglio senza che queste abbiano corrotto permanentemente i registri o la memoria.

Per supportare questa funzione di ripristino (recovery), l'architettura separa rigorosamente l'esecuzione dalla scrittura finale dei risultati. Si adotta una politica chiamata **Completamento in ordine (In-order commit)**: mentre i calcoli vengono eseguiti non appena i dati sono pronti (fuori ordine), i risultati vengono scritti nei registri visibili al programmatore o in memoria _esattamente nello stesso ordine sequenziale_ con cui le istruzioni erano state prelevate originariamente.

### 3. Il Reorder Buffer (ROB)

Per implementare il completamento in ordine, l'hardware necessita di una struttura dedicata chiamata **Buffer di Riordino (Reorder Buffer o ROB)**. Il ROB è una coda circolare hardware in cui ogni istruzione prelevata riceve uno "slot" temporaneo. 

Quando un'istruzione speculativa termina l'esecuzione, il suo risultato non viene scritto nel _Register File_, ma viene parcheggiato nel proprio slot all'interno del ROB. Solo quando l'istruzione raggiunge la testa del ROB ed è confermata come "sicuramente da eseguire" (non ci sono state predizioni errate precedenti o eccezioni), il suo risultato viene trasferito definitivamente nei registri di sistema.

Nello specifico, ogni voce (entry) del ROB contiene quattro campi fondamentali: 
* **Tipo di Istruzione:** Indica se si tratta di un salto (senza risultato), una store (destinazione in memoria) o un'operazione registro (load/ALU). 
* **Destinazione:** Il numero del registro (per load/ALU) o l'indirizzo di memoria (per le store). 
* **Valore:** Contiene il risultato dell'istruzione finché questa non va in commit. 
* **Ready:** Un flag che indica se l'esecuzione è terminata e il valore è disponibile .

### 4. Le 4 Fasi dell'Algoritmo di Tomasulo Speculativo

L'introduzione del ROB modifica l'algoritmo di Tomasulo classico. Le fasi di esecuzione diventano quattro:

1. **Issue (Emissione):** L'istruzione viene decodificata in ordine. Se c'è spazio sia in una Reservation Station (Stazione di Prenotazione) sia nel ROB, l'istruzione viene emessa e le viene assegnata un'etichetta (tag) corrispondente al suo slot nel ROB. Spesso chiamata anche **Dispatch**.

2. **Execution (Esecuzione):** Avviene fuori ordine. L'istruzione aspetta i suoi operandi; se non sono pronti, monitora il Common Data Bus. Appena pronti, esegue l'operazione.

3. **Write Result (Scrittura del Risultato):** Al termine dell'esecuzione, l'unità funzionale diffonde il risultato sul bus. Questo risultato viene catturato dalle eventuali Reservation Stations in attesa e viene scritto nello slot corrispondente all'istruzione all'interno del ROB, _ma non nei registri permanenti_.

4. **Commit (Consegna / Ritiro):** Avviene in ordine. Quando l'istruzione si trova alla testa della coda del ROB, possono verificarsi due scenari:
    - _Esecuzione corretta:_ Il risultato viene scritto dal ROB al Register File (o alla memoria, se è una Store), e lo slot del ROB viene liberato.
    - _Predizione errata:_ Se l'istruzione è un salto di cui si è sbagliata la predizione, il processore "svuota" (flush) semplicemente il ROB scartando tutte le istruzioni successive e riprende l'esecuzione dall'indirizzo corretto. Chiamata anche **Graduation**. È importante notare che esistono tre sequenze possibili: il commit normale (scrittura nel registro), il commit di una store (scrittura in memoria) e il caso di errore di predizione (flush del ROB e riavvio) .

### 5. Ridenominazione dei Registri ed Eccezioni Precise

Il ROB assume anche un ruolo vitale per la **ridenominazione dei registri (Register Renaming)**. Nei processori moderni (come la famiglia Intel x86), i pochi registri esposti all'architettura vengono mappati dinamicamente su un numero molto maggiore di registri fisici interni. Questo meccanismo elimina le false dipendenze (come i conflitti Write-After-Write e Write-After-Read) e fornisce un meccanismo robusto per la correzione degli errori di speculazione: è sufficiente ripristinare la mappa di ridenominazione allo stato in cui si trovava prima dell'errore speculativo.

Inoltre, il ROB garantisce che le **eccezioni siano precise**. Se un'istruzione speculativa causa un'eccezione (es. divisione per zero), l'eccezione viene annotata nel ROB ma non viene sollevata finché l'istruzione non giunge alla fase di _Commit_. Se faceva parte di un ramo predetto male, l'istruzione viene scartata e la sua eccezione "fantasma" scompare senza mai disturbare il Sistema Operativo.

L'introduzione del ROB semplifica il resto dell'hardware: 

* **Sostituzione Store Buffers:** Il ROB sostituisce completamente i buffer di store; le scritture in memoria avvengono solo quando l'istruzione raggiunge la testa del ROB. 

* **Nuovo ruolo per le RS:** La ridenominazione dei registri non è più gestita dalle Reservation Stations ma dal ROB . I risultati sul CDB sono ora etichettati con il **numero dello slot del ROB** invece che con l'ID della stazione di prenotazione.

### 6. Caso Reale: Il Microprocessore Intel Core i7

Un eccellente esempio reale di queste tecniche è la complessa microarchitettura del processore Intel Core i7.

- **Gestione Hardware:** Il Core i7 utilizza ampiamente il buffer di riordino e la ridenominazione dei registri per risolvere le false dipendenze e per recuperare il sistema in seguito a speculazioni errate.
- **Mappatura:** Il processore mantiene in tempo reale una mappa tra i registri dell'architettura e i registri fisici interni.
- **Penalità per l'errore:** Affidandosi massicciamente alla speculazione, il processore adotta un sofisticato predittore dei salti a più livelli. Tuttavia, quando sbaglia (misprediction), deve svuotare le strutture e ricaricare le istruzioni, pagando una severa penalità di circa 17 cicli di clock persi.