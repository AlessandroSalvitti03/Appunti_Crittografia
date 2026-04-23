
### 1. Oltre il CPI = 1: Architetture VLIW

L'obiettivo iniziale della pipeline è avvicinarsi a un CPI (Clock Cycles Per Instruction) pari a 1. Per superare questo limite (e avere quindi un CPI < 1, o IPC > 1), le architetture devono emettere più operazioni per singolo ciclo di clock. A differenza delle architetture Superscalari (che affidano all'hardware il compito di trovare il parallelismo a runtime), le architetture **VLIW (Very Long Instruction Word)** o a **Schedulazione Statica** si affidano interamente al **compilatore**.

- **Il concetto:** Il compilatore raggruppa un numero fisso di operazioni indipendenti (es. da 4 a 16 operazioni come somme, load, store, moltiplicazioni) in un'unica "lunghissima" parola di istruzione (Issue Packet).
- **Vantaggi:** L'hardware è molto più semplice rispetto ai processori superscalari. Non necessita di circuiti complessi per il riordinamento dinamico delle istruzioni, controlli RAW incrociati o hardware di schedulazione.
- **Svantaggi:** Richiede un enorme numero di registri per mantenere attive tutte le unità funzionali, un'elevatissima larghezza di banda verso le memorie e la cache, e soffre dell'espansione della dimensione del codice (code size explosion). Inoltre, fatica moltissimo in presenza di salti imprevedibili e non può adattarsi dinamicamente ai ritardi della memoria (come i cache miss).


- **ILP (Parallelismo a livello di istruzioni)**: È **trasparente all'utente** e mira a velocizzare l'esecuzione sovrapponendo singole operazioni (add, mul, load).
    
- **Parallel Processing**: È **non trasparente**, richiede che l'utente (o il programmatore) divida il programma in chunk separati per diversi processori.
### 2. Ottimizzazione dei Cicli: Espansione dei Cicli (Loop Unrolling)

I compilatori utilizzano algoritmi sofisticati per estrarre l'ILP (Instruction-Level Parallelism), ma all'interno di un blocco base (Basic Block, una sequenza di istruzioni senza salti interni) il parallelismo disponibile è minimo.

Una tecnica classica per esporre maggiore parallelismo è l'**Espansione dei Cicli (Loop Unrolling)**. Questa tecnica replica il corpo del ciclo più volte. Ad esempio, "srotolando" un ciclo di 4 volte, il processore elabora 4 elementi del vettore per ogni singola iterazione del nuovo ciclo.

- **Ridenominazione dei registri (Register Renaming):** Quando il compilatore srotola il ciclo, deve utilizzare registri temporanei differenti per ciascuna copia del corpo originale. Questo processo, chiamato _ridenominazione dei registri_, è fondamentale per eliminare le dipendenze nominali (false dipendenze WAW e WAR) che altrimenti bloccherebbero l'esecuzione parallela.
- **Limiti:** L'unrolling comporta costi di startup e "wind-down" (chiusura) per **ogni** iterazione del ciclo srotolato. Oltre a riempire la memoria istruzioni (aumento del codice), si arriva rapidamente a un punto in cui si esauriscono i registri disponibili.

"VLIW" è spesso usato come sinonimo di **Static Multiple Issue**. In questo schema, il compilatore deve garantire che non ci siano hazard all'interno di un pacchetto di istruzioni (Issue Packet), o tra pacchetti emessi consecutivamente.

### 3. Ottimizzazione dei Cicli: Software Pipelining

Per superare i limiti dell'Unrolling, i compilatori utilizzano il **Software Pipelining**. Questa tecnica riorganizza il ciclo in modo che ogni singola iterazione nel codice software-pipelined contenga istruzioni pescate da **differenti iterazioni** del ciclo originale.

**Approfondimento / Esempio pratico:** Immaginiamo un ciclo originale con tre operazioni dipendenti: Load (L), Calcolo (C), Store (S). Nel Software Pipelining, il corpo del nuovo ciclo conterrà, nello stesso momento:

- La **Store** per il dato dell'iterazione `i`
- Il **Calcolo** per il dato dell'iterazione `i+1`
- La **Load** per il dato dell'iterazione `i+2`

Questa tecnica divide concettualmente l'esecuzione in tre fasi:

1. **Prolog (Prologo):** Riempie gradualmente la "pipeline software" avviando le prime Load e Calcoli.
2. **Iterate (Corpo centrale a regime):** Tutte le unità funzionali lavorano a pieno regime senza conflitti. Il grande vantaggio rispetto all'Unrolling è che i costi di startup e wind-down si pagano **una sola volta per l'intero ciclo**, e non per ogni singola "fettina" srotolata. Se in un loop srotolato 4 volte si otteneva 0.36 FLOPS/ciclo, con il Software Pipelining si può raggiungere 1 FLOP/ciclo ideale.
3. **Epilog (Epilogo):** Svuota la pipeline scrivendo gli ultimi risultati.

Questa tecnica è definita anche come **"Symbolic Loop Unrolling"**. Il vantaggio chiave, oltre all'efficienza dei FLOPS, è che richiede **meno codice** rispetto all'unrolling fisico massiccio, poiché il corpo del ciclo (Kernel) rimane compatto.

## 4. Supporto Hardware ai Cicli: Rotating Register Files

Il Software Pipelining e il Loop Unrolling consumano moltissimi registri e causano una massiccia duplicazione del codice nel prologo e nell'epilogo. Per risolvere questi colli di bottiglia, architetture avanzate (come IA-64 / Itanium) utilizzano i **Rotating Register Files (File di registri rotanti)**.

L'hardware alloca un nuovo set di registri fisici per ogni iterazione del ciclo, in modo trasparente. Viene utilizzato un **RRB (Rotating Register Base)** che si somma al numero logico del registro per ottenere l'indirizzo fisico.

- A ogni iterazione, l'RRB viene incrementato/decrementato.
- _Esempio:_ Se un'istruzione carica un dato nel registro `P9` e la latenza della Load è di 3 cicli, l'istruzione dipendente che ne ha bisogno 3 iterazioni (cicli) dopo lo troverà "magicamente" scalato in un nuovo registro calcolato via RRB, senza che il compilatore debba riassegnare manualmente decine di registri ed espandere esplicitamente il codice del prologo/epilogo.

**Eliminazione di Prologo ed Epilogo**: In architetture avanzate come l'IA-64, i registri rotanti (insieme a predicati speciali) permettono di eseguire il software pipelining **senza scrivere esplicitamente il prologo e l'epilogo** nel codice, riducendo drasticamente la dimensione del binario.

## 5. Oltre i Cicli: Trace Scheduling

Non tutto il codice è costituito da cicli regolari (es. programmi con costrutti `if-else` irregolari). In presenza di flussi di controllo intensi, i Basic Block sono troppo corti per estrarre ILP.

La tecnica del **Trace Scheduling** permette di parallelizzare istruzioni estese attraverso più blocchi base:

1. **Profilazione (Profiling):** Il compilatore esegue prima il programma (o usa euristiche) per individuare il percorso ramificato più probabile e frequente, chiamato **Trace (Traccia)**.
2. **Schedulazione:** La traccia intera viene schedulata globalmente ignorando le diramazioni, come se fosse un unico gigantesco blocco base in cui si ottimizza pesantemente il codice.
3. **Compensation Code (Codice di compensazione):** Poiché la traccia salta su percorsi che potrebbero non essere sempre presi, il compilatore deve inserire del codice correttivo (Fixup code) nei punti di ingresso laterali e uscita laterale, per ripristinare la legalità del programma se la previsione risulta sbagliata. Il codice di compensazione, specialmente per gli ingressi laterali, è estremamente complesso da generare.

4. Speculazione ed Esecuzione Predicata (IA-64)

Per superare la rigidità dei salti (Branch), i progettisti usano tecniche speculative (Hardware o Software) che tentano di ipotizzare il comportamento delle istruzioni per anticiparne l'esecuzione.

- **Predicated Execution (Esecuzione Predicata):** Permette di eliminare del tutto i salti difficili da prevedere. Nel set IA-64, quasi tutte le istruzioni possono essere eseguite in modo condizionale basandosi su un _registro predicato_. Invece di diramarsi in due blocchi (if/else), il compilatore valuta la condizione in registri booleani (es. `p1`, `p2`) ed emette entrambe le istruzioni contemporaneamente. L'istruzione che ha il predicato _Falso_ diventa dinamicamente una `nop` (nessuna operazione). Questo trasforma un hazard di controllo in una dipendenza sui dati e fonde 4 blocchi base in un unico blocco.

- **Speculazione Software sulle Load:** Si vuole spostare una Load in alto (prima di un branch o di una Store) per nascondere la latenza di memoria, ma c'è il rischio di causare un'eccezione o leggere un dato non aggiornato (Memory Hazard). L'hardware fornisce supporto tramite:

    - _Load Speculative:_ Una load anticipata che, in caso di errore (es. page fault fittizio), non genera un'eccezione immediata ma imposta un **bit "veleno" (poison bit)** sul registro destinazione. L'eccezione scatta solo se e quando un'istruzione "check" conferma che il dato era realmente necessario nel blocco originale.
    
    - _Advance Load (Data Speculative Load):_ Una load posta prima di una Store potenzialmente conflittuale. Aggiunge l'indirizzo in una speciale **Address Check Table** hardware (associativa). Se la Store successiva scrive effettivamente a quell'indirizzo, invalida l'elemento in tabella. Un'istruzione di controllo (Check) successiva forza il salto a un _fixup code_ se il dato è stato invalidato.

"Advance Load", il termine tecnico specifico per la tabella hardware citata è **ALAT (Advanced Load Address Table)**.
- **Funzionamento**: Quando viene eseguita una `ld.a` (advance load), l'indirizzo viene inserito nell'ALAT. Qualsiasi Store successiva allo stesso indirizzo "cancella" la voce corrispondente. L'istruzione `chk.a` (check advance) controlla semplicemente se l'indirizzo è ancora presente nell'ALAT; se non c'è, fa scattare il codice di recupero.