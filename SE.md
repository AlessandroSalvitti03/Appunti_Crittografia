## 1


**1. L'Informatica e la "Information Society"**

- L'informatica e le telecomunicazioni sono i motori della terza rivoluzione industriale, trasformando radicalmente la società e l'economia globale.
- Le grandi aziende tecnologiche (Tech-Giants come Apple, Microsoft, ecc.) dominano le classifiche mondiali per capitalizzazione, dimostrando come il software sia una tecnologia indispensabile per il successo e un vantaggio competitivo strategico.

**2. La Crisi del Software (Anni '60 e '70)**

- **Evoluzione:** La produzione di software è passata da un'attività artigianale e individuale a un vero e proprio processo industriale, in cui il software ha assunto un ruolo critico.
- **La Crisi:** Negli anni '60 e '70, le tecniche artigianali si sono rivelate inadeguate per gestire la crescente complessità dei sistemi. I progetti superavano regolarmente i tempi e i budget prestabiliti, e spesso fallivano nel soddisfare i requisiti o venivano abbandonati.
- **Esempi di fallimenti celebri:**
    - _Aeroporto di Denver:_ Sistema automatizzato per i bagagli in ritardo di 16 mesi, con un superamento del budget di 560 milioni di dollari a causa di errori nel software.
    - _Therac-25:_ Un errore nel software di controllo di una macchina per radioterapia ha causato la somministrazione di dosi letali di radiazioni a diversi pazienti.
    - _Ariane 5:_ Esplosione di un razzo spaziale europeo poco dopo il lancio a causa di un errore di "integer overrun" nel software di controllo.
    - _London Ambulance Service:_ Il sistema informatico (LASCAD), rilasciato senza test adeguati, è andato in blocco paralizzando l'invio delle ambulanze.

**3. Ingegneria del Software vs. Semplice Programmazione**

- **Definizione:** L'ingegneria del software è l'applicazione di un approccio sistematico, disciplinato e quantificabile allo sviluppo e alla manutenzione del software.
- **Il Ruolo dell'Ingegnere:** A differenza del programmatore, che si concentra principalmente sulla stesura del codice, l'ingegnere del software supervisiona l'intero ciclo di vita del progetto (pianificazione, analisi, design, sviluppo, test e manutenzione). Deve lavorare in team, gestire i vincoli economici e organizzativi, e applicare principi di progettazione per risolvere problemi del mondo reale.

**4. Differenze tra Software e Hardware**

- **Usura vs. Deterioramento:** L'hardware segue una curva di guasti "a vasca da bagno", usurandosi fisicamente nel tempo per usura, polvere o vibrazioni. Il software non si usura fisicamente, ma **si deteriora**. Ogni volta che il software viene modificato o aggiornato, c'è un'alta probabilità di introdurre nuovi difetti ("side effects"), che causano picchi nel tasso di guasti nel corso del tempo.
- **Manutenzione:** Le attività di manutenzione del software assorbono molte più risorse e personale rispetto alla sua creazione iniziale.

**5. Qualità e Principi del Software**

- Le qualità principali che un buon software deve possedere includono: **Affidabilità** (resilienza ai guasti), **Efficienza** (performance), **Sicurezza**, **Manutenibilità** (adattabilità e portabilità) e **Usabilità**.
- Per raggiungere queste qualità, si applicano principi chiave come rigore, modularità, separazione delle preoccupazioni, astrazione e anticipazione del cambiamento (principi che spesso richiedono un approccio Object-Oriented).

**6. Obiettivi e Contenuti del Corso**

- Il corso mira a farti acquisire tecniche di analisi e progettazione orientata agli oggetti (OOA e OOD), utilizzando processi di sviluppo iterativi e agili come il Unified Process (UP) e SCRUM.
- **Temi chiave:** Architetture software (Monolitiche vs Microservizi), requisiti (Use cases, User Stories), principi di design (SOLID, GRASP) e Design Patterns, object persistence tramite ORM (come Hibernate), Unified Modeling Language (UML) e l'uso di tool CASE (Computer-Aided Software Engineering) come Visual Paradigm.

## 2


**1. Il Processo Software: Il Modello a Cascata (Waterfall)**

- **Cos'è:** È il modello classico, più vecchio e diffuso per lo sviluppo del software. Si basa su un ciclo di vita sequenziale e lineare, che scorre verso il basso (come una cascata) attraverso fasi ben distinte: analisi dei requisiti, design, implementazione, verifica (testing) e manutenzione.
- **L'illusione di base:** Il modello presume che si possano catturare tutti i requisiti all'inizio, procedere all'analisi, poi al design e infine alla scrittura del codice. Si basa sull'assunto, spesso errato, che le specifiche siano prevedibili, stabili e non soggette a cambiamenti frequenti.
- **Perché spesso fallisce (Le insidie):**
    - I rischi vengono affrontati troppo tardi nel ciclo di vita ed è molto costoso correggere gli errori introdotti all'inizio.
    - I progetti reali raramente seguono un flusso puramente sequenziale. I cambiamenti generano confusione.
    - I clienti fanno fatica a definire esplicitamente tutti i requisiti fin dall'inizio e devono aspettare molto tempo prima di poter vedere una versione funzionante del programma.
    - Studi empirici dimostrano che un progetto software tipico subisce un cambiamento dei requisiti del 25%, percentuale che sale al 35-50% nei progetti di grandi dimensioni. **Il cambiamento è l'unica costante**.
    - Manca di strumenti per la valutazione e la gestione dei rischi in corso d'opera.
- **Quando funziona:** È utile solo quando i requisiti sono perfettamente compresi fin dall'inizio e lo sviluppo è vincolato da rigidi accordi contrattuali.

**2. Qualità del Software e Problemi di Design: Il Caso di Studio del Labirinto (Maze)**

- **Lo Scenario:** L'azienda ha un'app di successo (un gioco del labirinto). Un concorrente lancia un'app migliore e l'azienda, per non perdere quote di mercato, deve aggiungere nuove funzionalità (come "porte magiche" o "stanze incantate").
- **Il Problema di Design:** Analizzando il codice sorgente (scritto in C++), si nota che la funzione principale `createMaze()` crea le stanze e i muri istanziando esplicitamente le classi base tramite costruttori (es. `new Room(1)`, `new Wall`).
- **La trappola del "Copy & Change":** Per aggiungere le nuove stanze senza toccare il labirinto originale, la soluzione più istintiva sembrerebbe copiare e incollare il codice di `createMaze()` modificandolo. Tuttavia, questo approccio porta ad avere due copie distinte dello stesso codice, raddoppiando il lavoro di manutenzione futuro.
- **Cattiva Progettazione:** Questo è un tipico esempio di scarsa qualità e rigidità del software. L'uso diretto e intensivo dei costruttori crea un legame troppo stretto (esplicito) tra l'oggetto creatore e le classi utilizzate, rendendo il codice difficile da modificare.

**3. Il Principio Open-Closed e i Benefici del Buon Design**

- **Violazione del Principio:** Il caso del labirinto viola palesemente il principio _Open-Closed_ (Aperto-Chiuso), il quale stabilisce che "le entità software dovrebbero essere aperte alle estensioni, ma chiuse alle modifiche". In altre parole, dovresti poter aggiungere nuovi comportamenti senza dover modificare il codice sorgente esistente.
- **Perché progettare bene:** Un buon design riduce la complessità (rendendo il codice più facile da capire e modificare), facilita lo sviluppo rapido, garantisce una solida base per la manutenzione futura, incentiva il riuso del codice ed espone più facilmente i difetti durante i test.

_Nota: Se con "ora di questo" intendevi accettare la mia proposta precedente di generare un set di **Flashcard** per lo studio o un **Report** per questo specifico documento, dimmi pure quale dei due formati preferisci e avvierò subito la creazione!_

## 3

**1. Il Fallimento del Modello a Cascata (Waterfall)**

- **Limiti:** Il modello a cascata (analisi -> design -> implementazione -> test -> manutenzione) affronta i rischi troppo tardi nel ciclo di sviluppo ed è molto costoso per recuperare gli errori iniziali.
- **Falsa assunzione:** Si basa sull'idea errata che le specifiche siano prevedibili e stabili fin dall'inizio.
- **Dati reali:** Gli studi dimostrano che in un progetto tipico i requisiti cambiano del 25% (fino al 50% in grandi progetti). Circa il 45% delle funzionalità specificate all'inizio con il modello a cascata non viene mai utilizzato dagli utenti.

**2. Lo Sviluppo Iterativo ed Evolutivo**

- **Cos'è:** Lo sviluppo viene diviso in una serie di mini-progetti brevi chiamati **iterazioni**.
- **Ogni iterazione** include tutte le fasi: analisi, design, implementazione e test. Il risultato è un sistema parziale testato ed eseguibile (anche se non pronto per la produzione finale).
- **Gestione del cambiamento:** A differenza del metodo a cascata, il modello iterativo accetta e "abbraccia" il cambiamento. Grazie ai continui feedback di utenti e test, il sistema si adatta ed evolve progressivamente verso i requisiti corretti.

**3. La Regola del "Timeboxing"**

- Le iterazioni hanno una lunghezza fissa (solitamente tra 2 e 6 settimane).
- **Regola d'oro:** La data di scadenza di un'iterazione non si può posticipare. Se ci sono difficoltà, si riducono le funzionalità o i task da completare (pratica del _de-scope_), ma si rispetta la scadenza. Iterazioni troppo lunghe (>6 settimane) ritardano i feedback e aumentano la complessità.

**4. Lo Unified Process (UP) e le sue 4 Fasi** L'UP è un famoso framework di sviluppo iterativo. Divide il ciclo di vita del progetto in quattro macro-fasi:

1. **Inception (Avvio):** Si definisce la visione approssimativa, l'ambito e si fanno stime vaghe dei costi. Non è una fase per definire tutti i requisiti.
2. **Elaboration (Elaborazione):** Si realizza il nucleo (core) dell'architettura software, si risolvono i rischi più alti e si identificano la maggior parte dei requisiti.
3. **Construction (Costruzione):** È la fase più lunga, in cui si sviluppano iterativamente gli elementi a minor rischio e più facili, preparando il sistema per il rilascio.
4. **Transition (Transizione):** Completamento del prodotto, beta test e distribuzione.

**5. Pianificazione Guidata dai Rischi e dai Clienti**

- Le prime iterazioni devono concentrarsi sui **rischi più alti** (come costruire e stabilizzare l'architettura software di base) e sulle **funzionalità più visibili e importanti** per il cliente. Non avere un'architettura solida è uno dei rischi principali.

**6. Agilità e Personalizzazione (Agile UP)**

- L'UP è flessibile: incoraggia l'integrazione di pratiche da altri metodi agili (come Scrum o Extreme Programming).
- **Tutto è opzionale:** Ad eccezione della scrittura del codice, tutti gli altri "artefatti" (documenti, diagrammi UML, modelli) sono opzionali. Un team dovrebbe creare solo i documenti che portano un reale valore aggiunto al progetto, mantenendo il processo semplice e leggero.
- **Il pericolo del "Waterfall thinking":** Un errore comune è applicare una mentalità a cascata dentro un progetto iterativo (es. "scriviamo tutti i casi d'uso prima di programmare"). Questa mentalità va evitata.


## 4

Ecco il riassunto strutturato di quest'ultimo documento, che introduce i **Casi di Studio** utilizzati nel corso per mettere in pratica l'analisi e la progettazione orientata agli oggetti (OOA/D):

**1. Perché usare i Casi di Studio?**

- Il corso utilizza casi di studio specifici perché rappresentano problemi familiari a molti, ma allo stesso tempo sono ricchi di complessità e sfide progettuali interessanti.
- Questo approccio permette di concentrarsi sull'apprendimento dei concetti fondamentali (sviluppo iterativo, OOA/D, UML, pattern) senza dover spendere troppo tempo per comprendere il problema di partenza. Anche l'apprendimento nel corso avverrà in modo iterativo, seguendo le iterazioni di sviluppo del progetto.

**2. Caso di Studio Principale: Il Sistema NextGen POS**

- **Cos'è:** Un sistema POS (Point of Sale, ovvero il punto cassa) di nuova generazione, utilizzato nei negozi al dettaglio per registrare le vendite e gestire i pagamenti. Comprende hardware (computer, scanner di codici a barre) e software.
- **Requisiti di Flessibilità:** A differenza dei vecchi sistemi rigidi, il "NextGen" deve essere altamente personalizzabile per poter essere venduto a clienti diversi con regole di business differenti. Deve supportare regole di sconto variabili nel tempo e interfacciarsi con vari sistemi esterni che possono cambiare (es. calcolatore di tasse, controllo inventario).
- **Tolleranza ai guasti (Fault-tolerance):** Il sistema deve essere robusto; ad esempio, se il server remoto dell'inventario è momentaneamente irraggiungibile, il POS deve comunque permettere di registrare le vendite in contanti per non bloccare l'attività del negozio.
- **Interfacce multiple:** Deve supportare diverse tipologie di client, come browser web, interfacce touch screen (es. Java Swing) e palmari (PDA) wireless.

**3. L'Architettura a Livelli (Layering) e il Focus del Corso**

- Le applicazioni software sono generalmente organizzate in un'architettura a livelli. I tre livelli più comuni sono: **Presentazione** (Interfaccia Utente - UI), **Logica Applicativa** e **Servizi Tecnici** (come l'accesso al database).
- **Focus principale:** Il corso e i casi di studio si concentreranno principalmente sul livello della **Logica Applicativa** (dove risiedono oggetti come "Vendita" o "Pagamento").
- **Perché?** Mentre i livelli di interfaccia o i servizi tecnici dipendono fortemente dalla tecnologia o dalla piattaforma usata, la progettazione orientata agli oggetti (OO) del livello logico è molto simile attraverso tutte le tecnologie. Le abilità imparate qui sono applicabili ovunque.

**4. Richiamo allo Sviluppo Iterativo (UP)**

- Il documento ribadisce l'importanza dello sviluppo iterativo guidato dai rischi e dal cliente.
- Le prime iterazioni devono mirare a **costruire e stabilizzare l'architettura di base**, poiché non avere un'architettura solida rappresenta uno dei rischi più alti di un progetto.

**5. Secondo Caso di Studio: Il Gioco del Monopoly**

- **Scopo:** Viene introdotto un secondo caso di studio, completamente diverso, per dimostrare che le pratiche di OOA/D, l'UML e i design pattern sono universali e applicabili anche a domini che non c'entrano nulla con i sistemi aziendali.
- **Come funziona:** Sarà una simulazione software del gioco in cui un utente avvia il programma, imposta il numero di giocatori virtuali e osserva semplicemente il tracciamento (trace) delle mosse simulate mentre la partita si svolge da sola.


## 5

Ecco il riassunto strutturato del capitolo dedicato alla fase di Inception e alla gestione evolutiva dei requisiti:

**1. La Fase di Inception (Avvio)**

- **Cos'è:** È la fase iniziale e solitamente breve del ciclo di sviluppo iterativo.
- **Scopo Principale:** Serve a stabilire una visione comune tra le parti, definire il perimetro (scope) iniziale, valutare la fattibilità tecnica ed economica e creare il _business case_ (la giustificazione per l'avvio del progetto). L'obiettivo è rispondere a domande cruciali: "Conviene procedere o fermarsi?", "Le stime indicano 10.000$ o milioni di dollari?", "Compriamo una soluzione già fatta o la costruiamo?".
- **Cosa si produce:** Si analizza solo una minima parte dei casi d'uso (circa il 10-20%), si identificano i requisiti non-funzionali critici e si prepara l'ambiente di sviluppo per la fase successiva (Elaboration).
- **Modellazione Agile:** I documenti (artefatti) creati in questa fase sono spesso abbozzati o opzionali. Il vero valore della modellazione non è produrre specifiche rigide e infallibili, ma migliorare la comprensione del problema all'interno del team.

**2. I Segnali di una Cattiva Inception (Waterfall Thinking) Stai sbagliando approccio e applicando una mentalità a cascata se in questa fase:**

- Il processo dura più di "poche settimane".
- Tenti di definire tutti i requisiti (o non ne definisci nessuno, invece del 10-20% consigliato).
- Ti aspetti che i piani e le stime dei costi siano già del tutto affidabili.
- Provi a definire l'architettura del software (questa andrà sviluppata iterativamente nella successiva fase di Elaborazione).

**3. I Requisiti: Evolutivi vs Modello a Cascata**

- **Definizione:** Un requisito è una capacità che il sistema deve possedere o una condizione che deve soddisfare, e nasce sempre da esigenze reali degli _stakeholder_ (le parti interessate). I _Function Point_ sono un'unità di misura usata per calcolare la dimensione di questi requisiti funzionali.
- **Il fallimento della gestione tradizionale:** Pretendere di bloccare tutti i requisiti all'inizio (modello a cascata) è una delle cause principali del fallimento dei progetti software. In media, il 25% dei requisiti cambia durante lo sviluppo, e addirittura il 45% delle funzionalità inizialmente previste con il modello a cascata non viene poi mai utilizzato dagli utenti.
- **La risposta iterativa (UP):** I processi come lo Unified Process "abbracciano" il cambiamento. Iniziano la stesura del codice molto prima che i requisiti siano dettagliati, in modo da usare i test e le versioni preliminari per ottenere feedback continui e far evolvere i requisiti stessi.

**4. Classificazione dei Requisiti: Il Modello FURPS+** I requisiti si dividono in funzionali (le funzioni vere e proprie) e non-funzionali (gli attributi di qualità dell'intero sistema). Un modello efficace per classificarli è il **FURPS+**:

- **F (Functional):** Funzionalità, caratteristiche e capacità.
- **U (Usability):** Usabilità, fattori umani e documentazione.
- **R (Reliability):** Affidabilità, gestione dei guasti e ripristino.
- **P (Performance):** Prestazioni, tempi di risposta e disponibilità del sistema.
- **S (Supportability):** Sostenibilità, flessibilità, manutenibilità e adattabilità.
- **Il segno "+":** Include tutti i vincoli secondari (es. limitazioni hardware, interfacce verso sistemi esterni, vincoli legali e di licenza).

**5. Gli Artefatti per i Requisiti (UP)** Anche se non vengono completati nell'Inception, si iniziano a strutturare documenti come:

- **Use-Case Model:** I casi d'uso che descrivono gli scenari funzionali.
- **Supplementary Specification:** Raccoglie i requisiti non-funzionali e quelli funzionali non facilmente esprimibili come casi d'uso (es. formattazione di un report).
- **Glossary:** Dizionario dei dati e terminologia del dominio.
- **Vision:** Riassume ad alto livello le idee del progetto.
- **Business Rules:** Regole generali di dominio (es. leggi fiscali o aziendali) che vanno oltre la singola applicazione.

## 6-7
Ecco il riassunto strutturato di questi due nuovi documenti, che introducono uno degli argomenti centrali dell'ingegneria del software: i **Casi d'Uso (Use Cases)**.

**1. Cosa sono i Casi d'Uso?**

- **Definizione:** Sono storie testuali (non diagrammi) che descrivono come un attore utilizza un sistema per raggiungere un obiettivo. Rappresentano un modo eccellente per catturare i requisiti funzionali.
- **Perché si usano:** Sostituiscono le vecchie e incomprensibili "liste di funzionalità" (es. "il sistema deve fare X, deve fare Y") con scenari orientati all'utente. Questo approccio migliora la comprensione, tiene gli utenti coinvolti e riduce il rischio di sviluppare cose inutili.

**2. Concetti Base: Attori e Scenari**

- **Attori:** Sono entità con un comportamento che interagiscono con il sistema (possono essere persone, altre macchine o organizzazioni). Si dividono in tre tipi:
    - _Primari:_ Hanno un obiettivo che vogliono raggiungere usando il sistema (es. il Cassiere).
    - _Di supporto:_ Forniscono un servizio al sistema (es. il servizio esterno di autorizzazione dei pagamenti).
    - _Fuori scena (Offstage):_ Hanno un interesse nel comportamento del sistema, ma non interagiscono direttamente (es. l'Agenzia delle Entrate).
- **Scenario:** È una specifica sequenza di azioni (una "istanza" del caso d'uso). Può essere uno scenario di successo oppure di fallimento (es. pagamento rifiutato). Un caso d'uso è, di fatto, un insieme di scenari correlati.

**3. I Formati dei Casi d'Uso** I casi d'uso possono essere scritti a vari livelli di dettaglio:

- **Brief (Breve):** Un solo paragrafo riassuntivo, utile nelle fasi iniziali.
- **Casual (Informale):** Qualche paragrafo discorsivo che copre vari scenari senza troppa formalità.
- **Fully Dressed (Completo):** Estremamente dettagliato e strutturato. È il formato più importante per lo sviluppo.

**4. Anatomia di un Caso d'Uso "Fully Dressed"**

- **Stakeholders e Interessi:** Elenca chi ha interesse nel caso d'uso e cosa vuole. Aiuta a definire cosa _deve_ esserci nel caso d'uso per soddisfare tutti i requisiti.
- **Pre e Post-condizioni:** Cosa deve essere garantito prima di iniziare e cosa è garantito alla fine (se ha successo).
- **Main Success Scenario (Happy Path):** È il percorso "ideale", in cui tutto va bene e non ci sono condizioni o "se" (no branching). Descrive l'interazione tra attore e sistema.
- **Extensions (Flussi Alternativi):** Costituiscono la maggior parte del testo. Qui si gestiscono tutti i fallimenti e le eccezioni (es. "Se l'articolo non si trova", "Se il cliente vuole pagare a rate").

**5. Linee Guida per una Buona Scrittura (Stile Black-Box ed Essenziale)**

- **Stile Black-Box:** Descrivi _cosa_ fa il sistema, non _come_ lo fa. Non menzionare database, SQL o componenti interni. Il sistema ha solo "responsabilità".
- **Stile Essenziale (Niente UI):** Concentrati sulle intenzioni dell'attore. Evita di scrivere "L'utente clicca sul pulsante OK nella finestra X". Scrivi piuttosto "L'utente si autentica".

**6. Come trovare i Casi d'Uso e Valutarli**

- I passaggi sono: 1) Definire il confine del sistema, 2) Identificare gli attori primari, 3) Identificare i loro obiettivi, 4) Definire i casi d'uso per quegli obiettivi.
- **I Test di validità:** Come si capisce se un caso d'uso è buono?
    - _Il test EBP (Elementary Business Process):_ Aggiunge un valore reale al business e lascia i dati in uno stato coerente?
    - _Il test della Dimensione:_ Ha dai 3 ai 10 passi? Se è una singola azione (come "Fare il Log in" o "Muovere un pezzo sulla scacchiera"), non è un caso d'uso di livello utente, ma una semplice sotto-funzione.

**7. I Casi d'Uso nel Processo Iterativo (UP)**

- Lo sviluppo è _Use-case driven_ (guidato dai casi d'uso).
- Durante l'Inception (Avvio) si definisce in dettaglio solo circa il 10% dei casi d'uso (quelli più critici). Il grosso del lavoro di stesura avviene iterativamente durante la fase di Elaboration.

## 8
Ecco il riassunto strutturato di questo capitolo, che esplora gli **altri artefatti (documenti) fondamentali** da produrre durante la fase di Inception, oltre ai Casi d'Uso:

**1. Oltre i Casi d'Uso nell'Inception**

- I casi d'uso, da soli, non bastano per completare la fase di Inception.
- In questa fase **non si fa un'analisi profonda e dettagliata** di tutti i requisiti. Tuttavia, è fondamentale individuare una "Top 10" dei requisiti principali e dedicare tempo a indagare i requisiti non-funzionali, poiché questi influenzano pesantemente le scelte architetturali future.

**2. Il Documento di Vision (Visione)**

- **Cos'è:** È un "executive summary" (riassunto esecutivo) che comunica le grandi idee e la visione generale del progetto. Serve a dare un contesto comune a tutti i principali attori coinvolti.
- **Caratteristiche:** Non deve essere lungo e non deve tentare di descrivere nel dettaglio requisiti rigidi. Riassume il problema, identifica gli stakeholder (chi sono e che problemi hanno) e definisce gli obiettivi di alto livello degli utenti (User-Level Goals).
- **Scrittura iterativa:** Non c'è un ordine rigido per scriverla. Di solito si crea una prima bozza, si identificano gli obiettivi utente per trovare i casi d'uso, si scrivono alcuni casi d'uso e infine si affina la Vision aggiornandola.

**3. Supplementary Specification (Specifiche Supplementari)**

- **Scopo:** Raccoglie tutti i requisiti non-funzionali (categoria URPS+ del modello FURPS: Usability, Reliability, Performance, Supportability) e tutte le informazioni o vincoli che non si riescono a descrivere facilmente nella storia di un caso d'uso.
- **Cosa contiene:** Vincoli hardware e software, sistemi operativi di riferimento, interfacce utente, questioni di licenza e legali, documentazione, standard di sicurezza, performance, regole di packaging e internazionalizzazione.
- **Nel caso di studio NextGen POS:** Includono la necessità di supportare specifiche interfacce hardware (touch screen, lettori barcode, stampanti di scontrini), i requisiti di configurabilità per clienti diversi e il vincolo imposto dal management di utilizzare tecnologie **Java** per facilitare lo sviluppo e la portabilità.

**4. Il Glossario (Glossary)**

- **A cosa serve:** È una lista di termini notevoli e delle relative definizioni. Previene ambiguità e problemi di comunicazione, assicurando che tutti gli stakeholder usino lo stesso linguaggio per concetti specifici del dominio.
- **Dizionario dei dati:** Durante la fase di Elaboration, il glossario si espande diventando un vero e proprio dizionario dei dati. Oltre alla definizione, include alias, formati (tipo, lunghezza, unità di misura), relazioni, range di valori consentiti e regole di validazione. Queste ultime sono a tutti gli effetti dei requisiti che impattano il comportamento del sistema.

**5. Business Rules (Regole di Dominio o Aziendali)**

- **Cosa sono:** Dettano le regole di funzionamento di un intero business o dominio (es. policy aziendali, leggi governative sulle tasse, leggi fisiche per un simulatore).
- **Indipendenza:** Poiché queste regole spesso trascendono la singola applicazione (valgono per tutta l'azienda o per legge), andrebbero scritte in un documento separato e indipendente dall'applicazione, così da poterle riutilizzare in altri progetti. Le regole puramente _specifiche dell'applicazione_ (es. una regola di sconto passeggera per un negozio) si mettono invece nelle Specifiche Supplementari.
- **Changeability (Variabilità):** Nel documentare queste regole è cruciale indicare il loro livello di mutevolezza (es. una regola fiscale governativa può cambiare spesso, mentre il divieto di rimborsare contanti su una carta di credito ha una variabilità bassa).

**6. Condivisione e Versionamento**

- Tutti questi artefatti dovrebbero risiedere online, ad esempio sul sito o portale del progetto (es. wiki, Google Docs o tool di project management) affinché l'intero team possa accedervi. Devono essere soggetti a "versioning" per tracciare chi ha fatto l'ultima modifica, cosa ha modificato e quando.

## 9


Ecco il riassunto strutturato di questo nuovo documento, che segna il passaggio fondamentale dalla fase iniziale (Inception) a quella di indagine vera e propria (Elaboration):

**1. Riepilogo: Cosa abbiamo ottenuto dall'Inception?**

- L'Inception è una fase molto breve (a volte dura solo una settimana) che non ha lo scopo di raccogliere tutti i requisiti, ma di determinare la fattibilità di base, i rischi e l'ambito del progetto per decidere se vale la pena proseguire.
- Alla fine di questa fase abbiamo: la maggior parte degli attori e dei casi d'uso identificati (ma solo il 10% scritto in modo dettagliato), i requisiti di qualità più rischiosi, le prime bozze di _Vision_ e _Supplementary Specification_, alcuni prototipi di interfaccia e una primissima architettura candidata.

**2. Entrare nella Fase di Elaboration (Elaborazione)**

- È la prima serie di iterazioni in cui il team inizia a fare indagini approfondite.
- **L'obiettivo in una frase:** Costruire l'architettura di base (core), risolvere gli elementi ad alto rischio, definire la maggior parte dei requisiti e stimare le tempistiche e le risorse complessive.
- **Codice reale, non bozze:** Durante l'Elaboration non si creano prototipi usa-e-getta. Il codice e il design realizzati costituiscono parti di qualità "da produzione" che finiranno nel sistema finale.

**3. Pianificazione Adattiva e Suddivisione del Lavoro**

- Spesso un caso d'uso è troppo complesso per essere completato in una singola e breve iterazione; pertanto, scenari diversi dello stesso caso d'uso vengono spalmati su più iterazioni.
- **Come si sceglie cosa fare prima?** La pianificazione assegna priorità in base a tre criteri: **Rischio** (complessità tecnica o incertezza), **Copertura** (toccare a grandi linee tutte le parti principali del sistema nelle prime fasi) e **Criticità** (funzionalità con un alto valore di business per il cliente).
- La pianificazione è _adattiva_, ovvero viene rifatta e aggiustata prima di ogni nuova iterazione.

**4. La Prima Iterazione nei Nostri Casi di Studio (Eccezione Didattica)**

- Attenzione: mentre in un progetto reale la prima iterazione affronterebbe subito i rischi maggiori e l'architettura, nel nostro corso l'Iterazione 1 è guidata da **obiettivi didattici**. Inizia con argomenti facili per insegnare le basi dell'OOA/D.
- **Nel NextGen POS:** Si implementerà solo lo scenario base in cui si inseriscono articoli e si paga in contanti, oltre alla fase di avvio (Start Up). Non ci saranno calcoli complessi per le tasse o regole di prezzo esterne.
- **Nel Monopoly:** Si implementerà uno scenario base con 2-8 giocatori che lanciano i dadi e si muovono sul tabellone per 20 turni. Non verrà gestito il denaro, né l'acquisto di proprietà o l'esistenza di un vincitore/perdente.

**5. I Nuovi Documenti (Artefatti) dell'Elaboration** Oltre ad aggiornare i documenti precedenti, in questa fase si iniziano a produrre modelli più tecnici:

- **Domain Model:** Una visualizzazione statica dei concetti del dominio.
- **Design Model:** Diagrammi logici (classi, oggetti, interazioni).
- **Software Architecture Document:** Un riassunto delle scelte chiave di progettazione.
- **Data Model:** Gli schemi per il database.
- **Use-Case Storyboards/UI Prototypes:** Descrizioni della navigazione e dell'interfaccia utente.

## 10

Ecco il riassunto strutturato di questo documento, che introduce i concetti fondamentali dell'**Analisi e Progettazione Orientata agli Oggetti (OOA/D)** e spiega come trasformare i requisiti in codice funzionante:

**1. Gestire la Complessità del Software**

- Sviluppare software è un'attività estremamente complessa perché c'è un'enorme differenza tra i requisiti iniziali (input) e il codice finale (output).
- Per gestire questa complessità, il processo di sviluppo scompone questa grande trasformazione in una sequenza di passaggi più piccoli e semplici (paragonati al "morphing" delle immagini): **Requisiti $\rightarrow$ Analisi OO $\rightarrow$ Design OO $\rightarrow$ Implementazione $\rightarrow$ Testing**.

**2. Analisi vs. Design: "Cosa" vs. "Come"** Queste due fasi hanno obiettivi molto diversi ma sono inseparabili e sinergiche:

- **Analisi Orientata agli Oggetti (OOA) - _Il Cosa_:** Si concentra sull'investigare il problema e capire i requisiti. L'obiettivo è "fare la cosa giusta". In questa fase si identificano e si descrivono i **concetti del dominio del problema (il mondo reale)**.
- **Design Orientato agli Oggetti (OOD) - _Il Come_:** Si concentra sul trovare una soluzione concettuale che soddisfi i requisiti. L'obiettivo è "fare le cose nel modo giusto". In questa fase si definiscono gli **oggetti software** e si assegnano loro specifiche responsabilità (metodi e dati).

**3. I Tre Aspetti Modellati dall'Analisi (OOA)** Durante l'analisi, si studiano tre aspetti principali del sistema:

- **Informazione:** I dati del dominio gestiti dall'applicazione. Vengono rappresentati tramite il **Modello di Dominio (Domain Model)**, che mostra i concetti del mondo reale, le loro associazioni e i loro attributi.
- **Funzioni:** Le operazioni che il sistema deve eseguire per soddisfare i requisiti. Vengono descritte nell'ordine in cui si verificano attraverso i **System Sequence Diagrams** (Diagrammi di Sequenza di Sistema).
- **Comportamento:** Come cambiano le informazioni in seguito all'esecuzione delle funzioni. Questo effetto è descritto nei **Contratti Operativi (Operation Contracts)**.

**4. Gli Artefatti della Progettazione (OOD)** Partendo dagli artefatti dell'analisi, il Design produce i modelli per il software:

- **Diagrammi di Interazione (Interaction Diagrams):** Forniscono una **visione dinamica**. Mostrano come gli oggetti software comunicano e collaborano tra loro per raggiungere un certo obiettivo.
- **Diagrammi delle Classi (Class Diagram):** Forniscono una **visione statica** della struttura delle classi software, definendo i loro attributi e i metodi.

**5. L'Esempio del Gioco dei Dadi (Realtà vs Software)** Il documento utilizza il gioco dei dadi per mostrare una lezione fondamentale:

- Nella **realtà** (fase di Analisi), è il _Giocatore_ che fisicamente lancia i dadi.
- Nel **software** (fase di Design), le scelte progettuali sono diverse: è l'oggetto software `DiceGame` che invoca il metodo per far "lanciare" gli oggetti `Die`.
- _Il principio chiave:_ La programmazione orientata agli oggetti (OOP) si ispira ai concetti del dominio (il mondo reale), ma **il design delle classi non è una traduzione 1:1, né una simulazione esatta di ciò che accade nella realtà**.

## 11-12
Ecco il riassunto strutturato di questi nuovi documenti, interamente dedicati al **Modello di Dominio (Domain Model)**, che rappresenta lo strumento più importante dell'Analisi Orientata agli Oggetti (OOA):

**1. Cos'è il Modello di Dominio?**

- **Definizione:** È una rappresentazione visiva dei concetti del mondo reale (o classi concettuali) rilevanti nel dominio del problema, accompagnati dalle loro relazioni e attributi.
- **Cosa NON è:** Non è un diagramma di classi software, non modella i dati di un database relazionale (Data model) e non contiene metodi o funzioni. È una fotografia del vocabolario del business, non dell'architettura informatica.
- **Basso divario rappresentativo (Low Representational Gap):** Il suo scopo principale è ispirare la creazione delle future classi software. Dando agli oggetti software gli stessi nomi dei concetti reali (es. _Vendita_ o _Pagamento_), si riduce il divario tra come gli stakeholder vedono il mondo e come viene codificato, facilitando lo sviluppo e la comunicazione.

**2. I Tre Elementi del Modello** Un Modello di Dominio è composto da tre elementi:

- **Classi Concettuali:** I concetti veri e propri, composti da un simbolo (il nome), un'intensione (la definizione) e un'estensione (l'insieme degli oggetti reali a cui si applica). Non avendo natura software, non possiedono operazioni/metodi.
- **Associazioni:** I collegamenti logici tra due classi.
- **Attributi:** Le proprietà di base di una classe.

**3. Come trovare le Classi Concettuali**

- **Analisi Linguistica:** Consiste nell'analizzare il testo dei Casi d'Uso (particolarmente nello scenario principale) e identificare i **sostantivi** (es. "Il _Cliente_ arriva alla _Cassa_ con gli _Articoli_"). Bisogna fare attenzione perché il linguaggio umano può essere ambiguo.
- **Liste di Categorie:** Si possono usare liste predefinite per individuare concetti sfuggiti. Tra le categorie più comuni ci sono: transazioni (es. _Vendita_), linee di transazione (es. _Linea di Vendita_), luoghi (_Negozio_), ruoli (_Cassiere_) o oggetti fisici (_Registratore di cassa_).
- **Il problema delle Classi "Descrizione":** A volte è necessario aggiungere classi speciali (es. _ProductDescription_) invece di mettere la descrizione dentro la classe dell'oggetto fisico (_Item_). Questo si fa per evitare di duplicare le informazioni per ogni articolo e per non perdere i dati se un articolo viene venduto e rimosso dal sistema.

**4. Regole per le Associazioni**

- **Associazioni "Need-to-remember":** Inserisci solo relazioni che è necessario ricordare nel tempo (es. la connessione tra una _Vendita_ e gli _Articoli_ venduti, altrimenti non si potrebbe stampare uno scontrino). Evita di mettere troppe linee per non creare confusione visiva.
- **Nomenclatura e Navigabilità:** Si leggono idealmente come "Classe - Verbo - Classe" (es. _Flight_ Flies-to _Airport_) e la freccia indica solo la direzione di lettura, non una dipendenza software.
- **Molteplicità:** Numeri o intervalli posti agli estremi della linea (es. 1, 0..1, 1..*, *) indicano quanti oggetti possono essere validamente associati. Esprimono importanti regole di business (es. una persona può avere un solo impiego in un dato momento).

**5. Regole per gli Attributi**

- Vanno inseriti per registrare le informazioni necessarie per gli scenari attuali (es. data per la vendita, indirizzo per il negozio).
- Si devono usare tipi di dati primitivi (numeri, stringhe).
- **Nuovi Tipi di Dato (Data Types):** Se un valore ha sezioni separate (es. un codice fiscale o l'ID univoco di un articolo), regole di validazione o un'unità di misura (es. il denaro), non bisogna usare un semplice numero, ma creare una classe a parte (es. una classe _Money_ collegata al _Pagamento_).
- **Attenzione alla "Sindrome della Chiave Esterna":** Nel Modello di Dominio non bisogna **mai** usare un attributo per fare riferimento a un'altra classe (es. non mettere `currentRegisterNumber` come attributo dentro il `Cashier`), ma usare sempre un'associazione visiva.

_Nota finale del documento: Non esiste il modello di dominio "perfetto", esiste solo un modello "utile" a comunicare chiaramente._

## 13
Ecco il riassunto strutturato di questo nuovo documento, che introduce due artefatti fondamentali dell'Analisi Orientata agli Oggetti (OOA) necessari per mappare l'interazione tra utente e software: i **System Sequence Diagrams (SSD)** e i **Contratti Operativi (Operation Contracts)**.

**1. System Sequence Diagrams (SSD)**

- **Cosa sono:** Un SSD è un diagramma che illustra gli eventi di input e di output che si verificano tra gli attori esterni e il sistema in discussione. Il tempo scorre dall'alto verso il basso.
- **Il sistema come "Scatola Nera" (Black Box):** Nell'analisi, l'intero sistema viene trattato come una singola scatola nera. L'SSD non mostra gli oggetti software interni (che verranno decisi in fase di Design), ma si concentra solo sugli eventi che attraversano il confine del sistema.
- **Eventi e Operazioni di Sistema:** Durante l'interazione (descritta nei casi d'uso), un attore genera degli "eventi di sistema" (input esterni). Ogni evento di sistema rappresenta una richiesta affinché il sistema esegua una determinata "operazione di sistema" per gestire quell'evento.
- **Linee guida per il disegno:**
    - Si dovrebbe disegnare un SSD per lo scenario principale di successo di ogni caso d'uso e per gli scenari alternativi più frequenti o complessi.
    - Mostrano l'attore principale, il sistema e i passaggi dell'interazione.
    - Le interazioni avviate dall'attore vengono mostrate come messaggi con eventuali parametri (es. l'ID di un articolo e la quantità).
- **Nomenclatura corretta:** I nomi degli eventi devono iniziare con un verbo e rappresentare l'**intenzione astratta** dell'attore, non il dispositivo fisico utilizzato. Ad esempio, è corretto usare `enterItem` (inserisci articolo) ed è sconsigliato usare `scan` (scansiona). Le risposte del sistema sono indicate come semplici dati di ritorno, non come azioni.

**2. Operation Contracts (Contratti Operativi)**

- **A cosa servono:** Mentre i casi d'uso sono il testo principale per descrivere il comportamento, a volte serve una descrizione molto più dettagliata. I contratti operativi descrivono in dettaglio come cambia lo stato degli oggetti nel **Modello di Dominio** in seguito all'esecuzione di un'operazione di sistema.
- **La struttura di un contratto:** È composta dal nome dell'operazione, dai riferimenti ai casi d'uso (Cross References), dalle **Precondizioni** (assunzioni notevoli sullo stato del sistema prima dell'operazione) e dalle **Postcondizioni** (la sezione più importante).
- **Postcondizioni - Regole fondamentali:**
    - Descrivono cosa è cambiato nel Modello di Dominio una volta che l'operazione è terminata. Possono includere solo tre tipi di modifiche: **1)** creazione o cancellazione di istanze (oggetti), **2)** modifica del valore di un attributo, **3)** associazioni (collegamenti) create o eliminate.
    - **Tempo passato:** Vanno scritte sempre al tempo passato (es. "Un'istanza _è stata creata_") per enfatizzare che sono osservazioni di stato dopo il termine dell'operazione, non ordini da eseguire nel codice.
    - **Errore comune:** Dimenticarsi di includere la formazione di nuove associazioni quando viene creata una nuova istanza (raramente gli oggetti nel modello di dominio sono isolati).
- **Trasformazione vs Interrogazione:** Un contratto ha senso solo se c'è un reale cambiamento di stato (trasformazione). Se un'operazione serve solo a interrogare il sistema (es. calcolare un totale senza salvare nulla), non ci sono postcondizioni. Non bisogna inserire postcondizioni relative all'interfaccia utente (es. scrivere "il totale è stato mostrato" è un errore).
- **Approccio Agile:** I contratti operativi non sono obbligatori. Vanno usati solo quando necessario per chiarire dettagli complessi o per facilitare la comprensione del team.

## 15

Eccoci al riassunto del nuovo documento, che ci fa fare un salto fondamentale dall'Analisi alla Progettazione (OOD) introducendo i **Diagrammi di Interazione UML** (UML Interaction Diagrams):

**1. Il Ruolo dei Diagrammi di Interazione**

- **A cosa servono:** Questi diagrammi illustrano come gli oggetti software interagiscono tra loro scambiandosi **messaggi** per portare a termine un determinato task.
- **Vista dinamica:** A differenza dei diagrammi delle classi che mostrano una vista statica del sistema, i diagrammi di interazione forniscono una **vista dinamica**, entrando nel dettaglio degli algoritmi per ogni metodo.

**2. L'importanza cruciale per il Design (OOD)**

- Il documento sottolinea un principio fondamentale: durante la progettazione orientata agli oggetti (OOD), bisogna dedicare tempo a creare diagrammi di interazione, non diagrammi delle classi.
- Saltare direttamente dall'Analisi (Modello di Dominio) alla stesura del diagramma delle classi o del codice è molto difficile. I diagrammi di interazione sono il ponte ideale che rende la successiva programmazione molto più semplice. I diagrammi delle classi possono poi essere generati facilmente a partire da questi.

**3. Diagrammi di Sequenza vs. Diagrammi di Comunicazione** Esistono due tipi di diagrammi di interazione che possiedono lo stesso contenuto informativo, ma lo mostrano in modi diversi:

- **Diagrammi di Sequenza (Sequence Diagrams):** Enfatizzano l'ordine temporale in cui i messaggi vengono scambiati. Sono i più ricchi di notazioni e sono generalmente i preferiti dai tool UML, ma hanno lo svantaggio di consumare molto spazio orizzontale quando si aggiungono nuovi oggetti.
- **Diagrammi di Comunicazione (Communication Diagrams):** Mostrano gli oggetti e i loro collegamenti in un formato a grafo. Sono ottimi per risparmiare spazio e offrono flessibilità nell'aggiungere oggetti in due dimensioni, ma rendono più difficile capire a colpo d'occhio l'esatta sequenza temporale dei messaggi.

**4. Notazioni Chiave: I Diagrammi di Sequenza**

- **Lifeline e Messaggi:** Gli oggetti sono rappresentati da scatole con una linea tratteggiata verticale ("lifeline"). L'interazione inizia spesso con un _found message_ (un messaggio il cui mittente non è specificato). I rettangoli verticali (Execution Specification Bars) indicano il focus di controllo, ovvero il tempo necessario per eseguire l'operazione.
- **Creazione e Distruzione:** Quando un nuovo oggetto viene creato (di solito con un messaggio `create`, che equivale alla chiamata di un costruttore), la sua scatola viene disegnata esattamente all'altezza in cui avviene la creazione. Se c'è bisogno di indicare la distruzione esplicita di un oggetto (es. in C++ o per connessioni chiuse), si usa lo stereotipo `«destroy»` con una grande 'X'.
- **I Frame (Cornici):** Per rappresentare algoritmi complessi, UML utilizza delle cornici (frames) con un operatore e una guardia booleana. I principali sono:
    - `alt`: Logica condizionale mutualmente esclusiva (il classico _if-else_).
    - `opt`: Frammento opzionale (si esegue solo se la condizione è vera).
    - `loop`: Per indicare cicli e iterazioni.
- **Riutilizzo (Interaction Occurrence):** Si può usare il frame `ref` per fare riferimento a un altro diagramma di sequenza (chiamato `sd`), permettendo di scomporre e semplificare diagrammi troppo grandi.

**5. Notazioni Chiave: I Diagrammi di Comunicazione**

- **I Link (Collegamenti):** Tutti i messaggi scorrono avanti e indietro su un singolo tratto, chiamato _link_ (che è un'istanza di un'associazione), che connette due oggetti.
- **Frecce e Numerazione:** Si utilizza una piccola freccia per indicare la direzione del messaggio. Per capire l'ordine di esecuzione, i messaggi sono numerati (es. 1, 2, 3).
- **Annidamento (Nesting):** Per indicare che un messaggio viene invocato all'interno di un altro, si usano i sotto-numeri (es. `1.1`, `1.2`, `2.1`). Anche qui si possono indicare iterazioni (usando un asterisco `*`) o condizioni (inserendo il test tra parentesi quadre) direttamente accanto al numero del messaggio.

## 16
Eccoci al riassunto di questo nuovo capitolo, che formalizza il passaggio alla progettazione vera e propria introducendo i **Diagrammi delle Classi UML (Design Class Diagrams - DCD)**:

**1. Dal Modello di Dominio al Modello di Design**

- Mentre il Modello di Dominio (fase di Analisi) offriva una prospettiva concettuale, il Modello di Design offre una **prospettiva software**.
- Le classi in questo diagramma rappresentano vere e proprie classi di programmazione (es. in Java o C++), dotate non solo di attributi, ma anche di metodi e visibilità (pubblica, privata, ecc.).

**2. Rappresentare gli Attributi**

- Gli attributi possono essere mostrati in due modi: come **testo** all'interno del riquadro della classe, o **visivamente** tramite una linea di associazione verso un'altra classe.
- Dal punto di vista semantico e del codice (es. `private Sale currentSale;`), le due notazioni sono equivalenti. Tuttavia, la notazione visiva con la linea e la freccia di navigabilità è spesso preferita per evidenziare i collegamenti tra le classi, specialmente quando si tratta di collezioni di oggetti (es. una lista).

**3. Operazioni vs. Metodi**

- **Operazione:** In UML è solo una _dichiarazione_ (firma) che include nome, parametri, tipo di ritorno e visibilità.
- **Metodo:** È l'_implementazione_ vera e propria dell'operazione nel codice.
- **Dettagli pratici:** I metodi per creare oggetti (messaggio `create`) diventano i costruttori. Inoltre, per evitare "rumore visivo", le operazioni di _getter_ e _setter_ (per leggere e scrivere attributi) vengono solitamente omesse dal diagramma.

**4. Le Relazioni tra le Classi (Design)** A livello di software, le relazioni diventano molto specifiche:

- **Dipendenza (Dependency):** Una relazione generale in cui un cambiamento nella classe "fornitore" influisce sulla classe "cliente" (rappresentata con una linea tratteggiata). Il caso più comune è la dipendenza `«uses»` (es. una classe usa un'altra classe istanziandola in un metodo locale o passandola come parametro).
- **Composizione (Composition):** Rappresentata da un **diamante pieno**, indica una relazione "tutto-parte" in cui **la parte non può esistere senza il tutto**. Se distruggi l'oggetto contenitore, viene distrutta anche la parte.
- **Aggregazione (Aggregation):** Rappresentata da un **diamante vuoto**, è una relazione "tutto-parte" più debole, in cui le parti possono esistere in modo indipendente.

**5. Polimorfismo e Interfacce**

- Il documento illustra l'importanza del polimorfismo con l'esempio di uno "Zoo": se vogliamo aggiungere un nuovo animale, non serve modificare la classe Zoo esistente. Basta creare una nuova classe (es. `Pig`) che implementa l'interfaccia `Animal`.
- **Interfacce:** Sono insiemi di operazioni astratte senza attributi. Permettono di slegare la richiesta di un servizio dalla sua implementazione specifica (binding dinamico), rendendo il sistema altamente flessibile e facilmente estendibile.

**6. Estensioni UML (Stereotipi, Vincoli e Tag)**

- **Stereotipi:** Modificano la semantica degli elementi (es. `«interface»` o `«actor»`).
- **Vincoli (Constraints):** Condizioni racchiuse tra parentesi graffe che devono essere rispettate (es. `{ size >= 0 }`).
- **Tag:** Coppie chiave/valore racchiuse tra parentesi graffe (es. `{author = Les}`), spesso usate per informazioni di project management.

**7. Connessione con i Diagrammi di Interazione**

- C'è una stretta sinergia: le classi e i messaggi identificati nei diagrammi di interazione (dinamici) diventano direttamente classi e operazioni nel diagramma delle classi (statico). Nella pratica lavorativa, questi due diagrammi vengono disegnati contemporaneamente.

## 17
Eccoci al cuore della progettazione software orientata agli oggetti (OOD)! Questo documento introduce i fondamentali pattern **GRASP** e il concetto di **Responsibility-Driven Design (RDD)**. Ecco il riassunto strutturato:

**1. Gli Input per il Design (Punto di partenza)**

- Prima di iniziare a progettare gli oggetti software, si usano gli artefatti prodotti durante l'analisi (Inception ed Elaboration).
- Questi includono: i Casi d'Uso (che definiscono il comportamento visibile), il Modello di Dominio (che ispira i nomi degli oggetti software), i System Sequence Diagrams (che identificano gli eventi di sistema) e i Contratti Operativi (che definiscono le post-condizioni da raggiungere).

**2. Responsibility-Driven Design (RDD)**

- **Cos'è:** È un approccio in cui la progettazione del software viene pensata in termini di **responsabilità, ruoli e collaborazioni** tra oggetti. Un progetto orientato agli oggetti è visto come una "comunità di oggetti che collaborano".
- Le responsabilità si dividono in due tipi principali:
    - **Knowing (Conoscere):** Un oggetto è responsabile di conoscere i propri dati privati (attributi), gli oggetti a cui è associato e le cose che può calcolare o derivare.
    - **Doing (Fare):** Un oggetto è responsabile di fare qualcosa da solo (es. creare un altro oggetto), chiedere ad altri oggetti di fare qualcosa, oppure controllare e coordinare le azioni di altri oggetti.

**3. Cosa sono i pattern GRASP?**

- L'acronimo sta per **General Responsibility Assignment Software Patterns**.
- Non sono regole calate dall'alto, ma la codifica di principi di base e best practice ampiamente usate per assegnare correttamente le responsabilità agli oggetti. Dare un nome a questi principi (es. "Usiamo un Controller") facilita immensamente la comunicazione nel team.
- Dei 9 pattern esistenti, il documento ne approfondisce 5 applicandoli al caso di studio del Monopoly.

**4. I 5 Principi GRASP Fondamentali**

- **1. Creator (Creatore):** Risponde alla domanda _"Chi deve creare un nuovo oggetto di classe A?"_. La soluzione è assegnare questa responsabilità a una classe B se: B aggrega o contiene A, B registra A, B usa strettamente A, oppure B ha i dati per inizializzare A. _(Es. Nel Monopoly, la classe `Board` crea gli oggetti `Square` perché li contiene)_.
- **2. Information Expert (Esperto delle Informazioni):** È il principio guida per eccellenza. Stabilisce che bisogna assegnare una responsabilità alla classe che **possiede le informazioni necessarie** per soddisfarla. _(Es. Se serve un metodo `getSquare(name)`, va messo in `Board` perché è lui ad avere le informazioni sulle caselle)_.
- **3. Low Coupling (Basso Accoppiamento):** L'accoppiamento misura quanto un elemento sia connesso o dipenda da altri. Mantenere un basso accoppiamento fa sì che gli oggetti non vengano impattati dalle modifiche esterne, siano più facili da capire e molto più riutilizzabili.
- **4. Controller (Controllore):** Risponde alla domanda _"Qual è il primo oggetto oltre il livello di interfaccia utente (UI) che riceve e coordina un'operazione di sistema?"_. Non bisogna mai mettere logica di business nell'interfaccia. Il Controller è solitamente un oggetto di facciata (Façade) che rappresenta l'intero sistema (es. `MonopolyGame`) o lo scenario di un caso d'uso specifico.
- **5. High Cohesion (Alta Coesione):** Misura quanto le operazioni di un elemento software siano focalizzate e funzionalmente correlate. Un oggetto "gigante" che fa troppe cose diverse ha una _bassa_ coesione. Progetti eccellenti hanno classi con alta coesione al loro interno e basso accoppiamento tra di esse. Spesso queste due metriche sono inversamente correlate (se alzi la coesione, tendi ad abbassare l'accoppiamento).

## 18
Ecco il riassunto strutturato di questo capitolo, che mostra l'applicazione pratica dei pattern GRASP per la **Realizzazione dei Casi d'Uso (Use Case Realization)** nel caso di studio del sistema POS:

**1. La Realizzazione dei Casi d'Uso**

- **Cos'è:** Descrive come un caso d'uso viene implementato all'interno del Modello di Design attraverso la collaborazione tra oggetti software.
- **Il processo:** Le operazioni di sistema (identificate precedentemente nei diagrammi di sequenza di sistema, come `makeNewSale` o `enterItem`) diventano i messaggi di partenza che innescano l'interazione all'interno del livello di dominio, entrando generalmente tramite un oggetto Controller.
- **Modello di Dominio come ispirazione:** I nomi delle classi software prendono spunto dal Modello di Dominio concettuale (per mantenere basso il "divario rappresentativo"), ma il Modello di Dominio non è un vincolo rigido: si possono creare nuove classi software se necessario.

**2. Progettazione delle Operazioni di Sistema (Scenario "Process Sale")** Il documento analizza passo dopo passo la progettazione delle operazioni per una vendita in contanti:

- **`makeNewSale()` (Avvio Vendita):**
    - _Controller:_ Viene scelto il `Register` (Registratore di cassa) come Controller di facciata.
    - _Creator:_ Chi crea l'oggetto `Sale` (Vendita)? Applicando il pattern Creator, la responsabilità va al `Register`, poiché nei requisiti il registratore "registra" le vendite. A sua volta, `Sale` creerà una lista vuota per contenere i futuri articoli.
- **`enterItem()` (Inserimento Articolo):**
    - _Creator:_ La responsabilità di creare la riga di vendita (`SalesLineItem`) viene assegnata a `Sale`, in quanto c'è una forte aggregazione tra la vendita e le sue righe.
    - _Expert:_ Per trovare la descrizione dell'articolo in base all'ID inserito, si usa il pattern Information Expert: il candidato ideale è il `ProductCatalog` (Catalogo Prodotti).
- **`endSale()` (Fine Vendita) e il Calcolo del Totale:**
    - _Expert in azione:_ Nessuna singola classe ha il totale pronto. Per calcolarlo, `Sale` (che conosce tutte le righe) somma i subtotali; `SalesLineItem` (che conosce quantità e descrizione) calcola il subtotale; `ProductDescription` fornisce il prezzo.
    - _Virtualization vs Materialization:_ Il totale è un "dato derivato". Può essere calcolato al volo ogni volta (_Virtualization_) oppure memorizzato in un attributo specifico di `Sale` che viene aggiornato (_Materialization_).
- **`makePayment()` (Pagamento):**
    - _Creator & Low Coupling:_ Per creare l'oggetto `Payment`, si sceglie `Sale` (invece di `Register`) per mantenere un basso accoppiamento (Low Coupling) e un'alta coesione.
    - _Expert:_ `Sale` è responsabile del calcolo del resto (bilancio), poiché conosce sia il totale della vendita sia il pagamento appena creato.

**3. Visibilità tra Oggetti** Affinché un oggetto possa inviare un messaggio a un altro (es. `Register` che interroga `ProductCatalog`), deve avere "visibilità" su di esso. Esistono quattro tipi di visibilità:

- **Attribute visibility:** L'oggetto A ha un attributo permanente che punta all'oggetto B (molto comune).
- **Parameter visibility:** L'oggetto B viene passato come parametro a un metodo di A (temporanea).
- **Local visibility:** L'oggetto B viene creato e usato localmente all'interno di un metodo di A.
- **Global visibility:** L'oggetto B è visibile globalmente a tutti (es. tramite pattern Singleton), ma aumenta molto l'accoppiamento.

**4. Connessione tra UI e Livello di Dominio**

- L'interfaccia utente (UI) non deve contenere logica di business. Inoltra semplicemente gli eventi di sistema (es. quando il cassiere preme un bottone) al Controller (il `Register`).
- A volte la UI ha bisogno di leggere dati (es. il totale). Può chiederli passando attraverso il `Register` (minore accoppiamento verso il dominio, ma interfaccia del Register più affollata) oppure farsi passare un riferimento all'oggetto `Sale` per interrogarlo direttamente (maggiore accoppiamento, ma più flessibilità).

**5. Il caso d'uso di StartUp (Inizializzazione)**

- È la fase in cui il sistema "prende vita".
- **Regola pratica:** La progettazione dello StartUp va fatta **per ultima** (quando si sa esattamente cosa deve essere inizializzato), ma il codice relativo va scritto **per primo** per poter avere un sistema eseguibile.
- Spesso si sceglie un "root object" (es. `Store`) che viene creato dal main dell'applicazione e che si occupa di istanziare a cascata tutti i componenti principali del dominio (es. crea il `ProductCatalog`, poi il `Register` e li collega tra loro).

## 19

Eccoci al passaggio cruciale: la trasformazione del Design in Codice reale e la conclusione della prima iterazione! Ecco il riassunto strutturato di questo capitolo:

**1. Dal Design al Codice (L'Approccio Agile)**

- **Implementazione:** Fino a questo punto, l'analisi e il design erano indipendenti dal linguaggio di programmazione. Ora è il momento di scegliere un linguaggio orientato agli oggetti specifico (es. Java, C++, C#).
- **Mentalità Agile:** I documenti di design non devono essere perfetti prima di iniziare a programmare. Scrivere il codice introdurrà inevitabilmente dei cambiamenti al design originale. L'importante è **sincronizzare i documenti e i diagrammi UML con il codice alla fine dell'iterazione**, così da mantenere i modelli utili e coerenti per l'iterazione successiva.

**2. Tradurre i Diagrammi in Codice**

- **Dai Diagrammi delle Classi (DCD):** Si genera il "codice scheletro". I DCD forniscono i nomi delle classi, gli attributi e le firme (signature) dei metodi. Le associazioni di tipo "1 a molti" (1..*) richiedono l'uso di classi speciali chiamate **Collection** (es. `List` o `ArrayList` in Java) per memorizzare le referenze agli oggetti.
- **Dai Diagrammi di Interazione:** Si ricava il corpo dei metodi. L'esatta sequenza dei messaggi in un diagramma di sequenza si traduce direttamente nella serie di istruzioni (statements) all'interno del metodo.
- **Ordine di implementazione:** Di solito si procede _bottom-up_, ovvero si inizia a programmare e testare le classi meno accoppiate (che non dipendono da nessun altro), per poi salire verso quelle più accoppiate.

**3. Il Refactoring e i "Code Smells"**

- **Cos'è il Refactoring:** È una tecnica strutturata per modificare la struttura interna del codice al fine di migliorarne la leggibilità, la manutenibilità e l'estensibilità, **senza alterarne il comportamento esterno**.
- **Code Smells (Puzze di codice):** Sono indizi o sintomi che indicano una probabile cattiva progettazione del codice. Includono: codice duplicato, metodi o classi gigantesche, mancanza di uso di interfacce e alto accoppiamento.
- **Le Tecniche di Refactoring:** Esistono "rimedi" specifici, catalogati in gran parte da Martin Fowler. Alcuni esempi sono:
    - _Extract Method:_ trasformare un metodo troppo lungo estraendone una parte in un metodo di supporto privato.
    - _Extract Class:_ creare una nuova classe spostandovi campi e metodi da una classe preesistente troppo ingombrante.
    - _Replace Conditional with Polymorphism:_ sostituire logiche condizionali complesse (if/switch) sfruttando il polimorfismo.

**4. Conclusione della Prima Iterazione (Fase di Elaboration)**

- **Non solo teoria:** La fase di Elaboration non riguarda solo la stesura di requisiti e design. Alla fine di ogni iterazione si deve produrre del software stabile, eseguibile e testato (codice "da produzione").
- **Feedback e Dimostrazione:** A fine iterazione, il codice viene mostrato agli stakeholder per validarlo, raccogliere idee e discutere cosa ha funzionato e cosa no. I requisiti (es. i Casi d'Uso e la _Vision_) si aggiornano ed evolvono proprio grazie a questi feedback.
- **La Regola del Timeboxing:** Ogni iterazione (che spesso dura 3 settimane) deve terminare alla data prestabilita. Se si è indietro col lavoro, **si riducono le funzionalità da consegnare (pratica del _de-scope_)**, ma non si ritarda la scadenza né si taglia sulla qualità o sui test.

Con questo capitolo hai completato l'intero ciclo che va dai Requisiti iniziali fino alla stesura e ottimizzazione del codice! Visto l'ampio vocabolario introdotto (Refactoring, Code Smells, Collection, De-scope), **ti andrebbe se creassi per te un set di Flashcard** per memorizzare queste definizioni, oppure un **Quiz di fine modulo** sull'intero processo?

## 20-21-22-23-24-25

Ecco il riassunto strutturato di questo corposo blocco di documenti. Entriamo nell'**Iterazione 2** e affrontiamo uno degli argomenti più importanti in assoluto per l'ingegneria del software: la seconda parte dei pattern GRASP e i **Design Pattern della "Gang of Four" (GoF)**.

**1. L'Iterazione 2 e gli ultimi Pattern GRASP** Nella seconda iterazione, i requisiti diventano più complessi. Nel POS vengono introdotti sistemi esterni (calcolatori di tasse, sistemi di accounting) e regole di prezzo complesse. Per gestire questa complessità, si usano gli ultimi 4 pattern GRASP:

- **Polymorphism (Polimorfismo):** Se il comportamento varia in base al tipo (es. diverse caselle del Monopoly o diversi calcolatori di tasse), assegna la responsabilità ai tipi stessi usando operazioni polimorfiche.
- **Pure Fabrication (Pura Invenzione):** Quando assegnare una responsabilità a una classe del dominio (Information Expert) causa un alto accoppiamento o bassa coesione, si "inventa" una classe artificiale che non esiste nel mondo reale (es. `PersistentStorage` per salvare nel database, o `Cup` per lanciare i dadi).
- **Indirection (Indirezione):** Assegna le responsabilità a un oggetto intermedio per disaccoppiare due elementi (es. gli Adapter).
- **Protected Variations (Variazioni Protette):** È il principio cardine. Identifica i punti di probabile variazione futura e crea un'interfaccia stabile per "proteggere" il resto del sistema da quei cambiamenti.

**2. Introduzione ai Design Pattern GoF** Pubblicati nel 1995 nel celebre libro della "Gang of Four", i pattern sono soluzioni documentate a problemi di design ricorrenti. Si dividono in tre categorie (Creazionali, Strutturali, Comportamentali) e si basano su due principi d'oro dell'OOP:

1. **Programmare verso interfacce**, sfruttando il _dynamic binding_ (collegamento dinamico a run-time).
2. **Favorire la Composizione di oggetti rispetto all'Ereditarietà:** L'ereditarietà (scatola bianca) lega rigidamente le classi. La composizione (scatola nera) permette di cambiare i comportamenti a run-time, mantenendo le classi piccole e incapsulate.

**3. Pattern Creazionali (Creational Patterns)** Astraggono il processo di istanziazione degli oggetti:

- **Abstract Factory:** Fornisce un'interfaccia per creare famiglie di oggetti correlati (es. i componenti fisici di un labirinto come Muri e Porte) senza specificare le loro classi concrete, permettendo di scambiare facilmente intere famiglie (es. Labirinto Standard vs Incantato).
- **Singleton:** Assicura che esista **una e una sola istanza** di una classe e fornisce un punto di accesso globale a essa. Ha un costruttore privato e un metodo statico `Instance()`.

**4. Pattern Strutturali (Structural Patterns)** Gestiscono la composizione di classi e oggetti:

- **Adapter:** Converte l'interfaccia di una classe in un'altra attesa dal client. Permette a classi incompatibili di collaborare.
- **Composite:** Raggruppa gli oggetti in strutture ad albero (gerarchie parte-tutto). Permette al client di trattare allo stesso modo (uniformemente) i singoli oggetti (foglie) e i gruppi di oggetti (compositi).
- **Façade:** Fornisce un'interfaccia singola e semplificata per un intero sottosistema complesso, riducendo l'accoppiamento.
- **Proxy:** Fornisce un surrogato o un "segnaposto" per controllare l'accesso a un altro oggetto (es. per caricare immagini pesanti solo quando servono, detto _virtual proxy_).
- **Decorator:** Aggiunge dinamicamente responsabilità a un oggetto. È un'alternativa flessibile all'ereditarietà (es. aggiungere cornici a una UI o ingredienti a un caffè senza creare centinaia di sottoclassi).

**5. Pattern Comportamentali (Behavioral Patterns)** Gestiscono algoritmi e responsabilità:

- **Strategy:** Definisce una famiglia di algoritmi, li incapsula e li rende intercambiabili. Il client usa l'algoritmo senza conoscerne i dettagli (es. diverse strategie di impaginazione testo).
- **Observer:** Definisce una dipendenza uno-a-molti. Quando l'oggetto principale (_Subject_) cambia stato, tutti gli oggetti dipendenti (_Observer_) vengono notificati e aggiornati automaticamente (tipico per separare i Dati dalla UI grafica).
- **State:** Permette a un oggetto di cambiare comportamento in base al suo stato interno, delegando le chiamate a un oggetto "State" specifico (es. una connessione TCP o un lettore musicale).

**6. Applicazione Pratica: Il Sistema POS (Capitolo 26)** Questi pattern brillano quando combinati nel nostro caso di studio:

- **Sistemi Esterni:** Per gestire calcolatori di tasse di terze parti, si usa l'**Adapter**. L'Adapter non viene creato a caso, ma da una _Factory_ che legge da configurazioni esterne quale classe instanziare (Data-driven design). Questa Factory viene resa un **Singleton** (`ServicesFactory.getInstance()`).
- **Regole di Sconto Complesse:** Ogni regola di prezzo viene incapsulata usando il pattern **Strategy** (`ISalePricingStrategy`). Anche qui le strategie sono istanziate da un'apposita Factory. Per gestire conflitti (es. cliente anziano _E_ spesa > 400$), si usa il pattern **Composite** (`CompositeBestForCustomer`), in cui una strategia macro contiene una lista di strategie micro e calcola, ad esempio, lo sconto migliore per il cliente.

Questa è una quantità incredibile di materiale tecnico ad altissimo valore, fondamentale per l'esame. I Design Pattern sono concetti basati sulla memoria e sulla comprensione delle differenze sottili (es. Decorator vs Proxy, Ereditarietà vs Composizione).
