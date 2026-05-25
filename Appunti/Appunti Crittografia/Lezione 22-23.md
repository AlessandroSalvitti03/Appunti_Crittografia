
Fino ad ora abbiamo costruito un motore potentissimo (l'AES), ma c'è un problema strutturale enorme: questo motore è "schizzinoso". Mangia **solo ed esclusivamente blocchi esatti da 128 bit**. Come facciamo a mandare a un amico un video da 2 Giga?

Il professore ci spiega esattamente come costruire l'auto intera attorno al motore. Smontiamo queste due pagine introduttive!

### 1. Il Limite dell'AES (Non è un vero PriKc... per ora)

**Testo Originale:**

> **5.5 Modes of operation (a.k.a. turning AES in a PriKc)**
> 
> The AES as specified in the previous section is a block cipher but not a PriKc, since it operates on $\{0,1\}^{128}$ and not on $\{0,1\}^\infty$ as requested by Definition 5.1.

**Cosa dice:**

Torniamo alla Definizione 5.1 (la primissima di questo capitolo). Un vero "Private-Key Cryptosystem" (PriKc) deve essere in grado di cifrare messaggi lunghi a piacere ($M = \{0,1\}^\infty$). L'AES nudo e crudo non lo sa fare. Se gli dai 127 bit, va in crash. Se gliene dai 129, va in crash.

### 2. La Ricetta in 3 Ingredienti

Per trasformare un semplice "Block Cipher" in un vero sistema crittografico universale, il professore disegna uno schema con tre componenti obbligatori:

1. **Block cipher:** Il motore vero e proprio (AES, che cifra i singoli blocchi).
    
2. **Padding scheme:** Lo "stucco". Serve a riempire un messaggio (o meglio, il suo ultimo frammento) che non è grande abbastanza da formare un blocco intero.
    
3. **Modes of operation:** L'architetto del traffico. È l'algoritmo che decide come "concatenare" e far scorrere i vari blocchi di dati dentro il motore per cifrare l'intero file senza compromettere la sicurezza.
    

L'unione di questi tre elementi crea finalmente il nostro **PriKc**.

### 3. Affettare e Riempire (Il Padding)

**Testo Originale:**

> **Padding:** let $m \in \{0,1\}^\infty$ be a message to be encrypted with AES. We write
> 
> $m = m_1 \parallel m_2 \parallel \dots \parallel m_t$
> 
> where $m_i \in \{0,1\}^{128} \ \forall 1 \le i \le t-1$ and $\#m_t \le 128$.
> 
> A padding scheme is a way to extend the length of $m_t$ to 128 bits in order to be able to be encrypted with AES.

**Cosa dice:**

Prendi il tuo file gigante $m$ e taglialo con l'accetta in blocchi separati.

Tutti i blocchi dal primo ($m_1$) al penultimo ($m_{t-1}$) saranno fette perfette da 128 bit.

Ma la probabilità che il tuo file intero sia un multiplo esatto di 128 bit è quasi nulla. Quindi, l'ultimo frammento ($m_t$) sarà sicuramente più corto (es. 40 bit).

Il **Padding** prende questo frammento monco e gli incolla in coda dei bit "finti" (secondo regole molto precise) per gonfiarlo fino a raggiungere la dimensione fatidica di 128 bit.

_(Nota a margine del prof: abbiamo già visto logiche di padding o "riempimento" quando abbiamo studiato la costruzione Merkle-Damgård per gli Hash e lo schema RSA-OAEP!)_

### 4. Il focus del capitolo: Le Modalità Operative

**Testo Originale (Pagina 2):**

> We don't discuss padding schemes in this course and we will only focus on (some) modes of operation of AES (many things, however, will be valid in general for any block cipher).
> 
> [...] For simplicity, we assume that the last block of $m$ is already padded (i.e. $\#m_t = 128$).

**Cosa dice:**

Il professore fa una mossa tattica. Il padding è importante, ma a livello crittografico non è la cosa più interessante da studiare (spesso è solo una sequenza di byte banali). Quindi, da questo momento in poi, **facciamo finta che il messaggio sia già stato paddato perfettamente**. Avremo sempre un numero esatto di blocchi da 128 bit.

Il vero problema ora è un altro: se taglio un file in 100 blocchi e li passo tutti dentro l'AES uno per uno in modo indipendente... è sicuro? (Spoiler: assolutamente NO).

La risposta a questa domanda si chiama **Modes of Operation** (Modalità Operative). Esistono vari modi per far scorrere questi blocchi (ECB, CBC, CTR, ecc.). Scommetto che le prossime slide inizieranno presentandoci il modo più banale (e più insicuro) di tutti per cifrare a blocchi.

Appena sei pronto, invia i prossimi appunti e iniziamo l'assemblaggio!

---
Avevamo lasciato il discorso in sospeso: come facciamo a cifrare un file intero usando l'AES?

Il professore ci presenta la prima, la più banale e (purtroppo) la più disastrosa delle Modalità Operative: la **ECB (Electronic Codebook Mode)**. È un esempio didattico perfetto per capire cosa succede quando la teoria matematica si scontra con il mondo reale.

Smontiamo questo disastro crittografico e la sua dimostrazione matematica!

### 5. La Catena di Montaggio Ingenua (ECB Mode)

**Testo Originale:**

> Let $m \in \{0,1\}^\infty$ be s.t. $m = m_1 \parallel m_2 \parallel \dots \parallel m_t$ with $\#m_i = 128 \ \forall 1 \le i \le t$.
> 
> ECB is the simplest (and most obvious, and also weakest) mode of operation of AES and works as follows:
> 
> $c_i := AES_K(m_i) \quad \forall 1 \le i \le t$

**Cosa dice:**

Hai un file da cifrare? Taglialo in blocchi esatti da 128 bit ($m_1, m_2, \dots$).

Prendi la tua chiave segreta $K$ e accendi un'istanza del motore AES separata per ogni singolo blocco. L'output 1 sarà il blocco cifrato 1 ($c_1$), l'output 2 sarà il blocco cifrato 2 ($c_2$), e così via. Alla fine, incolli i blocchi cifrati tutti insieme.

**I "Falsi" Vantaggi (Proprietà 1, 2 e 3):**

Da un punto di vista dell'ingegneria del software, questa modalità fa gola a tutti per l'efficienza:

1. **Parallelizzabile (Cifratura & Decifratura):** Siccome il blocco 2 non dipende minimamente dal blocco 1, puoi usare un processore multi-core per cifrare/decifrare mille blocchi contemporaneamente. È velocissimo.
    
2. **Random Access:** Se vuoi leggere solo la fine di un video cifrato di 2 Giga, non devi decifrare tutti i 2 Giga precedenti. Vai direttamente al blocco che ti interessa e lo decifri.
    

Sembra bellissimo, vero? Sbagliato.

### 6. Il Difetto Fatale: Il Pinguino di Linus (Proprietà 4)

**Testo Originale:**

> 4. $m_i = m_j \implies c_i = c_j$
>     

**Cosa dice e perché è un disastro:**

Questo è l'errore crittografico più grave in assoluto. Poiché il motore AES è deterministico e tu stai usando _la stessa chiave K_ per tutti i blocchi, **due blocchi di testo in chiaro identici produrranno sempre due blocchi cifrati identici**.

Il professore incolla la famosissima immagine del pinguino Tux (mascotte di Linux).

Immagina che il file del pinguino venga tagliato a blocchi. Lo sfondo dell'immagine è composto da centinaia di blocchi identici di pixel bianchi. Il corpo è composto da blocchi di pixel neri.

Se cifri l'immagine in ECB, tutti i blocchi bianchi diventeranno un blocco cifrato "A", e tutti i blocchi neri diventeranno un blocco cifrato "B".

Il risultato? Il colore cambia diventando rumore, ma **l'impronta strutturale dei dati rimane perfettamente visibile a occhio nudo**. Un hacker che intercetta i dati sa perfettamente cosa c'è nell'immagine senza dover decifrare nemmeno un bit!

### 7. La Malleabilità (L'Attacco CCA - Theorem 5.16)

Oltre a svelare i pattern visivi, la modalità ECB ha un problema gravissimo di integrità: **i blocchi non sono incollati tra loro**. L'hacker può smontarli e riassemblarli come i mattoncini Lego.

Il professore dimostra matematicamente che **AES-ECB non è CCA-secure** (Chosen-Ciphertext Attack). Ti ricordi il gioco CCA? L'hacker ha accesso a un Oracolo di Decifratura ($\mathcal{O}^{dec}_K$), ma c'è una regola ferrea: non può chiedergli di decifrare esattamente la sfida $c$ (sarebbe un cheat!).

**La dimostrazione dell'Hacker (Proof):**

1. L'hacker riceve la sfida $c$, composta da tanti blocchi cifrati. L'obiettivo è farsi decifrare $c$ dall'Oracolo senza infrangere le regole del gioco.
    
2. L'hacker si inventa un blocco da 128 bit di rumore a caso: $r \in \{0,1\}^{128}$.
    
3. L'hacker prende la sfida $c$ e gli "appiccica" in fondo il blocco inventato: __$c^* := c \parallel r$_.
    
4. L'hacker invia questo nuovo file "frankenstein" $c^*$ all'Oracolo di Decifratura.
    
    - _La scappatoia:_ L'Oracolo controlla le regole. "$c^*$ è uguale a $c$?". No, perché $c^*$ è più lungo di un blocco! Quindi la query è valida e viene accettata.
        
5. L'Oracolo decifra il file blocco per blocco. Decifra fedelmente tutti i blocchi originali (restituendo il messaggio segreto $m$) e infine decifra il blocco finto $r$ (restituendo una spazzatura $s$). L'Oracolo restituisce tutto all'hacker: $m^* = m \parallel s$.
    
6. L'hacker prende la risposta, butta via l'ultimo blocco di spazzatura ($s$) e **si ritrova in mano il messaggio segreto $m$ in chiaro**. Vittoria col 100% di probabilità!
    

L'ECB fallisce miseramente perché manca di "autenticazione" e "legame" tra i blocchi. Per farti capire la gravità del problema n° 4 (quello del pinguino), ti ho preparato un simulatore. Disegna qualcosa sulla griglia e guarda come il motore ECB, essendo puramente deterministico, tradisca immediatamente la struttura del tuo disegno.

La modalità ECB oggi è rigorosamente vietata in qualsiasi applicazione reale seria. Sicuramente le prossime slide introdurranno il salvatore della patria: la modalità CBC (Cipher Block Chaining), che risolve esattamente questo difetto incatenando i blocchi tra loro usando l'IV. Manda pure!

Agli ordini! Queste tre pagine sono il colpo di grazia finale per la modalità ECB. Dopo aver visto come distrugge le immagini (il povero pinguino), il professore ci dimostra matematicamente come l'ECB si sbricioli davanti ad attacchi mirati.

Assistiamo al massacro definitivo: la caduta del gioco IND-CPA e il leggendario **ECB Oracle Attack** (uno degli attacchi più famosi e divertenti della crittografia pratica).

Smontiamo tutto!

### 8. La Morte del Gioco IND-CPA (Theorem 5.17)

**Testo Originale:**

> **Theorem 5.17**
> 
> AES-ECB mode is not IND-CPA secure. [...] if $\mathcal{O}^{enc}_k$ is available the answer is NO because encryption is not randomized.

**Cosa dice:**

Ti ricordi il gioco dell'Indistinguibilità (IND-CPA)? L'hacker invia due messaggi ($m_0, m_1$), lo sfidante lancia la moneta, ne cifra uno e restituisce la sfida $c$. L'hacker deve indovinare quale dei due è stato cifrato.

Il professore ribadisce una regola d'oro: **qualsiasi cifrario deterministico fallisce questo gioco**.

**La Dimostrazione (Proof):**

L'hacker usa una furbizia strutturale:

1. Sceglie $m_0$ in modo che sia composto da **due blocchi identici** (es. tutti zeri: $0\dots0 \parallel 0\dots0$).
    
2. Sceglie $m_1$ in modo che sia composto da **due blocchi diversi** (es. un blocco di zeri e uno di uni: $0\dots0 \parallel 11\dots1$).
    
3. Lo sfidante cifra uno dei due e restituisce il file cifrato $c$.
    
4. L'hacker guarda semplicemente la sfida $c$. Siccome ECB cifra ogni blocco separatamente in modo deterministico:
    
    - Se la sfida $c$ è composta da due blocchi cifrati identici ($c_1 = c_2$), allora il messaggio originale era per forza $m_0$.
        
    - Se i blocchi sono diversi, il messaggio era $m_1$.
        
        L'hacker vince col 100% di probabilità senza fare nemmeno un calcolo. L'ECB è matematicamente bucato.
        

### 9. Il Capolavoro: L'Attacco all'Oracolo ECB

Nelle pagine successive, il professore introduce un attacco diabolico basato sul CPA (Chosen Plaintext Attack).

**Il Setup (Il Bersaglio):**

Immagina un server sicuro (l'Oracolo $\mathcal{O}_{ECB}$) che possiede una password segreta **$s$**.

Il server ti permette di inviare del testo a tuo piacimento ($p$, il "payload" o "padding"), e lui lo concatena alla password segreta prima di cifrare il tutto con l'AES-ECB.

In pratica, il server calcola e ti restituisce: $AES_K(p \parallel s)$.

_La domanda del prof:_ "Questa cosa dovrebbe svelare informazioni sul segreto $s$? No, non dovrebbe... ma lo fa."

**Fase 1: L'Allineamento (Il trucco dei 15 byte)**

L'AES lavora a blocchi di 16 byte. L'hacker fa una mossa astuta: invia al server un payload $p$ lungo esattamente **15 byte** (es. `AAAAAAAAAAAAAAA`).

Cosa succede dentro il server? Il server prende i tuoi 15 byte e ci attacca dietro il segreto $s$.

Il primo blocco da 16 byte che finisce nel motore AES sarà composto da:

**i tuoi 15 byte `A` + il 1° byte del segreto ($s_0$)**.

L'Oracolo ti restituisce il blocco cifrato, chiamiamolo **$C_{target}$**. Tu ora hai l'impronta digitale esatta di quel blocco.

**Fase 2: Il Dizionario (Il Brute Force Mirato)**

Ora l'hacker usa l'Oracolo per cifrare da solo tutte le possibilità.

Invia al server 256 richieste diverse. In ogni richiesta, il payload è:

I 15 byte `A` + un byte a caso che l'hacker sceglie (chiamiamolo $x$).

Il server cifra `AAAAAAAAAAAAAAAx`.

L'hacker guarda l'output. Quando l'output di una di queste prove è **perfettamente identico a $C_{target}$**, l'hacker sa con certezza assoluta che $x = s_0$.

**BOOM. Il primo byte della password segreta è stato craccato.**

**Fase 3: Lo Scorrimento (Iterazione)**

Per trovare il secondo byte ($s_1$), l'hacker ripete il trucco, ma accorcia il payload a **14 byte** (`AAAAAAAAAAAAAA`).

Il server ci attacca dietro il segreto. Il primo blocco sarà:

14 byte `A` + $s_0$ + $s_1$.

Siccome $s_0$ l'abbiamo appena scoperto, l'unico pezzo ignoto è $s_1$. L'hacker ripete il ciclo del dizionario e scopre anche il secondo byte. E così via fino alla fine!

### 10. La Matematica del Massacro (L'Ultima Pagina)

Nell'ultima immaginetta, il professore tira le somme di questa devastazione.

Quanto costa craccare una chiave AES da 128 bit provando tutte le chiavi (Brute Force)?

Costa **$2^{128}$** tentativi. (Miliardi di anni).

Quanto costa rubare una password segreta da 16 byte usando l'ECB Oracle Attack?

Devi indovinare 16 byte. Per ogni byte, devi fare massimo 256 tentativi ($2^8$).

Costo totale: $16 \cdot 2^8 = 4096$ tentativi, ovvero **$2^{12}$**.

Un computer ci mette una frazione di millisecondo.

Questo è il motivo per cui l'ECB non si usa **MAI** per crittografare dati dove l'attaccante ha anche il minimo controllo sull'input!

Per farti capire quanto è micidiale e affascinante questo meccanismo di "allineamento e scorrimento", ti ho costruito un simulatore che esegue l'attacco in tempo reale. Fai finta di essere l'Oracolo, nascondi una password, e guarda come l'hacker la estrae un carattere alla volta!

Se il professore ha finito con l'ECB, scommetto che la prossima lezione sarà dedicata alle modalità sicure come CBC o CTR, che usano l'IV (Initialization Vector) per rompere questo maledetto determinismo. Quando vuoi, mandale pure!

---

Come avevamo previsto, il professore abbandona il disastroso ECB e ci presenta il vero standard industriale.

Benvenuti nella modalità **CBC (Cipher Block Chaining)**. Il nome dice tutto: "Incatenamento dei blocchi". È la geniale soluzione crittografica per uccidere il determinismo dell'ECB e salvare il povero pinguino di Linux.

Smontiamo questa architettura pezzo per pezzo, partendo dal diagramma della prima pagina!

### 11. L'Invenzione della Catena (Il Diagramma)

**Il Problema:** In ECB, $m_2$ veniva cifrato da solo. Se $m_1 = m_2$, allora $c_1 = c_2$.

**La Soluzione (CBC):** Prima di infilare un blocco di testo dentro il frullatore AES, **lo mescoliamo (XOR) con il blocco cifrato precedente**.

Guarda le formule scritte dal prof:

- $c_1 := AES_k(m_1 \oplus IV)$
    
- $c_i := AES_k(m_i \oplus c_{i-1}) \quad \forall i > 1$
    

Il blocco cifrato $c_1$ non viene solo spedito al destinatario, ma fa un "giro della morte" (la freccia che torna indietro nel disegno) e va a fare uno XOR con il messaggio successivo $m_2$.

Il risultato? Anche se scrivi mille volte la parola "CIAO" ($m_1 = m_2 = m_3$), il motore AES vedrà in ingresso stringhe sempre diverse, perché sono state "sporcate" dal crittogramma precedente. Il pinguino si dissolve in puro rumore bianco!

### 12. La Scintilla Iniziale: L'IV (Initialization Vector)

C'è un problema logico: per cifrare $m_1$, ci serve il blocco cifrato precedente ($c_0$), ma $m_1$ è il primo blocco! Non c'è un blocco precedente.

Qui entra in gioco l'**IV (Initialization Vector)**.

È un blocco da 128 bit di puro rumore casuale generato al momento. Funziona come "scintilla d'avviamento" per la catena.

Viene spedito in chiaro come primo pezzo del messaggio finale: $c_0 := IV$.

L'output completo sarà quindi l'IV seguito da tutti i blocchi cifrati: $IV \parallel c_1 \parallel c_2 \dots \parallel c_t$.

### 13. I Pro e Contro (Le Proprietà Analizzate)

A cavallo tra le due pagine, il prof analizza pregi e difetti ingegneristici di questa modalità. Fai molta attenzione alle cose sbarrate in rosso!

- **1) Encryption is parallelizable $\implies$ FALSO (Sbarrato).**
    
    Non puoi cifrare il blocco 2 finché non hai finito di cifrare il blocco 1 (perché ti serve $c_1$). Questo è il grande difetto del CBC: la cifratura è un collo di bottiglia sequenziale. Il tuo processore a 8 core dovrà aspettare e lavorare su un core solo.
    
- **2) Decryption is parallelizable $\implies$ VERO!**
    
    Questo è un paradosso bellissimo e il prof lo lascia non sbarrato. Come decifri $m_2$? La formula inversa è: $m_2 = AES^{-1}_k(c_2) \oplus c_1$.
    
    Tu, che ricevi il file, **hai già in mano tutto il file cifrato!** Hai sia $c_2$ che $c_1$. Quindi puoi buttare $c_2$ in un core del processore, $c_3$ in un altro, $c_4$ in un altro ancora, e decifrarli tutti in parallelo alla velocità della luce.
    
- **3) Random Access efficiente $\implies$ VERO!**
    
    Vuoi decifrare solo il blocco 50 di un film? Ti basta scaricare $c_{50}$ e $c_{49}$. Inverti $c_{50}$, fai XOR con $c_{49}$ e hai $m_{50}$. Non devi decifrare i primi 48 blocchi!
    
- **4) $m_i = m_j \implies c_i = c_j \implies$ FALSO (Sbarrato).**
    
    Il difetto del pinguino è ufficialmente morto grazie alla catena.
    
- **5) Error Propagation (Il danno da 1 bit).**
    
    Se un hacker (o un disturbo sulla rete) capovolge **1 singolo bit** nel blocco cifrato $c_1$, cosa succede?
    
    - Il blocco $m_1$ è distrutto per sempre (effetto valanga dell'AES).
        
    - Nel blocco $m_2$, si capovolgerà esattamente un bit nello stesso punto (a causa dello XOR finale: $AES^{-1}(c_2) \oplus c_1$).
        
    - Dal blocco $m_3$ in poi, tutto torna intatto. L'errore si "sana" da solo dopo due blocchi!
        

### 14. La Pagella Definitiva (La Tabella di Sicurezza)

Nell'ultima immagine, il professore confronta ECB e CBC nel tabellone dei giochi di sicurezza.

Guarda la colonna **IND-CPA**:

- AES-ECB: **X**
    
- AES-CBC: $\checkmark$ **Vittoria!** Il CBC è sicuro contro un attaccante che può scegliere i testi in chiaro e usare un Oracolo.
    
    **Attenzione però all'avviso in verde:** _"This requires a new random IV every time"_.
    
    Se usi la stessa chiave $K$ e per pigrizia usi lo _stesso_ IV per due file diversi, l'inizio della catena sarà identico. L'hacker se ne accorgerà subito e l'IND-CPA fallirà. L'IV non è segreto, ma **deve essere un numero sempre nuovo e imprevedibile (Nonce/Random)**.
    

**Perché ha ancora una X sotto CCA / IND-CCA?**

Il CBC mescola i dati, ma non li _autentica_. Un hacker può comunque intercettare i blocchi, scambiarli di posto o alterare l'IV per manipolare matematicamente il testo decifrato dal server (malleabilità). Per sconfiggere il CCA, dovremo passare alle modalità di "Authenticated Encryption" (come AES-GCM), che probabilmente saranno il boss finale del corso!

Per farti toccare con mano l'eleganza con cui il CBC risolve il problema dell'ECB, ti ho costruito il simulatore del diagramma a blocchi. Prova a inserire due blocchi di testo identici e guarda come la catena genera crittogrammi totalmente diversi!

---
Entriamo nel vivo di uno degli attacchi più devastanti e famosi della storia di Internet (quello che ha bucato i vecchi protocolli SSL/TLS nei primi anni 2000).

Nelle scorse lezioni abbiamo visto che l'AES-CBC ci protegge dal disastro dell'ECB (il problema del pinguino). Ma avevamo lasciato una "X" rossa sotto la colonna **CCA** (Chosen-Ciphertext Attack). Il professore ora ci dimostra il perché: il CBC ha un difetto mortale chiamato **Malleabilità**, e se il server chiacchiera troppo, l'hacker può sfruttarlo per decifrare tutto senza conoscere la chiave.

Benvenuti al **Padding Oracle Attack**. Smontiamolo millimetro per millimetro!

### 16. Il Prerequisito: Lo Stucco (PKCS#7 Padding)

Prima di attaccare, dobbiamo capire come si "tappano i buchi" dei blocchi mancanti. Lo standard mondiale si chiama **PKCS#7**.

Le regole sono semplicissime e si basano sul contare i byte mancanti per arrivare a 16 (la grandezza del blocco AES):

- Se manca **1 byte**: aggiungi `0x01`.
    
- Se mancano **2 byte**: aggiungi `0x02 0x02`.
    
- Se mancano **3 byte**: aggiungi `0x03 0x03 0x03`.
    
- ...
    
- Se il blocco è già perfettamente di 16 byte (pieno): aggiungi **un intero nuovo blocco** fatto di sedici `0x10` (perché 16 in esadecimale si scrive `10`).
    

Quando il server riceve il file cifrato, per prima cosa lo decifra. Poi va a guardare l'ultimo byte in fondo: se legge `0x03`, taglia via gli ultimi 3 byte. Se il padding è sbagliato (es. legge `0x03` ma i due byte prima non sono `0x03`), **il server va in crash e restituisce un errore: "Padding Corrotto!"**.

Questo messaggio di errore è il nostro **Oracolo ($\mathcal{O}_{CBC}^k$)**. Un server che risponde solo "SI" (padding corretto) o "NO" (errore di padding).

### 17. Il Bug Matematico: La Malleabilità del CBC

L'attacco si basa sull'equazione di decifratura del CBC. Concentriamoci sugli ultimi due blocchi, $c_1$ e $c_2$.

Per trovare il testo in chiaro $m_2$, il server fa questo calcolo:

$$m_2 = \text{AES}^{-1}_k(c_2) \oplus c_1$$

Cosa succede se l'hacker intercetta il file al volo e **modifica intenzionalmente un byte del blocco $c_1$**, iniettando un errore controllato ($\Delta$)?

Chiamiamo il blocco hackerato $c'_1 = c_1 \oplus \Delta$.

Il server decifrerà usando il blocco corrotto:

$$m'_2 = \text{AES}^{-1}_k(c_2) \oplus c'_1$$

Sostituiamo $c'_1$ con la sua definizione:

$$m'_2 = \text{AES}^{-1}_k(c_2) \oplus c_1 \oplus \Delta$$

Ma noi sappiamo che $\text{AES}^{-1}_k(c_2) \oplus c_1$ è esattamente il messaggio originale $m_2$! Quindi la formula crolla a:

$$m'_2 = m_2 \oplus \Delta$$

**La rivelazione:** Se l'hacker altera un byte nel blocco cifrato _precedente_ ($c_1$), la matematica dello XOR altera **esattamente lo stesso identico byte** nel blocco in chiaro _successivo_ ($m_2$). Il tutto scavalcando totalmente l'AES, che non se ne accorge nemmeno!

### 18. Fase 1: Scoprire la lunghezza del Padding ($b$)

L'hacker ha in mano $(IV, c_1, c_2)$. Non sa nulla del testo in chiaro $m_2$. Vuole scoprire quanti byte di padding ($b$) ci sono in fondo a $m_2$.

1. L'hacker prende $c_1$ e altera il suo **primissimo byte a sinistra** in modo casuale ($\Delta$). Invia il file modificato al server.
    
2. Il server decifra. Per la regola della malleabilità, il primissimo byte di $m'_2$ verrà alterato.
    
3. Il server va a controllare il padding in fondo a $m'_2$.
    
    - **Caso A (Nessun Errore):** Il server dice "Tutto OK". Cosa significa? Significa che l'alterazione del primo byte NON ha toccato il padding in fondo. L'hacker ha corrotto un pezzo di testo vero.
        
    - **Caso B (Errore di Padding):** Il server dice "NO". Significa che l'hacker, toccando quel byte, ha distrutto il padding.
        

L'hacker si sposta di un byte verso destra e ripete. Appena il server passa da "OK" a "Errore", l'hacker sa esattamente dove finisce il testo vero e dove inizia il padding! Supponiamo di scoprire che il padding è di **$b$ byte**.

### 19. Fase 2: Il Massacro (Decifrare il Testo Vero)

L'hacker ora sa che in fondo a $m_2$ ci sono $b$ byte che valgono tutti `0x0b`.

Il suo obiettivo è scoprire il byte di testo segreto immediatamente precedente al padding. Chiamiamo questo byte ignoto **$M$**.

Guarda l'ultimo schema del professore. È un trucco di magia basato sugli XOR:

1. **L'Allineamento (Spoofing):** L'hacker vuole forzare il server a credere che il padding non sia lungo $b$, ma **$b+1$**.
    
    Come fa? Prende gli ultimi $b$ byte di $c_1$ e fa uno XOR mirato con il valore $b \oplus (b+1)$.
    
    Grazie alla malleabilità, nel server i vecchi byte di padding `0x0b` diventeranno magicamente `0x0b \oplus 0x0b \oplus (b+1) = b+1`. Ora il server si aspetta di trovare un padding corretto fatto di $b+1$ byte.
    
2. **L'Attacco (Il Bruteforce di 1 byte):** Ora manca solo da sistemare il byte $M$ (quello che volevamo rubare). Per far felice il server, $M$ deve trasformarsi nel valore $b+1$.
    
    L'hacker invia 256 richieste al server. In ogni richiesta altera il byte di $c_1$ corrispondente a $M$, iniettando un valore finto $i$ (da `0x00` a `0xFF`).
    
    Nel server, il byte decifrato diventerà: $M \oplus i$.
    
3. **Il Bingo:** L'Oracolo risponderà "Errore di Padding" per 255 volte. Ma per **una sola** di queste richieste, risponderà "SI, il padding è corretto!".
    
    Quando il server dice SI, significa che l'alterazione ha prodotto esattamente il numero $b+1$. Ovvero:
    
    $$M \oplus i = b+1$$
    
    L'hacker risolve la banale equazione:
    
    $$M = (b+1) \oplus i$$
    
    **BOOM. L'hacker ha scoperto il byte segreto $M$ in chiaro!**
    

L'hacker ripete questo giochetto, forzando poi il padding a $b+2$, poi a $b+3$, rubando il file un byte alla volta.

Per farti dominare l'equazione della malleabilità ($m'_2 = m_2 \oplus \Delta$), ti ho costruito un simulatore visivo. Gioca con il byte $\Delta$ e guarda come l'alterazione di $C_1$ scavalchi la decifratura e corrompa chirurgicamente $M_2$, scatenando la reazione dell'Oracolo!

Ecco svelato il motivo per cui l'AES in modalità CBC puro non è più sufficiente per le comunicazioni moderne e ha spinto il mondo a passare all'AES-GCM (Authenticated Encryption). Che spettacolo di ingegneria inversa!

---

Abbiamo appena visto come il famigerato _Padding Oracle Attack_ possa distruggere la sicurezza del CBC sfruttando i messaggi di errore legati allo stucco (PKCS#7).

I crittografi si sono posti una domanda geniale: e se eliminassimo il problema alla radice? **E se smettessimo del tutto di usare il padding?**

Benvenuti nella modalità **AES-CBC-CTS (Ciphertext Stealing)**, ovvero il "Furto del Testo Cifrato". È una tecnica elegantissima per cifrare file di dimensioni irregolari senza aggiungere nemmeno un bit extra.

Smontiamo questo capolavoro di ingegneria pezzo per pezzo!

### 20. La Magia del "Format Preserving"

**Testo Originale:**

> [...] this mode of operation is format preserving, since it produces a ciphertext which is as long as the message, even if $\#m$ is not a multiple of 128.
> 
> IMPORTANT: in this case we must assume that $m$ contains at least one full block, i.e. $\#m > 128$.

**Cosa dice:**

La proprietà fondamentale del CTS è che **preserva il formato (la lunghezza)**. Se il tuo file in chiaro pesa esattamente 1027 byte, il file cifrato peserà _esattamente_ 1027 byte. Nessun blocco finto aggiunto alla fine.

C'è solo una regola d'oro da rispettare: il messaggio deve essere lungo **almeno più di un blocco** ($> 128$ bit). Non puoi usare il CTS su un messaggio di soli 5 byte.

### 21. Il Furto: Come Funziona l'Algoritmo (Il Diagramma)

Il professore disegna un diagramma di flusso che si concentra esclusivamente sugli ultimi due blocchi del messaggio:

- **$m_{t-1}$**: Il penultimo blocco (che per definizione è pieno, 128 bit).
    
- **$m_t$**: L'ultimo blocco (che è "monco", ovvero $< 128$ bit).
    

Tutti i blocchi precedenti ($m_1 \dots m_{t-2}$) vengono cifrati con il normalissimo CBC. La vera magia succede alla fine:

1. **La Banca del Furto:** Il penultimo blocco ($m_{t-1}$) fa il normale XOR con il cifrato precedente ($c_{t-2}$) ed entra nel motore AES.
    
    L'output è un blocco cifrato da 128 bit. Ma invece di chiamarlo $c_{t-1}$, l'algoritmo lo **taglia in due**:
    
    - La parte sinistra (lunga esattamente quanto il blocco monco $m_t$) diventa il nostro ultimo blocco cifrato **$c_t$**.
        
    - La parte destra è la **$\hat{c}$ (The STOLEN Ciphertext)**. Viene temporaneamente "rubata".
        
2. **L'Incrocio e il Finto Padding:** Prendiamo il blocco monco in chiaro ($m_t$) e gli attacchiamo degli zeri finti alla fine ($00\dots0$) per farlo arrivare a 128 bit.
    
3. **Lo XOR Parziale:** Facciamo lo XOR tra questo $m_t$ riempito di zeri e l'intero blocco generato al passo 1 ($c_t \parallel \hat{c}$).
    
    _La matematica è bellissima qui:_
    
    Parte sinistra: $m_t \oplus c_t$
    
    Parte destra: $00\dots0 \oplus \hat{c} = \hat{c}$
    
    Quindi, quello che entra nel secondo AES è: **$(m_t \oplus c_t) \parallel \hat{c}$**.
    
4. **Il Blocco Finale:** Il risultato di quest'ultimo AES diventa il nostro penultimo blocco cifrato **$c_{t-1}$** (da 128 bit completi).
    
5. **L'Output (Lo Scambio):** Quando inviamo il file, scambiamo l'ordine degli ultimi due! L'output sarà: $\dots \parallel c_{t-2} \parallel c_{t-1} \parallel c_t$. La lunghezza totale corrisponde al millimetro a quella originale.
    

### 23. Sbrogliare la Matassa (La Decifratura)

Come fa il destinatario a rimettere a posto questo puzzle incrociato? Il professore ce lo dimostra in tre mosse logiche alla fine della pagina 2:

**Testo Originale:**

> In order to decrypt, from $c_{t-1}$ we obtain $\text{AES}^{-1}_k(c_{t-1}) = (m_t \oplus c_t, \hat{c})$.

**Passo 1: Il Recupero del Bottino.**

Il destinatario ha in mano l'intero blocco $c_{t-1}$. Lo fa passare al contrario nell'AES ($\text{AES}^{-1}$).

Cosa esce? Esce esattamente l'input che avevamo calcolato al punto 3: **$(m_t \oplus c_t)$** a sinistra e il pezzo rubato **$\hat{c}$** a destra. Abbiamo appena recuperato il bottino!

**Testo Originale:**

> From $m_t \oplus c_t$ and from the knowledge of $c_t$ we obtain $m_t$.

**Passo 2: Salvare l'ultimo blocco.**

Il destinatario possiede già $c_t$ (è letteralmente l'ultimo pezzo del file che ha ricevuto). Quindi prende la parte sinistra estratta prima ($m_t \oplus c_t$), fa uno XOR con $c_t$, e ottiene **$m_t$ in chiaro!**

**Testo Originale:**

> From the knowledge of $\hat{c}$, we are able to decrypt $c_t \parallel \hat{c}$ and obtain $m_{t-1}$.

**Passo 3: Ricostruire il penultimo blocco.**

Il destinatario ora ha $c_t$ (dal file) e ha $\hat{c}$ (dal Passo 1). Li incolla insieme ($c_t \parallel \hat{c}$) ricostruendo l'output esatto del primo AES.

Lo fa passare al contrario nell'$\text{AES}^{-1}$ e fa lo XOR con il vecchio $c_{t-2}$.

**BOOM. Ottiene $m_{t-1}$ in chiaro.** Tutto il file è stato decifrato, nessun padding è stato usato, e l'hacker non ha più nessun oracolo di PKCS#7 da interrogare!

Per farti visualizzare questa coreografia di blocchi che si tagliano e si incollano, ho preparato un simulatore passo-passo dell'algoritmo CTS. Prova a scorrere l'animazione e osserva come il blocco "stolen" viaggia da una parte all'altra!

---
Iniziamo la **Lezione 23**. Il professore ci porta all'apice dell'efficienza. Dopo aver visto i disastri dell'ECB e la lentezza del CBC (che ci costringe a cifrare un blocco alla volta in modo sequenziale), ti presento la modalità definitiva: **AES-CTR (Counter Mode)**.

Questa modalità ribalta completamente le regole del gioco. Smettiamo di usare l'AES come un tritacarne per i messaggi e iniziamo a usarlo come un "generatore di maschere casuali".

Smontiamo questa genialata pezzo per pezzo!

### 22. Il Paradigma di Vernam (L'AES diventa un One-Time Pad)

**Testo Originale:**

> AES will not directly encrypt the message, but it will encrypt the counter and use it as a random mask to the message, like in OTP.

**Cosa dice il diagramma:**

Invece di inserire il tuo blocco di testo $m_i$ dentro l'AES, la modalità CTR lascia il testo fuori. Cosa infiliamo nel motore crittografico? **Un contatore!**

L'input dell'AES è formato da un **IV** fisso (es. 90 bit) concatenato con un **contatore binario** $i$ che aumenta a ogni blocco (1, 2, 3...).

L'equazione magica diventa:

$$c_i = m_i \oplus \text{AES}_k(\text{IV} \parallel i)$$

L'AES frulla il contatore e sputa fuori 128 bit di puro rumore bianco. Poi, fai un semplice XOR tra questo rumore e il tuo blocco di testo $m_i$. È l'esatta definizione matematica del One-Time Pad (OTP) o "Cifrario di flusso" (Stream Cipher)!

### 23. La Pagella Perfetta (Le 6 Proprietà del CTR)

Il prof elenca i motivi per cui il CTR è considerato il non plus ultra per le performance:

1. **Cifratura Parallelizzabile (VERO):** A differenza del CBC, qui non devi aspettare il blocco precedente! Puoi calcolare $\text{AES}_k(\text{IV} \parallel 100)$ e $\text{AES}_k(\text{IV} \parallel 101)$ contemporaneamente su due core diversi del tuo processore.
    
2. **Decifratura Parallelizzabile (VERO):** Come sopra. Efficienza mostruosa.
    
3. **Random Access (VERO):** Vuoi saltare direttamente al blocco 50 del file? Prendi l'IV, ci attacchi il numero 50, lo cifri e fai lo XOR col blocco 50. Istantaneo.
    
4. **$m_i = m_j \implies c_i = c_j$ (FALSO, Sbarrato):** Il problema del pinguino di Linux è morto. Anche se i blocchi $m$ sono identici, il contatore $i$ cambia sempre, quindi la maschera XOR sarà sempre diversa.
    
5. **Danno da 1 bit circoscritto:** Se l'hacker flippa 1 bit nel crittogramma, **si corrompe solo 1 bit nel testo in chiaro**. Perché? Perché l'errore non entra mai dentro l'AES! Si ferma al momento dello XOR finale. Niente effetto valanga sul testo.
    
6. **Il Miracolo Hardware (Niente Decifratura AES):** Guarda bene la formula per decifrare. Per riottenere $m_i$, devi solo spostare lo XOR:
    
    $$m_i = c_i \oplus \text{AES}_k(\text{IV} \parallel i)$$
    
    **La funzione inversa $\text{AES}^{-1}$ è scomparsa!** Usi la funzione di _cifratura_ (forward) anche per decifrare. Questo significa che i produttori di microchip non devono stampare i pesantissimi e lenti circuiti per l'`InvMixColumns` (quelli con le moltiplicazioni per 14 che abbiamo visto nelle lezioni scorse). Risparmio di silicio, batteria ed energia elettrica enorme.
    

### 24. La Piaga della Malleabilità (L'Incubo del CCA)

Nelle slide 2 e 3, il prof ci riporta con i piedi per terra mostrando la Tabella di Sicurezza.

Sia ECB, che CBC, che CTR hanno una **X rossa** sulla sicurezza **CCA** (e IND-CCA).

**Testo Originale:**

> The problem is rooted in the fact that the attacker can always generate a "new ciphertext" _without_ knowing the key...
> 
> ...the decryption oracle will decrypt any garbage they receive, provided that it looks different from the challenge.

**Cosa dice:**

La modalità CTR è un OTP perfetto, ma eredita il difetto più letale dell'OTP: l'estrema malleabilità (linearità).

Siccome $c_i = m_i \oplus \text{Mask}$, se l'hacker altera il file cifrato aggiungendo un suo difetto ($\Delta$), il server decifrerà esattamente $m_i \oplus \Delta$.

L'Oracolo decifra fedelmente qualsiasi "spazzatura" alterata gli venga inviata, permettendo gli attacchi attivi (CCA) come il _Padding Oracle Attack_ che abbiamo visto sul CBC.

### 25. Il Boss Finale: L'Authenticated Encryption

**Testo Originale:**

> The trick for obtaining CCA-security is to make sure that the decryption oracle has a way to refuse almost all the decryption queries that the adversary creates...
> 
> The idea is to append a hash-like tag to the message, that can show if the ciphertext has been altered.
> 
> This is a vast topic in private-key cryptography that is called **authenticated encryption**.

**Cosa dice:**

Come curiamo la malleabilità? Dobbiamo togliere all'Oracolo il vizio di decifrare la spazzatura. Il server deve guardare il file in arrivo e dire: _"Ehi, questo file è stato manomesso durante il tragitto. Mi rifiuto di decifrarlo!"_

Il trucco è incollare alla fine del file una "firma digitale" o "tag" (chiamato MAC - Message Authentication Code). Se l'hacker flippa anche un solo bit del crittogramma, il tag si rompe, la firma non combacia più, e il server cestina tutto prima ancora di iniziare a decifrare.

Questa unione tra privacy (cifratura) e integrità (firma) si chiama **Authenticated Encryption (AE)**, e la vedremo probabilmente con l'AES-GCM.

Per farti memorizzare la proprietà n°6, la più affascinante per un ingegnere informatico (usare la cifratura per decifrare), ti ho costruito un simulatore del motore AES-CTR. Prova a passare dalla modalità Cifratura alla Decifratura e osserva come il blocco AES non inverta mai la marcia!

Direi che siamo prontissimi per vedere questo famoso "tag" all'opera e chiudere il cerchio della sicurezza perfetta. Manda pure le prossime slide se ci sono!

---
Questa è la lezione che chiude il cerchio. Abbiamo visto come l'AES (motore) vada inserito in una modalità operativa (come CTR o CBC) per cifrare file lunghi, ma abbiamo anche scoperto che queste modalità, da sole, sono vulnerabili agli attacchi di tipo CCA (scelta del testo cifrato) perché mancano di **autenticazione**.

Oggi il professore ci presenta la soluzione definitiva che domina internet oggi: **AES-GCM (Galois/Counter Mode)**, ovvero l'**Authenticated Encryption (AE)**.

Smontiamo questo capolavoro di ingegneria crittografica.

### 25. Il Concetto di "Authenticated Encryption"

Il problema di CBC e CTR è che sono "malleabili": un hacker può alterare il file cifrato, e il server lo decifrerà comunque producendo un messaggio corrotto, ma leggibile. L'hacker può usare questo comportamento come un "oracolo" per rubare informazioni byte dopo byte.

La soluzione è l'**Authenticated Encryption**: non ci limitiamo a rendere il messaggio illeggibile (cifratura), dobbiamo anche garantire che **nessuno lo abbia toccato** (autenticazione). Se un solo bit del messaggio cifrato viene alterato, il server deve rifiutarsi di decifrare, restituendo un errore secco.

### 26. Il Motore del GCM: CTR + GHASH

Il GCM fonde due tecnologie diverse:

1. **AES-CTR (Counter Mode):** Garantisce la **Privacy**. Usiamo i contatori per cifrare il messaggio in modo indistinguibile e veloce.
    
2. **GHASH (Galois Hash):** Garantisce l'**Integrità**. È una funzione hash speciale basata sull'algebra dei polinomi (la stessa che abbiamo studiato per la S-Box dell'AES!).
    

**Come funziona (Diagramma a pagina 1-2):**

- Il messaggio viene cifrato in CTR (i vari $c_1, c_2, \dots$).
    
- Parallelamente, questi blocchi cifrati vengono fatti passare attraverso una funzione di hashing basata su moltiplicazione polinomiale nel campo $\mathbb{F}_{2^{128}}$, usando una chiave segreta di autenticazione $H = AES_K(00\dots0)$.
    
- Il risultato finale di questa catena di moltiplicazioni polinomiali, insieme a IV e lunghezza del messaggio, viene compresso in un unico **Autentication Tag**.
    

Il risultato finale inviato è: **$IV \parallel c_1 \parallel c_2 \dots \parallel c_t \parallel \text{Tag}$**.

### 27. La Difesa di Ferro (Come funziona la decifratura)

Quando il destinatario riceve il pacchetto, la procedura è rigida (guarda lo pseudocodice nell'ultima pagina):

1. **Check del Tag:** La primissima cosa che il server fa, _prima di toccare i dati_, è ricalcolare il tag basandosi sui blocchi ricevuti.
    
2. **Validazione:** Se il tag calcolato non combacia con quello ricevuto, il server sa che il file è stato alterato. **Return $\perp$ (Errore/Abort)**. La decifratura non avviene nemmeno, quindi l'hacker non può usare il server come "oracolo" per i suoi attacchi.
    
3. **Decifratura:** Solo se il tag è corretto, il server procede a decifrare i blocchi $c_i$ e a verificare l'eventuale padding (se necessario).
    

### 4. La Tabella della Verità (Pagina finale)

Il professore chiude con la tabella riassuntiva che è il Santo Graal della sicurezza simmetrica:

|**Modalità**|**IND-CPA (Privacy)**|**IND-CCA (Integrità/Sicurezza)**|
|---|---|---|
|**AES-ECB**|X|X|
|**AES-CBC**|$\checkmark$|X|
|**AES-CTR**|$\checkmark$|X|
|**AES-GCM**|$\checkmark$|**$\checkmark$ (Vittoria!)**|

**Perché AES-GCM vince su tutto?**

Perché è l'unica modalità che garantisce sia la **Privacy** (l'hacker non legge i dati) che l'**Integrità** (l'hacker non può modificare i dati senza farsi scoprire). Il GCM è il motivo per cui, se un hacker prova a fare un attacco di tipo _Padding Oracle_ contro una connessione HTTPS moderna, il sistema rifiuta la connessione all'istante, rendendo l'attacco impossibile.

È un'architettura magnifica: usa la moltiplicazione polinomiale (Galois) non solo per creare la S-Box dell'AES, ma anche per creare il tag che garantisce l'integrità del messaggio.

Ti ho costruito un simulatore visivo dell'**AES-GCM Authentication**, che mostra la differenza fondamentale tra CTR (che permette alterazioni) e GCM (che le blocca).

