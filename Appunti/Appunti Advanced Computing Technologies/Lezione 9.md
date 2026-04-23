### 1. Introduzione: Eventi Inaspettati

L'unità di controllo di un processore è la parte più difficile da far funzionare correttamente e da rendere veloce. Una delle sfide ingegneristiche maggiori è rappresentata dalla gestione delle **eccezioni** e degli **interrupt**, ovvero eventi particolari che alterano il normale flusso sequenziale di esecuzione delle istruzioni di un programma.

Questi eventi agiscono in modo simile a delle chiamate a subroutine non pianificate e si dividono tipicamente in:

- **Eccezioni (interne):** Eventi inaspettati che si verificano all'interno del processore, come un'istruzione non definita, un _page fault_ della memoria virtuale, o un'anomalia aritmetica come l'overflow o l'underflow generato da calcoli che superano i limiti predefiniti di precisione,.
- **Interrupt (esterni):** Lo stesso meccanismo di base delle eccezioni è stato storicamente esteso per gestire la comunicazione asincrona tra il processore e i dispositivi di I/O esterni.

### 2. Il Meccanismo: Collaborazione tra Hardware e Sistema Operativo

Affinché le eccezioni si comportino nel modo previsto senza far crollare il sistema, l'hardware e il Sistema Operativo (SO) devono lavorare in stretta sinergia.

Quando si verifica un'eccezione, **l'hardware** deve compiere queste azioni fondamentali:

1. Bloccare l'istruzione responsabile dell'eccezione prima che venga terminata e alteri lo stato del sistema.
2. Lasciar completare regolarmente tutte le istruzioni antecedenti.
3. Eliminare (effettuare un _flush_) tutte le istruzioni successive già caricate nella pipeline.
4. Salvare l'indirizzo dell'istruzione che ha causato il problema in un registro dedicato, che nell'architettura MIPS prende il nome di **EPC (Exception Program Counter)**,.
5. Scrivere la causa dell'eccezione in un apposito **Registro Causa**,.
6. Trasferire il controllo (saltare) a un indirizzo di memoria preassegnato, dove risiede il codice del Sistema Operativo.

Una volta preso il controllo, **il Sistema Operativo** esamina la causa ed agisce in modo appropriato. Se si tratta di un errore hardware o di un'istruzione non valida, il SO tipicamente termina il programma. Se si tratta di una richiesta di I/O o di una chiamata al sistema (tramite l'istruzione speciale `syscall`), il SO passa in modalità _kernel_, svolge il compito richiesto e poi utilizza un'istruzione speciale di ritorno (come la `ERET` - Exception RETurn nel MIPS) per ripristinare la modalità _utente_ e riprendere l'esecuzione dall'indirizzo salvato nell'EPC,.

### 3. La Sfida delle Eccezioni nella Pipeline

Nelle architetture dotate di pipeline, la gestione delle eccezioni diventa particolarmente complessa perché **più istruzioni si trovano in esecuzione contemporaneamente in stadi diversi**.

**Approfondimento / Esempio pratico:** La difficoltà per i progettisti deriva dal fatto che bisogna avere conoscenza esatta dello stadio in cui ogni specifica eccezione può verificarsi. Ad esempio:

- Un'eccezione per "istruzione non definita" viene riconosciuta nello stadio di decodifica (**ID**).
- Un'eccezione per un overflow aritmetico o una chiamata al sistema operativo (`syscall`) avviene nello stadio di esecuzione (**EX**).

Poiché istruzioni diverse possono sollevare eccezioni in stadi diversi nello stesso momento, l'hardware deve gestire le "eccezioni pendenti" nel registro Causa, stabilendo delle priorità in modo tale da poter servire anche le altre eccezioni una volta risolta la prima.

### 4. Eccezioni Precise vs Eccezioni Imprecise

A causa della sovrapposizione delle istruzioni nella pipeline (o dell'esecuzione fuori ordine nei processori superscalari), c'è il rischio che, al momento del riconoscimento dell'eccezione, il Program Counter (PC) sia già andato molto avanti.

- **Eccezioni Imprecise:** In un calcolatore con eccezioni imprecise, la pipeline viene semplicemente fermata e il valore corrente del PC viene salvato. L'EPC potrebbe però contenere l'indirizzo di un'istruzione successiva a quella che ha realmente causato l'eccezione. Viene lasciato al Sistema Operativo il difficile (e lento) compito di analizzare a ritroso lo stato della macchina per capire quale istruzione abbia effettivamente provocato il danno.
- **Eccezioni Precise:** L'architettura MIPS e la stragrande maggioranza dei calcolatori moderni supportano eccezioni e interrupt precisi. Questo significa che l'hardware fa tutto il lavoro sporco per garantire che l'EPC contenga esattamente l'indirizzo dell'istruzione colpevole e che lo stato del sistema rifletta rigorosamente l'esecuzione in ordine fino a quel punto esatto.

**Perché le eccezioni precise sono fondamentali?** Sebbene implementare eccezioni precise in processori complessi sia molto costoso in termini di hardware, è una scelta obbligata. Come verrà approfondito studiando la gerarchia delle memorie, le eccezioni precise sono un requisito fondamentale per poter supportare la **memoria virtuale** (in particolare per riprendere in modo trasparente l'esecuzione dopo un _page fault_).