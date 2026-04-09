
### 1. Introduzione: Perché passare alla predizione dinamica?

Come visto con la predizione statica, ipotizzare a priori l'esito di un salto condizionato (ad esempio assumendo sempre "salto non effettuato") comporta un limite per le prestazioni, specialmente al crescere della profondità della pipeline o nei processori a emissione multipla (superscalari), dove la penalità per un errore costa molti cicli di clock o istruzioni perse.

A differenza della predizione statica in cui l'esito è fissato a tempo di compilazione, le tecniche di **Predizione Dinamica** si basano su un'idea fondamentale: **utilizzare il comportamento passato del salto per predirne il comportamento futuro a run-time**. Un aspetto cruciale di qualsiasi tecnica (statica o dinamica) è che lo stato del processore non deve essere mai modificato in modo permanente finché non si è assolutamente certi dell'esito del salto.

La predizione dinamica si articola in due meccanismi interagenti:

1. **Branch Outcome Predictor:** Predice la "direzione" del salto (Effettuato / Non Effettuato).

2. **Branch Target Predictor:** Predice l'indirizzo di destinazione in caso di salto effettuato.

___

### 2. Prevedere l'indirizzo: Il Branch Target Buffer (BTB)

Affinché la predizione sia utile e consenta di evitare di introdurre stalli (bolle) nella pipeline, il prelevamento (fetch) della nuova istruzione deve iniziare nel ciclo immediatamente successivo. Non basta sapere _se_ il salto avverrà, occorre anche sapere _dove_ saltare.

Per questo si usa il **Branch Target Buffer (BTB)** o Branch Target Predictor, una piccola memoria organizzata come una cache a cui si accede nello stadio di Instruction Fetch (IF) usando la porzione inferiore del Program Counter (PC) dell'istruzione prelevata. Ogni riga del BTB contiene tipicamente due informazioni:

- Il tag con l'indirizzo esatto dell'istruzione di salto.
- L'indirizzo predetto di destinazione (spesso espresso in modalità PC-relativa).

**Possibili problematiche:** Proprio come le cache convenzionali, il BTB può soffrire di collisioni (aliasing) se due istruzioni di salto differenti condividono gli stessi bit meno significativi del PC. Se non vengono gestite (ad esempio aumentando il numero di righe o usando funzioni di hash), le collisioni possono abbassare drasticamente le prestazioni.

- **Fase di Accesso**: Il BTB viene consultato nello stadio di **Instruction Fetch (IF)** contemporaneamente alla lettura dell'istruzione dalla memoria.

- **Contenuto della riga**: Oltre all'indirizzo di destinazione, ogni riga del BTB deve contenere un **Tag** (bit alti del PC) per confermare che l'istruzione corrente sia effettivamente il salto memorizzato e non un'altra istruzione con gli stessi bit bassi del PC (aliasing).

### 3. Prevedere la direzione: Il predittore a 1-bit (BHT)

Il meccanismo più elementare per la predizione dell'esito si chiama **Branch History Table (BHT)** o Branch Prediction Buffer. È una tabella in cui ogni voce contiene 1 singolo bit indicante se la volta precedente il salto è stato preso (Taken, 1) o non preso (Not Taken, 0). Anche questa tabella è indicizzata dai bit meno significativi dell'indirizzo del salto. In questo caso, la memoria non ha tag (ogni accesso è un _hit_): la predizione è considerata puramente un "suggerimento" (hint); se risulta errata a causa di una collisione con un altro salto o di un cambio di comportamento del programma, si subirà semplicemente un calo di prestazione senza intaccare la correttezza del risultato.

**Il problema dei predittori a 1-bit:** Il predittore a 1 bit ha un difetto critico: per i cicli (loop) sbaglia tipicamente due volte per ogni esecuzione completa del ciclo.

- _Esempio / Approfondimento:_ Immaginiamo un ciclo FOR che si ripete 10 volte. Alla prima iterazione, il salto condizionato di ritorno all'inizio del ciclo viene predetto erroneamente come _Not Taken_ perché l'ultima volta che il programma era in quel punto (alla fine dell'esecuzione precedente del ciclo) il salto non era stato preso per uscire dal ciclo. Il predittore viene quindi corretto a _Taken_. Il salto viene predetto correttamente per le iterazioni dalla 2 alla 9. All'iterazione 10 (l'uscita dal ciclo), il salto fallisce ma il predittore suggerisce _Taken_, generando il secondo errore (e reimpostando il bit a 0). Con un ciclo da 10 iterazioni avremmo 2 errori su 10, fornendo un'accuratezza modesta (80%) nonostante la fortissima regolarità del programma.

**Assenza di Tag**: A differenza del BTB, la **BHT** (o Branch Prediction Buffer) solitamente non ha tag. Questo perché una predizione errata dovuta ad aliasing (due salti che mappano sulla stessa voce) non causa errori logici, ma solo un calo temporaneo di prestazioni. È considerata un semplice "suggerimento" (hint).

### 4. Il predittore a 2-bit (Saturating Counters)

Per rimediare alla debolezza del predittore a 1 bit, l'architettura moderna sfrutta uno **schema a 2-bit**. Il principio alla base è dare al salto una "seconda possibilità" (2nd chance): **la predizione deve rivelarsi errata due volte consecutive prima di essere modificata**.

Il predittore a 2 bit agisce come una macchina a stati finiti (FSM) con quattro stati:

- **00 - Strongly Not Taken** (Fortemente Non Preso)
- **01 - Weakly Not Taken** (Debolmente Non Preso)
- **10 - Weakly Taken** (Debolmente Preso)
- **11 - Strongly Taken** (Fortemente Preso)

In questo caso, l'uscita da un ciclo cambierà lo stato da "Fortemente Preso" a "Debolmente Preso", senza invertire la predizione. Quando il ciclo verrà rieseguito in futuro, la prima iterazione sarà predetta correttamente come "Preso" (ritornando allo stato "Fortemente Preso") e si avrà un solo errore in totale (quello di uscita) invece di due.

**Generalizzazione:** L'idea può essere generalizzata usando un contatore a saturazione a _n_ bit. Quando il contatore raggiunge o supera la metà del suo valore massimo (2n−1), il salto è predetto come "Effettuato", altrimenti "Non Effettuato". Tuttavia, gli studi dimostrano che le prestazioni di un predittore a 2-bit sono quasi equivalenti a quelle di predittori a n bit infinitamente grandi.

### 5. Spingersi oltre: I Predittori Correlati (Globali)

Il limite delle Branch History Tables standard è che usano un approccio strettamente **locale**: basano la predizione di un salto unicamente sul passato di _quel singolo salto_.

Tuttavia, molto spesso l'esito di un costrutto condizionato (`if`) dipende da condizioni logiche valutate in costrutti `if` precedenti (es. se x=2, un successivo x>1 sarà sicuramente vero). I predittori che sfruttano il comportamento di altri salti recenti per prendere una decisione sono chiamati **Predittori Correlati (o predittori a 2 livelli)**.

Un predittore correlato di tipo (m,n) utilizza:

- **m bit di storia globale:** Si tiene traccia dell'esito degli ultimi _m_ salti eseguiti da tutto il programma in uno Shift Register chiamato **Branch History Register (BHR)**.
- **n bit di predizione locale:** Si creano 2m Branch History Tables separate, ciascuna composta da predittori a _n_ bit.

In hardware, l'indice della tabella si ottiene tipicamente concatenando la storia globale (BHR) con i bit bassi del PC del salto corrente (Local Predictor).

- _Approfondimento:_ I benchmark dimostrano che un predittore correlato (2,2) con appena 1K entries complessive spesso surclassa un semplice predittore locale a 2-bit dotato di dimensioni "infinite". Questo dimostra che la correlazione globale porta un contributo informativo insostituibile.

Come l'hardware usa i bit:

- **Indirizzamento**: In un predittore $(m,n)$, i $m$ bit di storia globale (Global History Register) servono a selezionare una tra le $2^{m}$ tabelle locali disponibili. Ogni tabella locale contiene predittori a $n$ bit.

- **Efficacia**: Ricorda il dato delle slide: un predittore correlato $(2,2)$ con solo **1024 voci** totali è spesso più accurato di un predittore locale a 2 bit di dimensioni infinite.

### 6. Il Tournament Predictor (Predittore a Torneo)

Poiché alcuni salti sono predetti meglio sfruttando la storia locale (ad es. i ritorni dei cicli loop) e altri saltano meglio guardando la storia globale, sono nati i **Predittori a Torneo (Tournament Predictors o GA Predictors)**.

In questo schema ibrido, per ciascun salto il processore mantiene:

1. Una predizione fornita da un modulo Locale.

2. Una predizione fornita da un modulo Globale.

3. Un **Selettore**, gestito tramite dei predittori a contatori (es. un 2-bit predictor) che tiene traccia di quale dei due moduli si sia rivelato storicamente più accurato per quel particolare salto. A run-time, il selettore abilita solo il modulo vincente per l'istruzione corrente.

Dettaglio sul modulo di controllo:

- **Selettore a 2 bit**: Anche il selettore che sceglie tra predittore locale e globale è tipicamente implementato come un **contatore a saturazione a 2 bit**. Se il modulo globale indovina e quello locale sbaglia, il contatore si sposta verso "Globale", e viceversa.

___
### 7. Il costo dell'errore (Misprediction e Pipeline Flush)

Cosa accade quando le tecniche di predizione dinamica sbagliano? Una predizione errata (_misprediction_) impone al processore di tornare sui propri passi.

L'unità di controllo della pipeline deve:

1. Congelare l'esecuzione lungo il ramo sbagliato.

2. Svuotare (operazione nota come **flush**) le istruzioni prelevate erroneamente che si trovano ormai negli stadi IF, ID e talvolta EX della pipeline. In pratica l'hardware converte dinamicamente queste istruzioni in bolle inerti, forzando i loro segnali di controllo a zero           (`nop`), affinché non modifichino lo stato o i registri visibili dal programmatore.

3. Riscrivere il PC con l'indirizzo della destinazione corretta e riprendere il _fetch_ da lì.

4. Aggiornare le tabelle (BHT, BTB, ecc.) con il reale esito del salto per istruire meglio la macchina alle prossime iterazioni.

L'inserimento di queste bolle è responsabile del calo delle prestazioni quantificato dai cicli di stallo (Branch Penalty), che aumenta col crescere del numero degli stadi in architetture profondamente "pipelined" o in processori che emettono parallelamente istruzioni multiple.

**Zeroing**: Durante il flush, l'hardware forza a **zero** tutti i segnali di controllo delle istruzioni che si trovano negli stadi IF, ID ed EX, trasformandole di fatto in `nop` (no operation).