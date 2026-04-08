
### 1. Introduzione: Evoluzione Tecnologica e "Muro della Potenza"

Il miglioramento delle prestazioni dei calcolatori è stato storicamente guidato dai progressi nella tecnologia dei semiconduttori (riduzione delle dimensioni dei transistor e aumento della frequenza di clock) e dai miglioramenti nell'architettura, come l'avvento dei compilatori per linguaggi ad alto livello (HLL), UNIX e le architetture RISC. Questo ha permesso la creazione di dispositivi leggeri e ha aumentato la produttività nello sviluppo del software.

Tuttavia, l'aumento continuo della frequenza di clock si è scontrato con il cosiddetto **Power Wall** (muro dell'energia). La potenza dinamica dissipata da un circuito CMOS è descritta dalla formula: **Potenza = Capacità di carico × Tensione² × Frequenza di commutazione**. Mentre per anni i progettisti hanno ridotto la tensione per contenere i consumi permettendo alla frequenza di scalare, questo processo ha raggiunto un limite pratico per il raffreddamento dei microprocessori. La conseguenza diretta è stata un epocale cambio di paradigma (il "parallel step"): l'industria è passata dai sistemi uniprocessore ai **sistemi multiprocessore (multicore)**, puntando sul miglioramento del throughput parallelo piuttosto che sulla velocità del singolo core.

Gestione del calore:
- **TDP (Thermal Design Power)**: Rappresenta il consumo di potenza sostenuto ed è il target per la progettazione dei sistemi di raffreddamento.

- **Potenza Statica**: Oltre alla potenza dinamica, esiste una quota di potenza statica dovuta alla corrente di perdita (leakage), che scala con il numero di transistor.

- **Power Gating**: Tecnica utilizzata per ridurre la potenza statica "spegnendo" parti del chip non utilizzate.

### 2. Metriche per Valutare le Prestazioni

Dire che "un calcolatore è più veloce di un altro" dipende da quale metrica è più importante per l'utente. Le due metriche fondamentali sono:

- **Tempo di risposta (Response Time / Execution Time):** È il tempo totale trascorso (elapsed time) per completare un singolo task, inclusi accessi al disco, latenza di memoria, I/O e overhead del sistema operativo. È la metrica cruciale per i personal computer e i dispositivi mobili.

- **Throughput (o Larghezza di Banda):** È la quantità totale di lavoro completata in una data unità di tempo. È la metrica fondamentale per i gestori di server e datacenter.

**Esempio di approfondimento:** Se confrontiamo un Boeing 747 con il Concorde, il Concorde ha un tempo di volo inferiore (minore tempo di risposta), ma il Boeing 747, trasportando molti più passeggeri per viaggio, ha un _throughput_ (passeggeri × miglia orarie) nettamente superiore. Inoltre, si dice che un calcolatore X è _n_ volte più veloce di un calcolatore Y se il rapporto tra le prestazioni di X e Y (che equivale al rapporto inverso dei loro tempi di esecuzione) è pari a _n_.

- **Warehouse Scale Computers (WSC)**: Sistemi usati per il Software as a Service (SaaS) dove l'enfasi è sulla disponibilità e sul rapporto prezzo-prestazioni.

- **Supercomputer**: Macchine focalizzate esclusivamente sulle prestazioni in virgola mobile e sulla velocità delle reti interne.

- **Embedded Computers**: Sistemi (come IoT o ECU automotive) dove il vincolo principale è il prezzo minimo e il basso consumo.

### 3. L'Equazione Classica delle Prestazioni della CPU

Il tempo di esecuzione della CPU per un programma (escludendo i tempi di attesa per I/O) è dato dal prodotto di tre fattori fondamentali: **Tempo CPU = Instruction Count (IC) × CPI × Tempo di Clock (**TCLK​**)**. Oppure dividendo per la frequenza di clock: **Tempo CPU = (IC × CPI) / Frequenza di Clock**.

Questi tre termini sono influenzati da diverse scelte hardware e software:

- **Instruction Count (IC):** Il numero di istruzioni eseguite dipende dall'algoritmo, dal linguaggio di programmazione, dal compilatore e dall'Instruction Set Architecture (ISA).

- **CPI (Clock Cycles per Instruction):** Il numero medio di cicli di clock per eseguire un'istruzione. È determinato dall'organizzazione dell'hardware (pipeline, sistema di memoria) e dall'ISA. Il CPI medio si calcola come ∑(CPIi​×Fi​), dove Fi​ è la frequenza di utilizzo di uno specifico tipo di istruzione.

- **Tempo di Clock (**TCLK​**):** Dipende dalla tecnologia dei semiconduttori e dall'organizzazione circuitale.

**Approfondimento:** L'obiettivo del progettista è minimizzare il tempo totale. Spesso modificare una variabile peggiora le altre: ad esempio, semplificare l'ISA (stile RISC) può aumentare il numero di istruzioni (IC), ma permette di costruire un'organizzazione hardware con un clock più veloce o un CPI inferiore.

- **Interrelazione**: I termini dell'equazione ($IC$, $CPI$, $T_{CLK}$) non sono isolati; ad esempio, un cambio nell'ISA per diminuire l'$IC$ può rallentare il clock o aumentare il $CPI$.

- **Correlazioni**: Il compilatore influenza sia l'$IC$ che il $CPI$, mentre l'organizzazione hardware influenza sia il $CPI$ che il tempo di clock.

- **Tecnologia**: La tecnologia dei semiconduttori determina principalmente il tempo di clock.

### 4. Quantificare le Scelte di Progetto: La Legge di Amdahl

Il buon senso ingegneristico suggerisce di **rendere veloce il caso più frequente** ("Make the common case fast"). Ottimizzare l'evento comune garantisce un miglioramento prestazionale molto più alto rispetto all'ottimizzazione del caso raro, ed è spesso più semplice da realizzare.

La **Legge di Amdahl** quantifica questo principio: il guadagno prestazionale ottenuto ottimizzando una parte del sistema è limitato dalla frazione di tempo in cui tale parte viene effettivamente utilizzata. La formula è: Speedupoverall​=(1−Frazionemigliorata​)+(Speedupmigliorato​Frazionemigliorata​​)1​.

**Esempio di approfondimento:** Immaginiamo di voler acquistare una nuova CPU 10 volte più veloce per un server. Tuttavia, il server è pesantemente limitato dall'I/O (I/O bound) e spende il 60% del suo tempo ad aspettare i dischi, utilizzando la CPU solo per il 40% del tempo. Speedupoverall​=(1−0.4)+(100.4​)1​=0.6+0.041​=0.641​=1.56. La natura umana è attratta dal "10X più veloce", ma mantenere la giusta prospettiva dimostra che il miglioramento reale di tutto il sistema è di solo 1.56 volte.



### 5. Altre Metriche: MIPS e MFLOPS (e i loro trabocchetti)

Un'alternativa all'uso del tempo di esecuzione è misurare il tasso di istruzioni completate:

- **MIPS (Millions of Instructions Per Second):** Si calcola come TempoCPU×106IC​ oppure CPI×106FrequenzaClock​.

- **MFLOPS (Millions of Floating-Point Operations Per Second):** Usato per applicazioni scientifiche limitatamente alle operazioni in virgola mobile.

**Trabocchetto (Fallacy/Pitfall):** Utilizzare i MIPS come singola misura di prestazione è fuorviante. I MIPS non tengono conto della capacità funzionale delle istruzioni: un processore CISC potrebbe eseguire meno istruzioni (quindi avere meno MIPS) rispetto a un RISC, ma terminare prima il programma. Inoltre, su una stessa macchina, i MIPS variano da programma a programma e possono perfino indicare un calo di prestazioni se un compilatore sostituisce istruzioni hardware semplici e veloci con istruzioni complesse.

- **Linux perf**: Raccoglie cicli CPU, istruzioni e mispredizioni dei rami.

- **Intel VTune / AMD uProf**: Profiler commerciali per analizzare il $CPI$ e i punti caldi (hotspots) del codice.

- **PAPI**: API per accedere direttamente ai contatori hardware della CPU.

### 6. Benchmark e Sintesi delle Prestazioni

Per avere misurazioni realistiche si utilizzano i **benchmark**, ossia programmi scelti specificatamente per misurare le prestazioni in modo standardizzato. Possono essere programmi reali, kernel (frammenti di codice molto usati), o benchmark sintetici. L'organizzazione **SPEC (System Performance Evaluation Cooperative)** fornisce suite standard, come la SPEC CPU2006 (calcoli intensivi su interi e virgola mobile) e SPECpower per il consumo energetico.

Per riassumere i risultati di più programmi, lo SPEC normalizza i tempi rispetto a una macchina di riferimento creando uno _SPECratio_. La sintesi avviene calcolando la **Media Geometrica** di tali rapporti, la quale garantisce che le prestazioni relative rimangano coerenti indipendentemente dal calcolatore scelto come base per la normalizzazione.

- **Media Aritmetica Pesata**: Da usare se i programmi del benchmark non vengono eseguiti con la stessa frequenza.

- **Media Armonica**: È obbligatorio usarla quando si mediano dei "tassi" o frequenze (come MIPS o mph) per evitare errori di calcolo.

- **Miglioramento**: Premiare lo speedup dei programmi più lenti del benchmark dà un risultato maggiore sulla riduzione del tempo totale.

### 7. L'Integrazione del Parallelismo e il Modello "Roofline" (Dal Libro - Cap. 7)

Come visto, i limiti fisici della potenza hanno forzato il passaggio ai multiprocessori. Mentre i compilatori un tempo potevano parallelizzare il codice implicitamente tramite la pipeline a livello di istruzione (ILP), i processori multicore richiedono la riscrittura esplicita del software (modelli SPMD, SIMD/Vettoriali).

La Legge di Amdahl applicata al calcolo parallelo ci avverte: se parallelizziamo solo l'elaborazione ma ignoriamo, ad esempio, i colli di bottiglia dell'I/O o le frazioni sequenziali, lo speedup effettivo non scalerà con il numero di processori.

Per valutare il bilanciamento tra potenza di calcolo pura e accesso in memoria, i progettisti utilizzano il **Modello Roofline** (Linea del tetto). Questo modello visivo su scala logaritmica traccia:

1. Le prestazioni di calcolo sull'asse Y (es. GFLOPS/sec).

2. L'**intensità aritmetica** sull'asse X (numero di operazioni Floating-Point divise per i Byte di memoria trasferiti). Questo grafico crea una "linea del tetto" orizzontale per il limite di calcolo massimo (Peak FP performance) e una diagonale basata sulla larghezza di banda massima della memoria. Questo permette a programmatori e progettisti di capire immediatamente se l'algoritmo scritto è limitato dal calcolo della CPU (Computation limited) o vincolato ai trasferimenti di memoria (Memory Bandwidth limited), fornendo un target per ulteriori ottimizzazioni.

3. Affidabilità (Dependability e Availability)

Nella progettazione di sistemi estesi (come server e storage), le metriche prestazionali si legano ai fattori di affidabilità ("Dependability"). Si quantifica attraverso:

- **MTTF (Mean Time To Failure):** Misura dell'affidabilità; il tempo medio fino all'interruzione del servizio per un guasto.
- **MTTR (Mean Time To Repair):** Tempo medio necessario per ripristinare il sistema a seguito di un guasto.
- **Disponibilità (Availability):** Percentuale di tempo in cui il sistema eroga correttamente il servizio rispetto al tempo totale, calcolata matematicamente come: **Availability = MTTF / (MTTF + MTTR)**.


**Curva di apprendimento**: Il costo di produzione cala con l'aumentare del volume; raddoppiare il volume riduce tipicamente il costo del 10%.

**Yield (Resa)**: È il fattore chiave che guida la riduzione dei costi dei microprocessori nel tempo.

**Processo**: I chip vengono prodotti su wafer di silicio e poi tagliati in singoli "die" per il test e il packaging.