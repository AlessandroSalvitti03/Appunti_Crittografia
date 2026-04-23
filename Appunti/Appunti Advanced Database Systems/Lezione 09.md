**1. Architettura Client-Server**

- **Il Paradigma Client-Server:** È l'architettura distribuita più semplice e diffusa. Si basa su una netta separazione delle funzioni: il **Client** richiede i servizi (occupandosi della presentazione delle informazioni), mentre il **Server** li fornisce (gestendo i dati).
- **Ruolo di SQL:** Il linguaggio SQL è lo strumento ideale per separare questi due ambienti. Il client formula la query ed elabora i risultati, la rete trasferisce i comandi, e il server esegue materialmente l'interrogazione.
- Oltre all'architettura classica, esiste l'**Architettura ad Application Server**, che inserisce un livello intermedio per la gestione delle applicazioni tra il client e il database server.

**2. Database Distribuiti: Concetti e Tipologie**

- **Definizione:** In un database distribuito, i dati e il controllo sono suddivisi su server (nodi) differenti ma coordinati tra loro. Le operazioni vengono il più possibile delegate ai vari nodi.
- **Motivazioni:** Questa soluzione nasce per assecondare la natura intrinsecamente distribuita di molte organizzazioni, sfruttando l'evoluzione dei computer (maggiore potenza, minori costi), le nuove tecnologie DBMS e gli standard di interoperabilità.
- **Tipologie:**
    - _Rete:_ Possono operare su reti locali (**LAN**) o geografiche (**WAN**).
    - _DBMS:_ Possono essere **Omogenei** (quando tutti i nodi usano lo stesso software DBMS) o **Eterogenei** (sistemi misti, tipici dei sistemi informativi inter-divisionali o bancari).

**3. Le 4 Tematiche Chiave** La gestione dei database distribuiti deve affrontare quattro problematiche principali:

1. **Indipendenza e Cooperazione:** C'è la necessità di spostare le competenze e il controllo direttamente dove i dati vengono gestiti localmente (indipendenza), pur mantenendo la possibilità per alcune applicazioni di accedere a database multipli simultaneamente (cooperazione).
2. **Livelli di Trasparenza** (dettagliati nel punto 5).
3. **Efficienza** (dettagliata nel punto 6).
4. **Affidabilità delle transazioni distribuite**.

**4. Frammentazione dei Dati** Per distribuire un database, le tabelle (relazioni) vengono scomposte in frammenti. Ogni frammentazione deve garantire due proprietà assolute: la **Completezza** (tutti i dati originali devono essere presenti nei frammenti) e la **Ricostruibilità** (deve essere possibile ricreare la tabella originale).

- **Frammentazione Orizzontale:** Divide la tabella in insiemi di tuple (righe) utilizzando operazioni di _selezione_. La tabella originale si ricostruisce unendo i frammenti tramite un'operazione di **Unione (Union)**.
- **Frammentazione Verticale:** Divide la tabella in insiemi di attributi (colonne) utilizzando operazioni di _proiezione_. In questo caso, è obbligatorio includere la chiave primaria della tabella in ogni frammento creato. La ricostruzione avviene tramite un'operazione di **Join** basata sulla chiave.

**5. Allocazione e Livelli di Trasparenza**

- **Allocazione:** Ogni frammento logico corrisponde a un file fisico memorizzato su un server. L'allocazione può essere _non ridondante_ (un frammento su un solo server) oppure _ridondante_ (un frammento replicato su più server).
- **Trasparenza:** I DBMS commerciali offrono tre livelli di trasparenza per formulare le query, dal più astratto al più specifico:
    - **Trasparenza di Frammentazione:** L'utente scrive la query come se il database fosse un'unica entità centralizzata, ignorando del tutto l'esistenza dei frammenti.
    - **Trasparenza di Allocazione:** L'utente conosce il nome dei frammenti (es. `ACCOUNT1`) e li richiama, ma non ha bisogno di sapere su quale server fisico o filiale essi si trovino.
    - **Trasparenza di Linguaggio:** L'utente deve specificare esplicitamente nella sintassi SQL il nodo di rete in cui si trova il dato (es. `ACCOUNT1@1` o `ACCOUNT2@C`).

**6. Efficienza ed Esecuzione** Per garantire l'efficienza, l'esecuzione delle query può avvenire in modo **seriale** (i frammenti vengono interrogati uno dopo l'altro) o **parallelo** (le richieste vengono inviate contemporaneamente a più nodi, risparmiando sul tempo di risposta globale). L'ottimizzazione si ottiene sia sfruttando questo parallelismo, sia utilizzando la conoscenza delle proprietà logiche dei frammenti per indirizzare le query solo ai server strettamente necessari.

**7. Classificazione delle Transazioni** Le operazioni in un database distribuito si dividono in base alla loro complessità:

- **Remote requests:** Transazioni di sola lettura (query SQL) indirizzate a un singolo DBMS remoto.
- **Remote transactions:** Comandi SQL di qualsiasi tipo (select, insert, update, delete) indirizzati a un singolo DBMS remoto.
- **Distributed transactions:** Comandi SQL indirizzati a più DBMS remoti. Tuttavia, ogni _singolo comando SQL_ può agire su un solo DBMS per volta. Questa tipologia richiede l'implementazione del protocollo _two-phase commit_.
- **Distributed requests:** Transazioni arbitrarie estremamente complesse, in cui un singolo comando SQL può riferirsi contemporaneamente a molteplici DBMS. Richiede un ottimizzatore distribuito.