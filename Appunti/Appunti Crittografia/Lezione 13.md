Questa lezione getta le basi teoriche per i sistemi che usiamo ogni giorno per navigare in sicurezza. Smontiamo le quattro immagini seguendo il nostro metodo implacabile.


---

### 1. Il Limite di Diffie-Hellman (Public-Key Encryption)

**Testo Originale:**
> **LECTURE # 13**
> **4. PUBLIC-KEY ENCRYPTION**
> The Diffie-Hellman protocol introduced in chapter 3 will be crucial in cryptographic protocols since it allows two mutually unknown parties to agree on a shared secret while interacting on a public network, but, as it is, it misses one fundamental feature: to encrypt **every possible message** (it is indeed used only to agree on a random key).
> This task will be accomplished by **public-key cryptosystems** which, basing their security on a hard mathematical problem, will allow the two parties to encrypt (and decrypt) messages on the flow, without the need of waiting for a key agreement to be used for block ciphers (introduced in Chapter 5) and other symmetric cryptosystems.

**Cosa dice:**
Il prof mette subito in chiaro una cosa: Diffie-Hellman è geniale, ma fa una cosa sola. Serve a creare un lucchetto condiviso a distanza. Fine. Non puoi usarlo per spedire il testo di una mail o la password della tua carta di credito in modo diretto.
Per spedire *qualsiasi* messaggio al volo senza dover prima concordare una chiave, ci servono i "Crittosistemi a Chiave Pubblica". Questi sistemi usano problemi matematici insolubili per permettere a chiunque di criptare un dato, in modo che solo il destinatario legittimo possa decriptarlo.

**Spiegazione dettagliata:**
Questa è la differenza tra scambiarsi un segreto per poi parlarsi (Simbiosi tra Asimmetrica e Simmetrica) e spedire direttamente una scatola blindata. Finora abbiamo visto come Alice e Bob si mettono d'accordo sul numero $g^{ab}$. Da oggi studiamo come Alice può prendere un file PDF, criptarlo con una formula matematica pubblica di Bob, e spedirglielo con la certezza che solo lui potrà aprirlo.

---

### 2. La Strada a Senso Unico (One-Way Function)

**Testo Originale:**
> We have discussed the security of CDHP in the previous chapter and we could come to the conclusion, but not necessarily to a proof, that we can **assume** that the CDHP is hard in a cyclic group $G$ as long as the CDLP is hard in $G$.
> Therefore, the **exponential function** in a group $G$ of order $\#G=n$ is a function
> $f: \mathbb{Z}_n \to G, \quad x \mapsto g^x$
> which satisfy the following properties:
> 1) $f$ is efficiently computable [there exist a polynomial-time algorithm that given $x$ computes $f(x)$]; (see, for example, Proposition 3.3 for the efficiency of the exponential function in a cyclic group)
> 2) given $h \in G$, it is computationally infeasible to determine $x \in \mathbb{Z}_n$ s.t. $f(x)=h$ [there exists no polynomial-time algorithm TODAY that solves CDLP].
> A function with such properties is called a **one-way function** [...]

**Cosa dice:**
Il professore definisce il blocco fondante di tutta la crittografia asimmetrica: la **Funzione a Senso Unico (One-Way Function)**. 
Usa l'elevamento a potenza modulare ($g^x$) come esempio perfetto. Questa funzione ha due regole inflessibili:
1. **Andare in avanti è facile e veloce:** Se hai la base $g$ e l'esponente $x$, un computer ci mette un istante a calcolare il risultato $g^x$.
2. **Tornare indietro è impossibile:** Se hai il risultato finale e la base $g$, e devi trovare l'esponente $x$ (il famigerato Logaritmo Discreto), ti schianti contro un muro matematico.

**Spiegazione dettagliata:**
Pensa a una funzione a senso unico come a un tubetto di dentifricio. Spremere il dentifricio fuori dal tubetto (calcolare la funzione) è facilissimo. Ma cercare di rimettere il dentifricio dentro al tubetto (invertire la funzione) è un incubo pratico. La crittografia asimmetrica si basa sul fatto che l'hacker è condannato a fare la seconda operazione.

---

### 3. Il Gioco dell'Hacker e la "Botola" Segreta (Trapdoor)

**Testo Originale:**
> [...] and given a one-way function $f: M \to C$ it is possible to define the following 2-party game between an adversary $\mathcal{A}$ and a challenger $\mathcal{C}$ as follows:
> *(Schema del gioco: $\mathcal{C}$ sceglie $x \in_R M$, invia $y := f(x)$ ad $\mathcal{A}$. $\mathcal{A}$ risponde con $z \in M$)*
> where $z$ is $\mathcal{A}$'s guess for $x$, therefore $\mathcal{A}$ wins the game $\iff f(z)=y$.
> Requiring that $f$ is a one-way function is equivalent to asking that no PPT algorithm $\mathcal{A}$ can win the previous game.
> The first public-key cryptosystem introduced soon will be based on the similar concept of **trapdoor one-way function**, which is a one-way function $f: M \to C$ for which there exists an extra piece of information called "trapdoor" such that
> 1) $f(x)$ can be computed efficiently for each $x$
> 2) given $y = f(x)$, computing $x$ is computationally infeasible
> 3) given $y = f(x)$ and **knowing the trapdoor**, $x$ can be computed efficiently.

**Cosa dice:**
Prima ripropone la struttura del "gioco" per formalizzare la sicurezza: una funzione è davvero "a senso unico" se nessun programma hacker ($\mathcal{A}$) riesce a indovinare l'input $x$ partendo dall'output $y$.
Ma poi svela il vero segreto dei crittosistemi moderni: la **Trapdoor One-Way Function (Funzione a Senso Unico con Botola)**. 
Questa funzione aggiunge una terza regola magica alle due precedenti: se possiedi un'informazione speciale e segreta (la "botola" o *trapdoor*), invertire la funzione diventa improvvisamente facilissimo!

**Spiegazione dettagliata:**
La botola è il concetto più elegante dell'informatica. Riprendiamo l'esempio del lucchetto a scatto:
1. **Chiuderlo è facile:** (Calcolare $f(x)$). Chiunque può prendere il lucchetto aperto e farlo scattare.
2. **Aprirlo è impossibile:** (Calcolare l'inverso). L'hacker può tirare, spaccare, usare la forza bruta o l'algoritmo di Pollard, ma il lucchetto non cede.
3. **La Botola:** Se tu, e solo tu, possiedi la minuscola **chiave fisica** (la Trapdoor), la infili nella serratura, giri, e il lucchetto si apre senza sforzo.

Nelle prossime lezioni il professore ci svelerà cos'è matematicamente questa "botola" e come viene costruita (probabilmente parlando del sistema RSA). 

---

Qui formalizziamo i tre verbi sacri della crittografia: **Generare, Cifrare, Decifrare**, per poi introdurre il colpo di scena finale: **Firmare**.
### 4. L'Anatomia di un Crittosistema (Definition 4.1)

**Testo Originale:**
> **4.1 Encrypt, decrypt, sign and verify**
> The idea of public-key encryption is that we can associate to each party a public and a private key in such a way
> $cB's \text{ plaintext} + cA's \text{ public key} \longrightarrow \text{ciphertext}$
> $\text{ciphertext} + cA's \text{ private key} \longrightarrow cB's \text{ plaintext}$
> Let us now define the main ingredients for a public-key cryptosystem
> **Definition 4.1**
> Let $M, C$ and $K$ be three sets, acting respectively as a message, a ciphertext, and a key space.
> The triple $(Gen, Enc, Dec)$ is called a **public-key cryptosystem** on $(M, C, K)$ if the following conditions hold:
> 1) **Gen** (key-generation algorithm) is a PPT algorithm which takes as input a security parameter $N$ (how large we want the size of the input to be) and outputs:
> - the private key $sk \in K$
> - the public key $pk \in K$;
> 2) **Enc** (the encryption algorithm) is a PPT algorithm which takes as input the public key $pk$ and a message $m \in M$ and outputs $Enc_{pk}(m) \in C$ the encryption of $m$ under $pk$;
> 3) **Dec** (the decryption algorithm) is a deterministic algorithm which takes as input the private key $sk$ and a ciphertext $c \in C$ and outputs $Dec_{sk}(c) \in M$ or an error symbol $\bot$;
> 4) **(reversibility)** $\forall m \in M \quad Dec_{sk}(Enc_{pk}(m)) = m$;

**Cosa dice:**
Il professore definisce le tre "scatole magiche" che compongono ogni sistema a chiave pubblica:
1.  **L'Algoritmo Gen (Fabbro):** Gli dai la lunghezza desiderata della chiave ($N$), e lui forgia una coppia indissolubile: una chiave segreta ($sk$, *secret key*) da tenere per sé, e una pubblica ($pk$, *public key*) da urlare al mondo.
2.  **L'Algoritmo Enc (Lucchetto):** Prende il messaggio in chiaro ($m$) e lo chiude usando la chiave pubblica del destinatario ($pk$), trasformandolo in un testo incomprensibile ($c$, *ciphertext*).
3.  **L'Algoritmo Dec (Chiave):** Prende il testo incomprensibile ($c$) e lo apre usando la chiave segreta ($sk$), restituendo il messaggio originale ($m$) o un errore se il file è corrotto.
La regola 4 (Reversibilità) è la prova del nove: se chiudi un messaggio e poi lo riapri con le chiavi giuste, devi riavere il messaggio intatto, lettera per lettera.

**Spiegazione dettagliata:**
Nota bene lo schema iniziale: Bob vuole mandare un messaggio ad Alice. Bob non usa la sua chiave! Prende il suo messaggio, prende la chiave *pubblica di Alice* (che chiunque può scaricare), e chiude il pacco. Da quel momento, nemmeno Bob stesso può più riaprirlo. L'unica entità nell'universo in grado di sbloccare quel pacco è Alice, usando la sua chiave *privata*. Abbiamo appena eliminato la necessità di incontrarsi in un vicolo buio per scambiarsi la password!

---

### 5. Il Test di Sicurezza Definitivo (Regola 5)

**Testo Originale:**
> 5) **(security w.r.t. passive eavesdroppers)**
> No PPT algorithm $\mathcal{A}$ can win the following game with a challenger $\mathcal{C}$ with non-negligible probability:
> *(Schema del gioco:)*
> $\mathcal{C}$ calcola $(pk, sk) := Gen(N)$
> $\mathcal{C}$ invia $pk$ ad $\mathcal{A}$
> $\mathcal{C}$ sceglie a caso $m \in_R M$
> $\mathcal{C}$ invia $c := Enc_{pk}(m) \in C$ ad $\mathcal{A}$
> $\mathcal{A}$ risponde con $m^*$
> where $\mathcal{A}$ wins the game $\iff m^* = m$.

**Cosa dice:**
Come per Diffie-Hellman, non basta dire "è sicuro", bisogna dimostrarlo con un gioco matematico. 
Lo Sfidante ($\mathcal{C}$) crea le due chiavi. Dà la chiave pubblica all'Hacker ($\mathcal{A}$). Poi lo Sfidante pensa a un messaggio casuale $m$, lo cripta, e lancia il file criptato $c$ all'Hacker.
L'Hacker vince solo se, pur avendo in mano il file criptato e il "lucchetto" pubblico, riesce a sputare fuori l'esatto messaggio originale in chiaro ($m^* = m$). 
Il sistema è certificato sicuro solo se la probabilità che l'Hacker vinca è *trascurabile* (torna la famosa funzione $\varepsilon$ che scende a picco!).

**Spiegazione dettagliata:**
Questo gioco formalizza l'incubo di chiunque intercetti il Wi-Fi di un bar (passive eavesdropper). L'hacker vede la tua richiesta di connessione (la chiave pubblica) e vede i pacchetti dati che passano (il ciphertext). Ha letteralmente i mezzi e il bersaglio, ma la crittografia gli garantisce che la decodifica richiederà un tempo che va oltre la vita dell'universo.

---

### 6. Il Ribaltamento delle Regole: La Firma Digitale

**Testo Originale:**
> When cA and cB are using a public-key cryptosystem, a precise distinction must be set between what is of public use ($pk, Enc$) and what is of cA's exclusive use ($sk, Dec$).
> Reading this from another perspective, the public-key cryptosystem can be used from cA to do something that only cA and no one else must be able to do: **sign!**
> Using a public-key cryptosystem in a different way we can obtain a (digital) signature scheme which will allow cA to sign their message $m$ in such a way every other party knowing cA's public key $pk_{cA}$ can determine if the message has been signed by the only possessor of the signer's private key $sk_{cA}$, that is only cA.
> A digital signature scheme will work in this way:
> $cA's \text{ message} + cA's \mathbf{\text{private key}} \longrightarrow \text{signature}$
> $cA's \text{ message} + \text{signature} + cA's \mathbf{\text{public key}} \longrightarrow \text{YES/NO}$
> (everybody will be able to tell if the signature is valid or not)

**Cosa dice:**
Qui il prof compie un salto logico geniale. Cosa succede se usiamo la cassaforte "al contrario"? 
Invece di usare la chiave *pubblica* per chiudere un segreto che solo tu puoi leggere, tu Alice usi la tua chiave *privata* per "sigillare" un documento visibile a tutti. 
A quel punto, chiunque nel mondo può prendere la tua chiave pubblica e provare a verificare quel sigillo. Se la chiave pubblica di Alice riesce a sbloccare il sigillo, c'è una sola inconfutabile conclusione logica: quel sigillo è stato impresso da chi possiede l'unica chiave privata corrispondente. Ovvero Alice.

**Spiegazione dettagliata:**
Questa è la nascita della **Firma Digitale**, il sistema che usiamo per certificare documenti, contratti e persino gli aggiornamenti di sistema di Windows e Apple.
Attenzione ai colori e ai verbi usati dal prof negli appunti:
* **Cifratura (Nascondere):** Pubblico blocca $\to$ Privato sblocca. L'obiettivo è la Segretezza.
* **Firma (Certificare):** Privato "blocca" la firma $\to$ Pubblico verifica la firma (YES/NO). L'obiettivo non è nascondere il messaggio (che tutti leggono), ma dimostrarne l'Autenticità e la Non-Ripudiabilità.

Questo cambio di prospettiva è uno dei passaggi più eleganti di tutta l'informatica teorica. Pronto per esplorare l'algoritmo esatto che esegue queste magie? Probabilmente è arrivato il momento del mitico RSA.

---
Smontiamo queste due pagine, che sono letteralmente il "Bignami" della crittografia moderna.
### 7. La Cassaforte Magica (L'Analogia Definitiva)

**Testo Originale:**

> Roughly said, we have learned that a public-key cryptosystem is a special kind of locker that can be locked by everyone and unlocked by the only possessor of the private key **or** locked by the possessor of the private key and unlocked by everyone.
> 
> Accordingly, cA's locker can be operated in two different ways, for two different purposes (encrypt / decrypt or sign / verify):

**Cosa dice:**

Dimentica la matematica per un attimo. Un sistema a chiave pubblica è una cassaforte stranissima. Ha due chiavi separate: una la tieni tu in tasca (Privata), l'altra ne fai mille copie e la regali a tutti (Pubblica).

Questa cassaforte ha un potere speciale: **se la chiudi con una chiave, può essere riaperta SOLO con l'altra**. Non puoi usare la stessa chiave per chiudere e aprire.

Questo ci permette di usarla per due scopi diametralmente opposti.

---

### 8. E/D: Cifratura e Decifratura (L'Arte del Segreto)

**Testo Originale:**

> **E/D**
> 
> The locker is locked by the sender cB and can be opened only by cA. This protects the **confidentiality** of the message $m$ sent by cB exclusively to cA.
> 
> _(Diagramma: Bob prende $m$, applica il lucchetto azzurro $pk$. Invia la scatola ad Alice. Alice usa la chiave rossa $sk$ per aprire e ottenere $m$)_.

**Cosa dice:**

Questo è l'uso classico (Encrypt/Decrypt). L'obiettivo qui è la **Confidenzialità**: nessuno deve leggere i fatti miei.

Bob vuole mandare una lettera d'amore ad Alice. Prende la cassaforte di Alice, ci mette dentro la lettera $m$, e la chiude con la **Chiave Pubblica** (il lucchetto azzurro $pk$).

Da quel _clic_, la scatola è blindata. Persino Bob non può più aprirla. La scatola viaggia su internet. Gli hacker la vedono, ma non hanno la chiave. Quando arriva ad Alice, lei tira fuori la sua **Chiave Privata** (quella rossa $sk$), la infila, gira e legge la lettera.

---

### 9. S/V: Firma e Verifica (L'Arte della Certezza)

**Testo Originale:**

> **S/V**
> 
> cA has a public message $m$ and they want everybody to be able to verify the **authenticity** of the message, i.e. that the message has been produced by cA. Then the message is put in the locker by cA and it can be opened by everyone else.
> 
> _(Diagramma: Alice ha un messaggio $m$ in chiaro. Crea una copia, la mette nella scatola e la chiude con la chiave rossa $sk$. Invia $m$ in chiaro + scatola chiusa a tutti. Tutti aprono la scatola col lucchetto azzurro $pk$. Se il contenuto della scatola è identico a $m$, la firma è valida)_.

**Cosa dice:**

Questo è l'uso capovolto (Sign/Verify). L'obiettivo non è nascondere, ma garantire l'**Autenticità**: tutti devono sapere che questo ordine l'ho dato io e non un impostore.

Alice scrive un contratto $m$ (pubblico). Per dimostrare che è suo, ne fa una copia, la mette nella cassaforte e la chiude usando la sua **Chiave Privata** (quella rossa $sk$).

Alice pubblica sia il contratto in chiaro, sia la cassaforte chiusa.

Chiunque nel mondo può scaricare la **Chiave Pubblica** di Alice (il lucchetto azzurro $pk$) e usarla per aprire la cassaforte. Se la cassaforte si apre, e il documento all'interno è identico al contratto $m$ in chiaro... **Bingo!** Poiché solo la chiave pubblica di Alice ha aperto quella scatola, significa che solo la chiave privata di Alice può averla chiusa. La firma è autentica al 100%.

---

### 10. Il Prossimo Capitolo: L'Ingresso del Re

**Testo Originale:**

> In the next section we will travel through all the steps that led to the first public-key cryptosystem: **RSA** (public from the end of '70s)

Il professore chiude la lezione con un annuncio epico. Abbiamo visto la teoria, abbiamo giocato con le casseforti, abbiamo capito i logaritmi discreti. Adesso ci porta nel 1977, nei laboratori del MIT, a conoscere Rivest, Shamir e Adleman (RSA): i tre tizi che hanno tradotto questa analogia della "cassaforte a doppia mandata" in un algoritmo matematico basato sui numeri primi che ancora oggi, mezzo secolo dopo, protegge i conti in banca di tutto il pianeta.

---

### 11. Il Requisito Fondamentale (La Biettività)

**Testo Originale:**

> **4.2 towards RSA**
> 
> If we want to obtain a public-key cryptosystem, we must make sure that condition 4 of Definition 4.1 is satisfied, which means that the encryption process is reversible. In mathematical terms, this is the **bijectivity** of the encryption functions.
> 
> Once cA and cB have their own bijective trapdoor one-way encryption functions, denoted by $f_A$ and $f_B$ respectively, they can use them in this way:

**Cosa dice:**

Per far funzionare tutto l'impianto della crittografia a chiave pubblica, la Regola 4 (Reversibilità) è sacra. Un messaggio criptato deve poter tornare _esattamente_ al messaggio originale. In matematica, una funzione che garantisce questo si chiama **biva** (biunivoca o _bijective_): a ogni singolo messaggio in chiaro corrisponde uno e un solo messaggio cifrato, e viceversa. Non ci possono essere sovrapposizioni o ambiguità.

Assumendo che Alice ($f_A$) e Bob ($f_B$) abbiano trovato queste funzioni magiche (biettive e con la "botola"), il prof ci mostra un trucco geniale per usarle.

**Spiegazione dettagliata:**

La biettività è vitale. Immagina se la funzione crittografica trasformasse sia la parola "ATTACCO" che la parola "RITIRATA" nello stesso codice cifrato "X7Y9". Quando il generale decripta "X7Y9", cosa deve fare? La biettività garantisce matematicamente che non ci siano mai due input che finiscono nello stesso output.

---

### 12. Il Trucco del Doppio Lucchetto (Il Protocollo a 3 Passaggi)

**Testo Originale:**

> _(Diagramma visuale: cA e cB)_
> 
> 1. cA mette il lucchetto $f_A$ sul messaggio $\to$ viaggia chiuso.
>     
> 2. cB riceve, mette il suo lucchetto $f_B$ sul messaggio $\to$ viaggia chiuso (ha 2 lucchetti!).
>     
> 3. cA riceve, toglie il suo lucchetto $f_A^{-1}$ $\to$ viaggia chiuso (ha solo il lucchetto di cB).
>     
> 4. cB riceve, toglie il suo lucchetto $f_B^{-1}$ $\to$ ottiene il messaggio originale!
>     

**Cosa dice:**

Questo è un famoso paradosso crittografico (noto come protocollo di Shamir senza scambio di chiavi).

Alice vuole mandare una scatola a Bob, ma non si fidano dei postini.

1. Alice chiude la scatola col suo lucchetto e la spedisce.
    
2. Bob la riceve, ma non ha la chiave di Alice. Allora cosa fa? Aggiunge il _suo_ lucchetto alla scatola e la rispedisce indietro.
    
3. Alice la riceve, usa la sua chiave per togliere il _suo_ lucchetto, e la rispedisce a Bob.
    
4. Bob la riceve, usa la sua chiave per togliere l'ultimo lucchetto rimasto (il suo), e apre la scatola.
    

Nessuno ha mai dovuto scambiarsi le chiavi, e la scatola non ha mai viaggiato aperta!

---

### 13. La Legge della Commutatività (La Realtà vs La Matematica)

**Testo Originale:**

> From a mathematical point of view, closing the locker means applying the easy-to-compute-hard-to-invert function $f$. In the way displayed above we have computed
> 
> $f_B^{-1} f_A^{-1} f_B f_A(m) = t$.
> 
> If we expect that $t = m$ is because we are expecting that $f_A, f_A^{-1}, f_B$ and $f_B^{-1}$ **commute!**
> 
> So we have learned that a public key cryptosystem can be obtained from bijective commuting functions.

**Cosa dice:**

Il prof svela l'inghippo. Nel mondo reale, l'ordine in cui togli i lucchetti da una scatola non ha importanza. Ma in matematica, l'ordine in cui applichi le funzioni cambia _tutto_ (è come mettersi prima le scarpe e poi i calzini, o viceversa).

L'equazione matematica del doppio lucchetto è $f_B^{-1} ( f_A^{-1} ( f_B ( f_A ( m ) ) ) ) = t$.

Affinché il risultato finale $t$ sia uguale al messaggio originale $m$, le funzioni devono potersi annullare a vicenda ($f_A$ con $f_A^{-1}$). Ma per annullarsi, devono stare vicine! Questo significa che la matematica che usiamo deve **commutare**: devi poter scambiare di posto le operazioni senza alterare il risultato.

---

### 14. Il Santo Graal: I Monomi di Potenza

**Testo Originale:**

> What is a set of functions that **always** commute?
> 
> POWER MONOMIALS.
> 
> Indeed, if we take for example
> 
> $f_1: x \mapsto x^e$
> 
> $f_2: x \mapsto x^d$
> 
> for some integers $e,d$ we have
> 
> $f_1 f_2(x) = f_1(x^d) = (x^d)^e = x^{de} = x^{ed} = (x^e)^d = f_2(x^e) = f_2 f_1(x)$.
> 
> Let us now put all the pieces together, seeing in which condition power monomials are bijective in a finite group.

**Cosa dice:**

Quali operazioni matematiche avanzate ci permettono di scambiare l'ordine a piacimento? I **Monomi di Potenza** (l'elevamento a potenza)!

Il professore lo dimostra in una riga: se Alice eleva il messaggio alla $e$ (mette il lucchetto), e Bob eleva il risultato alla $d$ (mette il secondo lucchetto), il risultato è il messaggio elevato alla $e \cdot d$.

E siccome nella moltiplicazione normale $e \cdot d$ è uguale a $d \cdot e$, possiamo invertirli senza rompere la matematica!

**Spiegazione dettagliata:**

Ci siamo. Questa è la nascita di **RSA**. Il professore ci ha appena detto che le chiavi crittografiche pubbliche e private non saranno altro che _esponenti_ (la $e$ e la $d$).

Il lucchetto si chiude elevando alla $e$, e si apre elevando alla $d$. Siccome le potenze commutano, tutta l'architettura dei lucchetti a chiave pubblica sta in piedi alla perfezione.

L'ultima frase lancia la sfida per la prossima lezione: abbiamo deciso di usare le potenze, ma dobbiamo capire a quali condizioni queste potenze rispettano la primissima regola che abbiamo visto (la biettività, ovvero non creare due messaggi cifrati uguali).

---
Il professore ci dà la risposta con una dimostrazione che lui stesso definisce la chiave di volta di tutta la faccenda ("unlocks crypto"). Smontiamola subito.v
### 15. La Chiave di Sblocco Universale (Proposition 4.3)

**Testo Originale:**
> **Proposition 4.3 (unlocks crypto)**
> Let $(G, \cdot)$ be a group of order $n$, $e \in \mathbb{Z}_n$ such that $\gcd(e, n) = 1$. Then $f: G \to G, x \mapsto x^e$ is bijective.
> **Proof**
> Let $d \in \mathbb{Z}_n$ be such that $ed = 1 \bmod n$.
> Then $h: G \to G, x \mapsto x^d$ is such that
> $h(f(x)) = h(x^e) = x^{ed} = x^{1+kn}$
> for some $k \in \mathbb{Z}$. Therefore
> $h(f(x)) = x$, so $f$ is invertible and its inverse is $h$. $\blacksquare$

**Cosa dice:**
Il teorema stabilisce la regola d'oro per creare le chiavi pubbliche e private. Se scegli un esponente $e$ (che sarà la tua chiave pubblica) che non ha divisori in comune con la grandezza dell'ingranaggio $n$ (ovvero $\gcd(e, n) = 1$), allora la funzione che eleva alla $e$ ($x^e$) è matematicamente biettiva. Non creerà mai collisioni e sarà sempre invertibile.
La dimostrazione ci fa vedere come si inverte: siccome $\gcd=1$, sappiamo che esiste per forza l'inverso modulare $d$ (che sarà la tua chiave privata) tale che $e \cdot d \equiv 1 \pmod n$. 
Se applichi prima $e$ e poi $d$, ottieni $x^{ed}$. Per le regole del modulo, questo equivale a $x^{1+kn}$. E siccome in un gruppo di ordine $n$ elevare qualcosa alla $n$ fa tornare sempre al punto di partenza (diventa 1), il pezzo $kn$ sparisce e ti rimane esattamente $x^1$, cioè il messaggio originale $x$!

**Spiegazione dettagliata:**
Qui la matematica fa quasi commuovere per quanto si incastra perfettamente. Ti ricordi le lezioni sul "Grimaldello Incastrato" e l'Algoritmo di Euclide Esteso? Ecco a cosa servivano!
Tu scegli una chiave pubblica $e$ in modo che sia "coprima" con l'ordine del gruppo $n$. Il computer calcola istantaneamente l'inverso modulare $d$. 
* **Chiave Pubblica (Lucchetto):** L'esponente $e$.
* **Chiave Privata (Botola):** L'esponente $d$.
Bob chiude il messaggio facendo $x^e$. Tu Alice apri il messaggio facendo $(x^e)^d$. Le potenze si moltiplicano, diventano $1 \pmod n$, e il lucchetto si volatilizza lasciando il messaggio in chiaro.

---

### 16. Il Cliffhanger: Il Problema del Terreno (Not every group is good!)

**Testo Originale:**
> there is still something missing: not every group is good!

**Cosa dice:**
Attenzione a non festeggiare troppo presto. Abbiamo i lucchetti perfetti (gli esponenti $e$ e $d$) e sappiamo come farli funzionare matematicamente. Ma il professore ci lancia un avvertimento vitale: il "gruppo" (cioè il campo di battaglia dove facciamo questi calcoli) non può essere scelto a caso. Se scegliamo il gruppo sbagliato, il sistema crolla.

**Spiegazione dettagliata:**
Questa singola riga introduce il vero segreto di RSA. Riguardiamo la dimostrazione: per calcolare la chiave privata $d$, devi usare l'Algoritmo di Euclide su $e$ e **sull'ordine del gruppo $n$**. 
Se l'hacker (che conosce il gruppo pubblico e la chiave $e$) riesce a scoprire quant'è grande questo ordine $n$, prende la sua calcolatrice, lancia l'Algoritmo di Euclide, si calcola da solo la chiave segreta $d$, e ti svuota il conto in banca.
Quindi, cos'è davvero RSA? È la ricerca spasmodica di un gruppo matematico in cui calcolare l'esponente $e$ sia facilissimo, ma in cui **scoprire il valore di $n$ (l'ordine del gruppo) sia impossibile per chi non possiede la "botola"**.

---

In queste tre pagine, ci mostra esattamente cosa succede se scegliamo il gruppo sbagliato (un disastro totale) e poi ci svela finalmente il trucco per far funzionare RSA. 

Smontiamo questo clamoroso fallimento didattico per capire il trionfo successivo.****
### 17. Il Falso RSA (Example 4.4 - Pohlig-Hellman)

**Testo Originale:**
> **Example 4.4 (Pohlig-Hellman exponentiation cipher)**
> [yet another non-example of public-key cryptosystem]
> Idea: take power monomials as encryption-decryption functions on $(\mathbb{Z}_p^*, \cdot)$, for $p \in \mathbb{P}$.
> Message space = ciphertext space = $\mathbb{Z}_p^*$
> Key space = $(\mathbb{Z}_{p-1}, +)$
> $Gen(N) \mapsto p \in \mathbb{P}, (e,d) \in K \times K \text{ s.t. } \gcd(e, p-1)=1 \text{ and } ed = 1 \bmod p-1$
> $Enc(e) \mapsto E_e: \mathbb{Z}_p^* \to \mathbb{Z}_p^*, x \mapsto x^e$
> $Dec(d) \mapsto D_d: \mathbb{Z}_p^* \to \mathbb{Z}_p^*, x \mapsto x^d$
> Is property 4 of Definition 4.1 satisfied? (is encryption bijective?)
> YES! Since $\gcd(e, \#G = p-1) = 1$, we have by Proposition 4.3 $\forall x \in \mathbb{Z}_p^* \quad D_d(E_e(x)) = (x^e)^d = x^{ed} = x^{1+k(p-1)} = x$.

**Cosa dice:**
Il professore prova a costruire un sistema a chiave pubblica usando le potenze (come avevamo detto), scegliendo come campo di battaglia il gruppo $\mathbb{Z}_p^*$ (dove $p$ è un numero primo). 
Costruisce le chiavi esattamente come ci si aspetta: sceglie la chiave pubblica $e$, calcola la grandezza degli ingranaggi (che in un gruppo primo è $p-1$), e trova l'inverso $d$. 
La cifratura ($x^e$) e la decifratura ($x^d$) funzionano a meraviglia. Il messaggio torna in chiaro, quindi la Regola 4 (Reversibilità) è perfettamente superata.

**Spiegazione dettagliata:**
Sulla carta sembra tutto bellissimo. Alice ha il suo lucchetto pubblico ($e$) e la sua botola segreta ($d$). Bob le manda un messaggio criptato, lei lo decripta, tutto fila liscio come l'olio. È il sistema perfetto, no? 
Il professore lo chiama "Pohlig-Hellman exponentiation cipher" e avverte subito tra parentesi quadre: questo è un *non-esempio*. È una trappola mortale.

---

### 18. Il Furto della "Botola" (Perché il sistema crolla)

**Testo Originale:**
> Is property 5 of Definition 4.1 satisfied? (is $(Gen, Enc, Dec)$ secure against a passive eavesdropper?)
> NO!
> *(...)*
> The adversary $\mathcal{A}$ can win the game with probability 1 and in linear time proceeding as follows:
> 1) computes $xgcd(e, p-1) = 1$
> 2) determines $d$, the inverse of $e \bmod p-1$
> 3) computes $x^* := c^d$.
> We have $(x^*)^e = c^{de} = c$, therefore $\mathcal{A}$ wins the game.
> The Pohlig-Hellman exponentiation cipher is not a public-key cryptosystem (indeed it was a private-key cryptosystem). It is important to understand why: because the knowledge of $e$ (which is public) **implies** the knowledge of $d$ (which should have been private).
> The problem is in the group where all this is set: $\mathbb{Z}_p^*$. Here, the group and its order are information efficiently computable the one from the other.

**Cosa dice:**
La Regola 5 (Sicurezza) fallisce miseramente. L'hacker vince sempre, e per di più ci mette una frazione di secondo (tempo lineare).
Perché? Perché l'hacker conosce i dati pubblici: il modulo $p$ e l'esponente $e$. 
Ma in matematica, se conosci un numero primo $p$, conosci automaticamente la grandezza del suo gruppo: è semplicemente $p-1$. 
L'hacker prende la sua calcolatrice, fa "meno uno" al modulo pubblico, lancia l'Algoritmo di Euclide Esteso (xgcd) su $e$ e $p-1$, e si calcola da solo la tua chiave privata $d$. Tu avevi nascosto la chiave sotto lo zerbino, e lo zerbino era trasparente!

**Spiegazione dettagliata:**
Questo foglio spiega l'errore concettuale più clamoroso della crittografia: **confondere il lucchetto con la cassaforte**. 
La formula matematica $x^e$ è ottima, ma il "luogo" dove la applichiamo ($\mathbb{Z}_p^*$) è un colabrodo. Per calcolare l'inverso $d$ serve conoscere l'ordine del gruppo. Se chiunque può calcolare l'ordine del gruppo, chiunque può calcolare $d$. La "botola" è alla mercé di tutti.

---

### 19. La Nascita di RSA (Il Gruppo Segreto)

**Testo Originale:**
> Is there a group where
> knowing the group $\not\Rightarrow$ (efficiently) knowing the order of the group?
> If this is the case
> 1) compute $xgcd(e, \#G) = 1$ is **not doable** since $\#G$ is not known;
> 2) determine $d$, the inverse of $e \bmod \#G$ is **not doable** for the same reason.
> We already know a group where this happens. Indeed, (see also Remark 2.43), if $G = \mathbb{Z}_n^*$ for $n$ a **composite integer** and we **assume that factorizing $n$ is hard**, then
> Knowing $G = \mathbb{Z}_n^* \not\Rightarrow$ knowing $\#\mathbb{Z}_n^* = \varphi(n)$.

**Cosa dice:**
Il professore tira fuori il coniglio dal cilindro. Ci serve un gruppo matematico magico dove: tu mi dici come si chiama il gruppo, ma se io ti chiedo "Quanti elementi ci sono dentro?", tu non lo puoi sapere a meno di non sbatterci la testa per milioni di anni.
E noi questo gruppo lo conosciamo già! È il gruppo $\mathbb{Z}_n^*$, dove il modulo $n$ **non è un numero primo**, ma è un numero "composto" (ovvero la moltiplicazione di due numeri primi giganti). 
In questo gruppo, la grandezza totale si calcola con la Funzione Totiente di Eulero, la famosa **$\varphi(n)$**.
Ma per calcolare $\varphi(n)$, devi prima sapere quali sono i due numeri primi originali che compongono $n$. E siccome fattorizzare (smontare) un numero gigante è un problema matematico attualmente irrisolvibile per i computer (Hard), l'hacker non potrà MAI scoprire l'ordine del gruppo. E se non scopre l'ordine, non può lanciare l'algoritmo di Euclide per rubarti la chiave $d$.

**Spiegazione dettagliata:**
*BOOM.* Hai appena assistito alla scintilla di genialità che ha creato **RSA**. 
Alice prende due numeri primi enormi ($p$ e $q$) nel segreto della sua stanza. Li moltiplica ($n = p \cdot q$). Calcola da sola la grandezza del gruppo ($\varphi(n)$) usando la formula segreta di Eulero. Si crea le sue chiavi $e$ e $d$. 
Poi, urla al mondo solo due cose: il prodotto $n$ e l'esponente pubblico $e$. 
L'hacker riceve $n$ ed $e$. Vede $n$, sa che è un numero composto, ma per rubare la chiave $d$ ha bisogno di $\varphi(n)$. Per trovare $\varphi(n)$ cerca di smontare $n$ in $p$ e $q$, ma il suo computer si impalla perché il numero è troppo lungo. 

Il sistema è blindato. La crittografia moderna è servita. 

---
In questa pagina, il professore applica la formula magica dei lucchetti (la Proposizione 4.3) al gruppo segreto che abbiamo appena scoperto, ma subito dopo si accorge di un nuovo fastidiosissimo ostacolo pratico.

Smontiamo tutto!
### 20. La Nascita Ufficiale delle Chiavi RSA (Corollary 4.5)

**Testo Originale:**

> **Corollary 4.5**
> 
> Let $n > 1$ and let $2 < e < \varphi(n)$ such that $\gcd(e, \varphi(n)) = 1$.
> 
> _(Nota in alto: $\varphi(n)$ is even, so $e \neq 2$)_
> 
> The function $f: \mathbb{Z}_n^* \to \mathbb{Z}_n^*, x \mapsto x^e$ is bijective.
> 
> Moreover, if $d$ is such that $ed = 1 \bmod \varphi(n)$ ($d$ is the inverse of $e$ in $\mathbb{Z}_{\varphi(n)}^*$), then
> 
> $h: \mathbb{Z}_n^* \to \mathbb{Z}_n^*, x \mapsto x^d$ is the inverse of $f$.
> 
> **Proof**
> 
> It is Proposition 4.3 applied to $G = \mathbb{Z}_n^*$. $\blacksquare$

**Cosa dice:**

Il Corollario 4.5 prende il Teorema della pagina precedente e lo cala nel nostro gruppo speciale $\mathbb{Z}_n^*$.

Ci dice che l'ordine di questo gruppo si chiama **$\varphi(n)$** (la Funzione di Eulero).

Quindi, la regola d'oro diventa: scegli un esponente $e$ che non abbia divisori in comune con $\varphi(n)$. Poiché $\varphi(n)$ dà sempre un risultato pari, il professore nota che non potrai mai usare $e=2$ (altrimenti avrebbero in comune il 2, e il GCD non sarebbe 1). Devi usare un $e$ strettamente maggiore di 2.

Se fai questo, la tua chiave privata $d$ sarà l'inverso di $e$ calcolato col modulo $\varphi(n)$.

**Spiegazione dettagliata:**

Questa è la ricetta esatta che usano i computer oggi per generare le chiavi RSA.

Guarda bene l'equazione per calcolare $d$: **$ed = 1 \bmod \varphi(n)$**.

L'hacker conosce $e$. Per trovare $d$, gli basterebbe lanciare l'Algoritmo di Euclide Esteso. Ma per lanciarlo, il computer gli chiede il modulo: _"Inserire $\varphi(n)$"_. E l'hacker si blocca. Come avevamo visto prima, per sapere $\varphi(n)$ devi scomporre $n$ nei suoi fattori primi originali, un compito che distruggerebbe qualsiasi computer. La cassaforte è ufficialmente blindata.
##### <font color="#c00000">Flusso per capire meglio </font>
###### 1. Il Mondo dei Messaggi (Base $m$, Modulo $n$)

Qui il protagonista è il messaggio $m$. Il suo "recinto" è $n = p \cdot q$.

- **L'Asterisco ($\mathbb{Z}_n^*$):** Per la teoria classica (Teorema di Eulero), dobbiamo inizialmente supporre che $m$ sia coprimo con $n$ ($gcd(m, n) = 1$). Questo significa scartare lo zero e i multipli di $p$ e $q$.
    
- **La Proprietà di Ritorno:** Solo se $m$ è in questo "club" dei coprimi, la matematica ci garantisce che elevandolo alla potenza $\phi(n)$ otteniamo 1:
    
    $$m^{\phi(n)} \equiv 1 \pmod n$$
    
- **Perché ci serve l'1?** Perché vogliamo che la decifratura "pulisca" il messaggio. Se l'esponente totale è un giro completo più un passo ($ed = 1 + k\phi(n)$), allora:
    
    $$m^{ed} = m^{1} \cdot (m^{\phi(n)})^k = m \cdot (1)^k = m$$
    

---

###### 2. Il Mondo della Regia (Esponenti $e$ e $d$, Modulo $\phi(n)$)

Qui decidiamo come muovere il messaggio. Il "recinto" non è più $n$, ma $\phi(n) = (p-1)(q-1)$.

- **L'Orologio degli Esponenti:** Le potenze non vivono nel modulo $n$, ma seguono il ritmo di $\phi(n)$. È un orologio che ha esattamente $\phi(n)$ tacche.
    
- **La Regola del GCD:** Per poter tornare indietro dal messaggio cifrato, dobbiamo trovare una chiave privata $d$ che sia l'inverso di $e$.
    
- **Il Vincolo:** La matematica dice che $d$ esiste **solo se** $gcd(e, \phi(n)) = 1$. Se non fossero coprimi, rimarresti bloccato su certi valori e non riusciresti mai a far fare al messaggio un giro completo per tornare all'ora "1".
    

---

###### 3. La Sintesi del Flusso (Il tuo ragionamento)

Hai ragione su tutta la linea:

1. **Scegliamo $n = p \cdot q$** per nascondere la botola ($\phi(n)$).
    
2. **Imponiamo $gcd(e, \phi(n)) = 1$** per essere certi che esista la chiave privata $d$.
    
3. **Cifriamo $m^e$ e decifriamo $(m^e)^d$**.
    
4. Il meccanismo funziona perché $ed$ è costruito per essere **"un passo dopo il giro completo"** ($1 \pmod{\phi(n)}$).
    
5. Il messaggio torna $m$ perché quel "giro completo" nell'esponente si trasforma in un "1" nella base, grazie a Eulero.
    

---

###### 4. Il "Bonus" Ingegneristico (Il Teorema 4.10)

C'è solo un piccolo dettaglio che l'ingegneria ha aggiunto alla teoria: anche se il Teorema di Eulero ti direbbe di evitare i multipli di $p$ e $q$, la dimostrazione del **Teorema 4.10** ci dice che RSA è "più intelligente".

Anche se prendi un messaggio $m$ che **non** è coprimo con $n$ (un multiplo di $p$ o $q$), la decifratura **funziona lo stesso**. La biunivocità si estende a tutto $\mathbb{Z}_n$. Quindi, alla fine, puoi buttare dentro RSA qualsiasi numero senza preoccuparti di nulla!

##### <p align="right"><font color="#2DC26B">1. Perché la funzione Totiente di Eulero è sempre pari e perché non possiamo usare e=2?</font></p>

Per capire perché $\varphi(n)$ è sempre pari, dobbiamo guardare come è fatto $n$. In RSA, $n = p \times q$, dove $p$ e $q$ sono due numeri **primi** (e solitamente molto grandi).

- **La formula:** $\varphi(n) = (p-1)(q-1)$.
    
- **I numeri primi:** Tranne il numero $2$, tutti i numeri primi sono **dispari** ($3, 5, 7, 11, \dots$).
    
- **La sottrazione:** Se prendi un numero dispari (come $p$ o $q$) e gli sottrai $1$, ottieni sempre un numero **pari**.
    
- **Il risultato:** Quindi $\varphi(n)$ è il prodotto di due numeri pari: $(\text{pari}) \times (\text{pari}) = \text{sempre pari}$.
    

**Il problema con $e=2$:**

La regola d'oro di RSA è che $\gcd(e, \varphi(n)) = 1$ (devono essere coprimi).

Se $\varphi(n)$ è pari, significa che è divisibile per $2$. Se scegliamo $e=2$, entrambi i numeri sono divisibili per $2$. Di conseguenza, il loro Massimo Comun Divisore sarà (almeno) $2$, non $1$.

> **In breve:** Se il GCD non è $1$, non esiste l'inverso modulare $d$. Senza $d$, non puoi creare la chiave privata e non puoi decifrare nulla. Il lucchetto si chiuderebbe ma non avresti la chiave per riaprirlo.


---

##### <p align="right"><font color="#2DC26B">1. Il Livello del Palco: Il Messaggio ($m$)</font></p>

Qui l'operazione è $m^e \pmod n$. Il "recinto" è $n = p \cdot q$.

- **Perché l'asterisco $\mathbb{Z}_n^*$ (i non-multipli di $p$ e $q$)?**
    
    La teoria classica (Eulero) dice che per far tornare indietro il messaggio, dobbiamo passare per la formula $m^{\phi(n)} \equiv 1 \pmod n$. Questa formula **funziona matematicamente solo se** $m$ e $n$ non hanno nulla in comune ($gcd=1$).
    
- **Chi sono i "nemici"?** Solo lo zero e i multipli di $p$ e $q$.
    
- **E i numeri pari?** Se $n=15$ ($3 \times 5$), il numero **2** è pari ma è "amico" di 15 ($gcd(2,15)=1$). Quindi i messaggi pari vanno benissimo e stanno nell'asterisco!
    
- **La svolta:** Come abbiamo detto, l'ingegneria di RSA scopre che, grazie a un trucco avanzato (Teorema Cinese del Resto), anche se scegli un multiplo di $p$ o $q$, il messaggio torna indietro lo stesso. Ma per la teoria base, l'asterisco serviva a garantire che la formula di Eulero non "esplodesse".
    

---

##### <p align="right"><font color="#2DC26B">2. Il Livello della Regia: L'Esponente ($e$)</font></p>

Qui l'operazione è trovare $d$ tale che $e \cdot d \equiv 1 \pmod{\phi(n)}$. Il "recinto" è $\phi(n) = (p-1)(q-1)$.

- **Perché $e$ deve essere coprimo con $\phi(n)$?**
    
    Questa è una regola di ferro della **logica delle inversioni**. Per poter calcolare la chiave privata $d$, devi poter "dividere" per $e$ nel mondo modulare.
    
    La matematica dice: puoi trovare l'inverso di $e$ **solo e soltanto se** $gcd(e, \text{modulo}) = 1$.
    
- **Il problema dei Pari qui:** Poiché $\phi(n)$ è sempre un numero pari, se scegli un $e$ pari (2, 4, 10...), il $gcd$ sarà almeno 2.
    
- **Risultato:** Se $e$ non è coprimo con $\phi(n)$, **la chiave privata $d$ non esiste proprio**. Non è che il sistema è debole; è che non puoi proprio costruirlo. Il lucchetto non avrebbe una chiave corrispondente.
    

---

##### <p align="right"><font color="#2DC26B">3. Tabella Riassuntiva: Mettiamo i tasselli al loro posto</font></p>

| **Livello**   | **Protagonista** | **Modulo Usato**  | **Regola d'oro**               | **Perché?**                                                     |
| ------------- | ---------------- | ----------------- | ------------------------------ | --------------------------------------------------------------- |
| **Messaggio** | $m$              | $n$ ($p \cdot q$) | $gcd(m, n) = 1$ (Teoria)       | Per far funzionare il Teorema di Eulero $m^{\phi(n)} \equiv 1$. |
| **Esponente** | $e$              | $\phi(n)$         | $gcd(e, \phi(n)) = 1$ (Sempre) | Per garantire che esista matematicamente la chiave privata $d$. |


1. **Sui messaggi ($m$):** I multipli di $p$ e $q$ sono un problema (teorico) perché "rompono" la simmetria del cerchio di Eulero. I numeri pari **non** sono un problema.
    
2. **Sull'esponente ($e$):** Qui essere coprimo è vitale. E siccome il modulo $\phi(n)$ è pari, **tutti i numeri pari sono esclusi** perché avrebbero il 2 in comune con il modulo, impedendo la creazione di $d$.
    


---

##### <p align="right"><font color="#2DC26B">4. La Dimostrazione (L'intuizione del "Set di Amici")</font></p>

Immagina l'insieme di tutti i numeri tra $1$ e $n$ che sono coprimi con $n$ (quelli dell'asterisco $\mathbb{Z}_n^*$). Chiamiamo questo insieme $S$:

$$S = \{a_1, a_2, a_3, \dots, a_{\phi(n)}\}$$

In totale ci sono esattamente $\phi(n)$ numeri.

Ora, prendi un messaggio $m$ (anche lui coprimo con $n$) e moltiplicalo per ogni numero di $S$:

$$S' = \{m \cdot a_1, m \cdot a_2, m \cdot a_3, \dots, m \cdot a_{\phi(n)}\}$$

**Il trucco:** Poiché $m$ è coprimo con $n$, moltiplicare per $m \pmod n$ è come rimescolare le carte. Gli elementi di $S'$ sono esattamente gli stessi di $S$, solo in un ordine diverso.

Se moltiplichiamo tra loro tutti gli elementi di $S$ e tutti quelli di $S'$, i risultati devono essere uguali modulo $n$:

$$(m \cdot a_1) \cdot (m \cdot a_2) \cdots (m \cdot a_{\phi(n)}) \equiv a_1 \cdot a_2 \cdots a_{\phi(n)} \pmod n$$

Ora usiamo l'algebra:

1. A sinistra, abbiamo moltiplicato $m$ per se stesso $\phi(n)$ volte:
    
    $$m^{\phi(n)} \cdot (a_1 \cdot a_2 \cdots a_{\phi(n)}) \equiv a_1 \cdot a_2 \cdots a_{\phi(n)} \pmod n$$
    
2. Poiché tutti i numeri $a_i$ sono coprimi con $n$, possiamo "cancellarli" da entrambi i lati (tecnicamente, moltiplichiamo per i loro inversi).
    
3. Cosa resta?
    
    $$m^{\phi(n)} \equiv 1 \pmod n$$
    

---

##### <p align="right"><font color="#2DC26B">5. Perché non basta m^n= m?</font></p>

Se usassimo il modulo $n$ invece di $\phi(n)$ nell'esponente, il sistema fallirebbe perché:

- In un modulo composto ($n = p \cdot q$), la sequenza delle potenze di $m$ non si ripete ogni $n$ passi, ma segue il ritmo dei suoi fattori primi.
    
- Il ritmo "armonico" che riporta tutto a 1 è dettato dal numero di elementi che non hanno fattori in comune con $n$, che è appunto $\phi(n)$.
---



##### <p align="right"><font color="#2DC26B">6. Il conteggio dei "nemici"</font></p>

Immaginiamo il nostro modulo $n = p \cdot q$. Per calcolare $\phi(n)$, dobbiamo prendere tutti i numeri da $0$ a $n-1$ e togliere quelli che non sono coprimi.

- **I multipli di $p$:** Sono $\{p, 2p, 3p, \dots, q \cdot p\}$. In totale sono **$q$** numeri.
    
- **I multipli di $q$:** Sono $\{q, 2q, 3q, \dots, p \cdot q\}$. In totale sono **$p$** numeri.
    
- **Il numero $n$ (o lo 0):** È un multiplo di entrambi, quindi lo abbiamo contato due volte. Dobbiamo aggiungerne **1** per correggere l'errore.
    

##### <p align="right"><font color="#2DC26B">7. La formula della sottrazione</font></p>

Se scrivi questa operazione in riga, ottieni:

$$\phi(n) = n - p - q + 1$$

Ora, guarda la magia dell'algebra. Se sostituiamo $n$ con $p \cdot q$:

$$\phi(n) = pq - p - q + 1$$

E se proviamo a raggruppare i termini (come facevi a scuola):

$$\phi(n) = p(q - 1) - 1(q - 1)$$

$$\phi(n) = (p - 1)(q - 1)$$

**Ecco svelato il mistero!** La formula $(p-1)(q-1)$ che usa il professore non è altro che il risultato finale della tua idea: prendere il totale e levare tutti i multipli di $p$ e di $q$.
### 21. L'Ultimo Fastidioso Ostacolo (Group Membership)

**Testo Originale:**

> We want to apply Pohlig-Hellman's idea to $\mathbb{Z}_n^*$ but we have another issue to solve: **group membership**, which means checking if $m \in \mathbb{Z}_n^*$ before encryption.
> 
> _(Diagramma)_
> 
> message $m \to$ $m \in \mathbb{Z}_n^*$ ? YES $\to$ can encrypt it
> 
> message $m \to$ $m \notin \mathbb{Z}_n^*$ ? NO $\to$ cannot encrypt it

**Cosa dice:**

Tutto il castello matematico che abbiamo costruito regge su una condizione fondamentale: le funzioni $x^e$ e $x^d$ si comportano bene _solo se_ il messaggio $m$ appartiene al gruppo $\mathbb{Z}_n^*$.

Cosa significa "appartenere a quel gruppo"? Significa che il messaggio $m$ non deve avere alcun divisore in comune col modulo $n$ (ovvero $\gcd(m, n) = 1$).

Se cerchi di criptare un messaggio "sbagliato" (che condivide un fattore con $n$), la matematica si rompe e il messaggio va perso per sempre. Quindi, per sicurezza, il computer dovrebbe fermarsi prima di ogni singola cifratura e controllare se il messaggio è valido.

**Spiegazione dettagliata:**

Questa è la differenza tra un teorema matematico perfetto e un software del mondo reale. Immagina di usare WhatsApp e, prima di premere "Invia", il telefono deve fare un calcolo lunghissimo per controllare se la tua frase "Ciao come stai?" rispetta la regola del Massimo Comune Divisore col server di Mark Zuckerberg. È un incubo ingegneristico! Se per sfortuna becchi una frase "non compatibile", il telefono ti darebbe "Errore di cifratura". Un sistema crittografico serio deve poter cifrare _qualsiasi_ pacchetto dati, senza fare domande.

#### <p align="right"><font color="#2DC26B">2. Perché il messaggio m deve avere gcd(m, n) = 1?</font></p>

Questa è la parte più sottile. Hai ragione a pensare: _"Ma io voglio mandare qualsiasi messaggio, mica solo quelli che piacciono alla matematica!"_.

**Il motivo teorico (il "perché" del professore):**

Il Corollario 4.5 si basa sulla **Teoria dei Gruppi**. La funzione di cifratura $x^e$ è dimostrata essere biunivoca (ovvero che a ogni messaggio corrisponde una e una sola versione cifrata e viceversa) solo per gli elementi che appartengono al gruppo $\mathbb{Z}_n^*$.

Gli elementi di questo gruppo sono, per definizione, solo quelli che non hanno fattori in comune con $n$ (ovvero $\gcd(m, n) = 1$). Se $m$ non è nel gruppo, le proprietà matematiche usate nella dimostrazione (come il Teorema di Eulero) tecnicamente non si applicano.

**Il motivo pratico (la realtà dei fatti):**

Nella pratica, se tu provassi a cifrare un messaggio $m$ che ha un fattore in comune con $n$, succederebbe una cosa incredibile: **avresti appena rotto la chiave RSA senza volerlo.**

Ecco perché:

1. Sappiamo che $n = p \times q$.
    
2. Se $\gcd(m, n) \neq 1$, significa che il messaggio $m$ è un multiplo di $p$ o di $q$.
    
3. Se tu (o un hacker) trovi un numero $m$ che ha un divisore in comune con $n$, ti basta calcolare il $\gcd(m, n)$ per trovare immediatamente $p$ o $q$.
    
4. Trovato $p$, calcoli $\varphi(n)$, trovi la chiave privata $d$ e il sistema è distrutto.
    

**Quindi, devo preoccuparmi?**

No, ed ecco il trucco: i numeri $p$ e $q$ sono talmente giganteschi che la probabilità di scegliere "per sbaglio" un messaggio $m$ che sia un loro multiplo è praticamente **zero**. È più probabile che un asteroide colpisca il tuo computer esattamente mentre premi "Invio" piuttosto che tu scelga un $m$ non coprimo con $n$.

##### 1. I "Mattoni" di $n$

Sappiamo che $n = p \times q$. Poiché $p$ e $q$ sono numeri primi, i **divisori** di $n$ sono soltanto quattro:

1. **$1$** (che divide tutto).
    
2. **$p$**
    
3. **$q$**
    
4. **$n$** (ovvero $p \times q$).
    

##### 2. Se $\gcd(m, n) \neq 1$, cosa succede?

Se il Massimo Comun Divisore tra il tuo messaggio $m$ e il modulo $n$ non è $1$, significa che $m$ e $n$ condividono un divisore che non è $1$. Guardando la lista qui sopra, le uniche possibilità sono che il divisore comune sia **$p$** oppure **$q$**.

Quindi, come dici tu, $m$ deve essere un **multiplo** di uno dei due:

- $m = p, 2p, 3p, \dots$
    
- $m = q, 2q, 3q, \dots$
    

##### 3. Perché questo è un "disastro" per la sicurezza?

Se tu riuscissi a trovare un messaggio $m$ che ha un divisore in comune con $n$, avresti vinto tutto.

Perché? Perché calcolare il $\gcd(m, n)$ è un'operazione che i computer fanno in un millisecondo (usando l'algoritmo di Euclide).

- Se calcoli $\gcd(m, n)$ e il risultato è $p$, allora hai appena **scomposto $n$**.
    
- Una volta che hai $p$, trovi subito $q$ (facendo $n / p$).
    
- A quel punto calcoli $\varphi(n) = (p-1)(q-1)$.
    
- E infine trovi la chiave privata $d$.
    

**La cassaforte è aperta.**

---


### 22. Il Cliffhanger Finale

**Testo Originale:**

> Can we build a public-key cryptosystem which works in $M = \mathbb{Z}_n$ instead of $\mathbb{Z}_n^*$?

**Cosa dice:**

Il professore chiude la pagina con la domanda da un milione di dollari: possiamo sbarazzarci di quel fastidiosissimo asterisco (*)?

Possiamo costruire un sistema che prenda l'intero set di numeri da $0$ a $n-1$ (cioè $\mathbb{Z}_n$ intero), ignorando la regola dei coprimi, e garantendo comunque che il messaggio non si distrugga durante la cifratura?

**Spiegazione dettagliata:**

Questa è la mossa finale. Togliere quell'asterisco significa togliere il controllo d'ingresso. Significa poter buttare dentro la cassaforte qualsiasi file, qualsiasi numero, qualsiasi stringa di testo senza controllarla prima, sapendo con certezza assoluta che la decifratura ci restituirà esattamente il file originale.

La risposta è RSA nella sua forma definitiva, basata su una fantastica generalizzazione del Teorema di Eulero. 

##### <p align="right"><font color="#2DC26B">1. Cosa significa l'asterisco *</font></p>

In termini tecnici, $\mathbb{Z}_n$ è l'insieme di **tutti** i numeri da $0$ a $n-1$. Invece, $\mathbb{Z}_n^*$ è il **Gruppo Moltiplicativo**, ovvero solo il sottoinsieme di quei numeri che sono **coprimi** con $n$ (quelli il cui $gcd(x, n) = 1$).

L'asterisco implica tre grandi limitazioni:

1. **Esclusione dello Zero:** Lo zero non è mai coprimo con nessuno, quindi $0 \notin \mathbb{Z}_n^*$.
    
2. **Esclusione dei Fattori:** Se $n = p \times q$, tutti i multipli di $p$ (come $p, 2p, 3p...$) e tutti i multipli di $q$ (come $q, 2q, 3q...$) sono lasciati fuori dal club.
    
3. **Controllo all'Ingresso:** Prima di cifrare un messaggio $x$, in teoria dovresti controllare se $gcd(x, n) = 1$. Se non lo fosse, la matematica "standard" di Eulero non ti garantirebbe più che il messaggio torni indietro integro.


---

Qui la teoria diventa ingegneria. Smontiamo la "fabbrica" di RSA pezzo per pezzo.

### 23. La Fabbrica del Lucchetto (Definition 4.7 e 4.8)

**Testo Originale:**

> **Definition 4.7 (GenModulus)**
> 
> `GenModulus` is a PPT algorithm which takes as input a security parameter $N$ and returns a triple $(n, p, q)$ where
> 
> 1. $p$ and $q$ are distinct primes of $N/2$ bits each
>     
> 2. $n = p \cdot q$.
>     
> 
> **Definition 4.8 (GenRSA)**
> 
> `GenRSA` is a PPT algorithm which takes in input a security parameter $N$ and returns a modulus $n$ [...] and $1 < e, d < \varphi(n)$ such that $ed = 1 \bmod \varphi(n)$.

**Cosa dice:**

Il professore definisce i due "macchinari" informatici che creano le chiavi.

Il primo macchinario (`GenModulus`) prende in input la sicurezza desiderata (es. $N = 2048$ bit) e genera due numeri primi giganteschi a caso, $p$ e $q$, lunghi esattamente la metà ($1024$ bit). Poi li moltiplica per creare il super-modulo $n$.

Il secondo macchinario (`GenRSA`) fa il lavoro matematico completo in 5 step:

1. Chiama `GenModulus` per avere $(n, p, q)$.
    
2. Calcola $\varphi(n)$ usando la formula magica che solo chi conosce i fattori può usare: **$(p-1) \cdot (q-1)$**.
    
3. Sceglie un esponente pubblico $e$ a caso (coprimo con $\varphi(n)$).
    
4. Calcola l'inverso segreto $d$ in modo che $e \cdot d \equiv 1 \pmod{\varphi(n)}$.
    
5. Sputa fuori i tre numeri fondamentali: $(n, e, d)$.
    

**Spiegazione dettagliata:**

Fermati un attimo sul **Punto 2** di `GenRSA`. Questa è la **Botola** (Trapdoor) di cui parlavamo nelle lezioni scorse!

Per calcolare la grandezza dell'ingranaggio degli esponenti ($\varphi(n)$), l'unico modo veloce è moltiplicare $(p-1)$ per $(q-1)$.

Alice, che ha appena generato $p$ e $q$, fa questa operazione in un nanosecondo. Crea la chiave privata $d$, e poi distrugge per sempre $p$ e $q$.

L'hacker riceverà solo $n$ ed $e$. Per trovare $d$, deve trovare $\varphi(n)$. Ma non avendo $p$ e $q$, l'unico modo che ha è prendere l'enorme numero $n$ e cercare di "smontarlo" a forza bruta nei suoi fattori originali. Con chiavi di 2048 bit, il Sole si spegnerà prima che il computer dell'hacker abbia finito.

---

### 24. Il Crittosistema RSA Ufficiale (Definition 4.9)

**Testo Originale:**

> **Definition 4.9 (RSA public-key cryptosystem)**
> 
> Let us define the following triple of algorithms:
> 
> - **Gen:** on input a security parameter $N$, runs `GenRSA(N)` and obtains $(n, p, q)$. Defines $M = C = \mathbb{Z}_n$, $K = \mathbb{Z}_{\varphi(n)}$ and outputs
>     
>     - the **public key** is $(e, n)$
>         
>     - the **private key** is $d$.
>         
> - **Enc:** on input the public key $e$ and a message $x \in M$ it operates as the (encryption) function
>     
>     $E_{pk}: \mathbb{Z}_n \to \mathbb{Z}_n, \quad x \mapsto x^e \bmod n$
>     
> - **Dec:** on input the private key $d$ and a message $x \in M$ it operates as the (decryption) function
>     
>     $D_{sk}: \mathbb{Z}_n \to \mathbb{Z}_n, \quad x \mapsto x^d \bmod n$
>     

**Cosa dice:**

Questa è l'architettura finale, pronta per essere programmata in C++ o Python.

L'algoritmo `Gen` impacchetta la **chiave pubblica** che darai ai tuoi amici: è una coppia di numeri $(e, n)$. La tua **chiave privata** è il solo numero $d$.

Cifrare è banale: prendi il messaggio $x$ ed elevalo alla $e$ modulo $n$.

Decifrare è identico: prendi il messaggio cifrato ed elevalo alla $d$ modulo $n$.

**Spiegazione dettagliata:**

Guarda la bellezza simmetrica di queste funzioni: **$x^e$** chiude, **$x^d$** apre. Tutto calcolato nel recinto del modulo pubblico $n$.

È un sistema così semplice che si può scrivere in tre righe di codice, eppure la sua sicurezza matematica è titanica. La chiave pubblica non è solo l'esponente $e$, ma _deve_ includere anche il modulo $n$, altrimenti chi spedisce il messaggio non saprebbe in che "recinto" fare l'operazione di modulo!

---

### 25. Il Miracolo dell'Asterisco Scomparso

**Testo Originale:**

> The idea of RSA encryption is that if $m \in \mathbb{Z}_n^*$ (but soon it will be shown to be valid for $m \in \mathbb{Z}_n$)
> 
> $m = m^{ed \bmod \#\mathbb{Z}_n^*} \text{ in } \mathbb{Z}_n^*$
> 
> _(...)_ Defines $M = C = \mathbb{Z}_n$

**Cosa dice:**

Il professore ci fa notare un dettaglio clamoroso. Nella Definizione 4.9, lo Spazio dei Messaggi ($M$) e lo Spazio dei Testi Cifrati ($C$) sono definiti come **$\mathbb{Z}_n$**. Non c'è l'asterisco!

Ricordi il "fastidioso ostacolo" della lezione precedente? Avevamo paura di dover controllare ogni singolo messaggio per assicurarci che non avesse divisori in comune con $n$.

RSA se ne frega. Prende l'intero spazio di tutti i numeri possibili da $0$ a $n-1$, senza eccezioni.

**Spiegazione dettagliata:**

L'ultima riga tagliata in fondo alla tua seconda foto dice: _"We must prove that (Gen, Enc, Dec) [...] is a public-key cryptosystem"_.

Il prof sta preparando il terreno per la dimostrazione più epica del corso. Dovrà dimostrarci matematicamente perché RSA funziona perfettamente anche se, per puro caso, cerchiamo di criptare un "messaggio proibito" (un multiplo di $p$ o di $q$).

È la vittoria definitiva della teoria dei numeri applicata all'informatica. Manda pure la prossima pagina con la dimostrazione, sono carico!

---

Dobbiamo dimostrare che RSA funziona per **qualsiasi** messaggio, anche quelli "proibiti" che condividono divisori con il modulo $n$. 

Smontiamo questa dimostrazione epica (Theorem 4.10) passo dopo passo!


### 26. L'Elefante nella Stanza (La zona sicura vs La zona di pericolo)

**Testo Originale:**
> **Theorem 4.10 (Correctness of RSA)**
> The algorithms of Definition 4.9 satisfy reversibility, that is the condition 4 of Definition 4.1.
> We have $\gcd(e, \varphi(n)) = 1$ and that $d$ satisfies $e \cdot d = 1 + k\varphi(n)$.
> $\forall m \in \mathbb{Z}_n^* \quad (m^e)^d = m^{1+k\varphi(n)} = m \quad \text{(Euler-Fermat's)}$
> $\to$ we must prove $m^{ed} = m \text{ even if } m \in \mathbb{Z}_n \setminus \mathbb{Z}_n^*$.

**Cosa dice:**
Il prof imposta l'obiettivo. Sappiamo che la chiave pubblica $e$ e la privata $d$ moltiplicate insieme danno $1 + k\varphi(n)$. 
Se il messaggio $m$ sta nella "zona sicura" (l'anello interno del disegno, $\mathbb{Z}_n^*$), il Teorema di Eulero-Fermat ci garantisce gratis che la decifratura funziona: le potenze si annullano e torna $m$.
Ma il nostro software RSA accetta *tutti* i messaggi, anche quelli della "zona di pericolo" ($\mathbb{Z}_n \setminus \mathbb{Z}_n^*$), cioè i multipli segreti di $p$ o di $q$. Dobbiamo dimostrare che la formula $(m^e)^d \equiv m \pmod n$ sopravvive anche lì.

---

### 27. Dividi et Impera (Il Trucco del Modulo $p$)

**Testo Originale:**
> **Proof**
> Let $(n, e, d)$ be the output of GenRSA(N) [...] $n=p \cdot q$, where $p,q \in \mathbb{P}$ are distinct, [...] and $de = 1 + k\varphi(n)$.
> We need to prove that for each $m \in M = \mathbb{Z}_n$ we have $D_{sk}(E_{pk}(m)) = m$, which means that $\forall m \in \mathbb{Z}_n \quad m^{ed} = m \bmod n$.
> Let $m \in \mathbb{Z}_n$. Notice that $\gcd(m, p) \in \{1, p\}$.
> If $\gcd(m, p) = 1$. Then, by Fermat's little theorem (Cor. 2.16) $m^{p-1} = 1 \bmod p$. This implies
> $m^{(p-1)(q-1)k} = m^{\varphi(n)k} = 1 \bmod p \implies m^{ed} = m^{1+\varphi(n)k} = m \bmod p$.
> If $\gcd(m, p) = p$, then $m^{ed} = m \bmod p \quad (\text{since we have written } 0 = 0 \bmod p)$

**Cosa dice:**
Invece di combattere contro il mostro gigante (modulo $n$), il prof lo taglia a metà. Analizza cosa succede all'equazione solo rispetto al modulo $p$.
Prende un messaggio $m$ qualsiasi. Ci sono solo due casi possibili rispetto a $p$:
* **Caso 1 (Nessuna parentela):** $m$ e $p$ non hanno divisori in comune ($\gcd = 1$). Qui usa il *Piccolo Teorema di Fermat*, che dice che $m^{p-1} \equiv 1 \pmod p$. Visto che $\varphi(n)$ contiene $(p-1)$ al suo interno, tutta la parte gigante dell'esponente collassa a $1$, e ci rimane un bellissimo $m^{ed} \equiv m \pmod p$.
* **Caso 2 (Parentela stretta):** $m$ è un multiplo di $p$ ($\gcd = p$). Questo era il caso che ci terrorizzava! Ma in modulo $p$, dire "multiplo di $p$" significa che $m$ vale zero! E zero elevato a qualsiasi potenza fa sempre zero. Quindi l'equazione diventa banalmente $0 \equiv 0 \pmod p$. Funziona lo stesso!

**Spiegazione dettagliata:**
Questo passaggio è un capolavoro di eleganza. Il prof ha appena dimostrato che, indipendentemente dal messaggio che scegli, se applichi cifratura e decifratura RSA, l'identità è sempre salva se la guardi "con gli occhiali" del modulo $p$. E siccome $p$ e $q$ sono simmetrici nella formula, la stessa identica magia avviene se metti "gli occhiali" del modulo $q$.

##### <p align="right"><font color="#2DC26B">1. Perché se gcd(m, p) = 1 allora m^{p-1} = 1  mod p?</font></p>

Questa non è una coincidenza, è una legge universale della teoria dei numeri chiamata **Piccolo Teorema di Fermat**.

Immagina il modulo $p$ come un cerchio di numeri da $0$ a $p-1$. Se prendi un numero $m$ che non ha nulla a che fare con $p$ (cioè $\gcd(m, p) = 1$) e inizi a elevarlo a potenza, succedono cose cicliche. Fermat ha scoperto che, se il modulo è un numero **primo**, elevando qualsiasi numero alla potenza $(p-1)$ "fai il giro completo" e torni esattamente all'unità ($1$).

**Nel tuo caso:**

L'esponente di RSA è $ed = 1 + k\varphi(n)$.

Sappiamo che $\varphi(n) = (p-1)(q-1)$.

Quindi l'equazione diventa:

$$m^{ed} = m^{1 + k(p-1)(q-1)} = m \cdot (m^{p-1})^{k(q-1)}$$

Se $(m^{p-1}) \equiv 1$, allora tutta quella parte gigante tra parentesi diventa $1$ elevato a qualcosa. E $1$ elevato a qualsiasi cosa fa sempre $1$.

Risultato: $m \cdot 1 = m$. **Magia: il messaggio è tornato intatto!**

---

##### <p align="right"><font color="#2DC26B">2. Il Piano del Funzionamento (Perché usiamo mod p e mod q)</font></p>

Immagina di costruire una calcolatrice. Prima di venderla, devi garantire che faccia $2+2=4$ **sempre**, non "quasi sempre".

Il problema iniziale era questo:

- La formula di RSA basata sul Teorema di Eulero diceva: "RSA funziona se il messaggio $m$ non è un multiplo di $p$ o $q$".
    
- Ma un matematico pignolo direbbe: _"E se per puro caso l'utente scrive un messaggio che è un multiplo di $p$? La calcolatrice gli darà un errore? Il messaggio verrà distrutto?"_
    

**Ecco a cosa servono mod $p$ e mod $q$:**

Il professore non li usa per aiutare l'hacker, li usa come **strumenti di prova**. Serve a dimostrare che, anche in quel caso "sfortunato" in cui $m$ è un multiplo di $p$, la matematica di RSA continua a sputare fuori il risultato corretto.

> **Serve a dirti:** "Tranquillo, la tua calcolatrice RSA non esploderà mai, qualunque numero tu ci scriva dentro. Funziona al 100%."

---

##### <p align="right"><font color="#00b050">3. Il Sistema delle "Coordinate" (Teorema Cinese dei Resti)</font></p>

Ogni numero $m$ all'interno del modulo $n = p \cdot q$ può essere visto come una coppia di coordinate:

- **Coordinata A:** Il resto di $m$ diviso $p$.
    
- **Coordinata B:** Il resto di $m$ diviso $q$.
    

Prendiamo l'esempio di $n=15$ ($p=3, q=5$). Confrontiamo due multipli di $p$: il messaggio **$3$** e il messaggio **$6$**.

| **Messaggio (m)** | **Mondo di p (mod 3)** | **Mondo di q (mod 5)** | **Coppia Unica (p,q)** |
| ----------------- | ---------------------- | ---------------------- | ---------------------- |
| **$m = 3$**       | $0$                    | **$3$**                | $(0, 3)$               |
| **$m = 6$**       | $0$                    | **$1$**                | $(0, 1)$               |
**Cosa impariamo?** Anche se nel mondo di $p$ sembrano entrambi $0$, nel mondo di $q$ sono **diversi**. Il Teorema Cinese dei Resti garantisce che per ogni coppia di coordinate esiste **un solo e unico** numero in $mod\ n$. Non ci sono collisioni.
##### <p align="right"><font color="#2DC26B">4. Il Piano della Sicurezza (L'hacker)</font></p>

All'hacker non importa nulla della nostra dimostrazione con $p$ e $q$. Lui vede solo il messaggio cifrato $c$ e il modulo $n$.

Il fatto che $m$ sia un multiplo di $p$ è un problema di **sicurezza** solo se l'hacker potesse vedere $m$ "nudo". Ma siccome $m$ è "vestito" (cifrato) dentro $c$, l'hacker non sa che quel messaggio è proprio un multiplo di $p$. Quindi non può usarlo per calcolare il $\gcd$ e rompere $n$.

---

##### <p align="right"><font color="#2DC26B">5. Quindi, "di che cavolo stiamo parlando?"</font></p>

Stiamo parlando di **coerenza matematica**.

Se non avessimo fatto la dimostrazione con mod $p$ e mod $q$, avremmo avuto un sistema crittografico "con il buco". Sarebbe stato come dire:

_"RSA è sicuro, però ricordatevi di non inviare mai messaggi che siano multipli di quei due numeri primi segreti che non conoscete... altrimenti il sistema fallisce e non riuscite più a leggere il messaggio."_

Sarebbe assurdo, no? Come faresti a evitare dei numeri che non conosci?

**La svolta è stata questa:**

1. **Domanda:** RSA funziona per TUTTI i numeri?
    
2. **Dubbio:** Forse no, se becchiamo un multiplo di $p$ la formula di Eulero non si applica.
    
3. **Soluzione (la dimostrazione del prof):** "Uso il trucco di guardare dentro $p$ e $q$ per dimostrarvi che, pure se becchiamo quei numeri rari, RSA sputa fuori il risultato giusto."
    
4. **Risultato:** Possiamo usare RSA ad occhi chiusi su qualsiasi file, immagine o testo, senza temere che la matematica si rompa.

##### <p align="right"><font color="#2DC26B">6. Perché se gcd(m, p) = p allora ridà 0</font></p>

Qui la questione è molto più "brutale" e semplice.

Dire che $\gcd(m, p) = p$ significa, che $m$ è un **multiplo di $p$** (es. se $p=7$, $m$ potrebbe essere $7, 14, 21, \dots$).

- **In aritmetica modulare $p$**, un multiplo di $p$ equivale a **zero**. È come dire che se sono le 12:00, in un orologio da 12 ore, la lancetta sta sullo $0$.
    
- Quindi, se $m$ è un multiplo di $p$, allora $m \equiv 0 \pmod p$.
    

Ora guarda l'equazione di cifratura/decifratura:

$$m^{ed} \equiv ? \pmod p$$

Se $m$ è $0$, l'equazione diventa:

$$0^{ed} \equiv 0 \pmod p$$

Siccome avevamo detto che $m \equiv 0$, l'equazione finale è $0 = 0$.

**Il messaggio è tornato intatto anche qui!** Anche se $m$ era un multiplo "pericoloso" di $p$, la matematica non si è rotta: è rimasta coerente.

---

##### <p align="right"><font color="#2DC26B">7. A cosa servono mod p e mod q? (Il "Ponte")</font></p>

Siccome su $n$ (che è un numero composto) siamo bloccati dalla clausola del $\gcd=1$, il professore scende di un livello e va sui **numeri primi** ($p$ e $q$).

Sui numeri primi abbiamo uno strumento più potente: il **Piccolo Teorema di Fermat**.

La scomposizione in mod $p$ e mod $q$ è servita a dirti questo:

- **In modulo $p$:** Anche se $m$ è un multiplo di $p$ (e quindi Eulero su $n$ fallirebbe), la matematica ci dice che $m^{ed} \equiv m$ è comunque **vera** (perché $0 = 0$).
    
- **In modulo $q$:** Vale lo stesso discorso.
    

**Cosa ti ha detto questa dimostrazione?**

Ti ha dato la certezza che la proprietà $m^{ed} \equiv m$ è valida **sempre**, anche quando $\gcd(m, n) \neq 1$. Ha "tappato il buco" che il Teorema di Eulero lasciava scoperto.

---

##### <p align="right"><font color="#2DC26B">8. Perché non "doverebbe funzionare" se m è multiplo?</font></p>

In matematica, se non lo dimostri, non esiste. Senza questo passaggio, un critico potrebbe dirti:

_"Ok, RSA funziona per il 99.999% dei messaggi. Ma chi mi garantisce che se invio un multiplo di $p$ non mi esca fuori un risultato completamente a caso invece del mio messaggio originale?"_

Usando mod $p$ e mod $q$, il professore ha dimostrato che **l'uguaglianza regge anche nel caso peggiore**.

### 28. La Ricomposizione e il Trionfo (Il Lemma 2.38)

**Testo Originale:**
> In any case $m^{ed} = m \bmod p$, and similarly $m^{ed} = m \bmod q$.
> So we have proved
> $\begin{cases} m^{ed} = m \bmod p \implies p \mid m^{ed} - m \\ m^{ed} = m \bmod q \implies q \mid m^{ed} - m \end{cases}$
> and, since $p$ and $q$ are **distinct primes**, (n is square-free) by Lemma 2.38, $n = pq \mid m^{ed} - m$, and therefore
> $m^{ed} = m \bmod n$, what we wanted to prove. $\blacksquare$

**Cosa dice:**
Mettiamo insieme i pezzi. Abbiamo scoperto che la differenza tra il messaggio decifrato e quello originale ($m^{ed} - m$) è divisibile esattamente per $p$, ed è divisibile esattamente per $q$.
Ma $p$ e $q$ sono due numeri primi completamente diversi. C'è una regola dell'algebra (Lemma 2.38, derivato dal Teorema Cinese del Resto) che dice: se due numeri primi diversi dividono una torta, allora anche la loro moltiplicazione dividerà quella torta.
Quindi, il loro prodotto ($n = pq$) divide la differenza. E scrivere questo in matematica modulare significa esattamente: **$m^{ed} \equiv m \pmod n$**. 
Il quadratino nero ($\blacksquare$) sigilla la dimostrazione. 

**Spiegazione dettagliata:**
Questa è la vittoria totale. Abbiamo appena dimostrato che RSA è una macchina indistruttibile. Puoi dargli in pasto qualsiasi numero da $0$ a $n-1$, senza fare alcun controllo preventivo, e le potenze $e$ e $d$ si incastreranno sempre alla perfezione restituendoti l'input originale. L'asterisco che limitava il nostro campo d'azione è stato spazzato via.

---

### 29. La Doccia Fredda Finale (L'Amara Verità sulla Sicurezza)

**Testo Originale:**
> The security of RSA w.r.t. passive eavesdroppers **must be assumed** (we don't know how to prove it).

**Cosa dice:**
Dopo un trionfo logico del genere, il professore ci riporta brutalmente con i piedi per terra. 
Abbiamo dimostrato in modo assoluto che RSA *funziona* (reversibilità). Ma non possiamo dimostrare matematicamente che sia *sicuro al 100%*. Dobbiamo semplicemente **presumerlo**.

**Spiegazione dettagliata:**
Questa è la più grande ironia della crittografia informatica. Tutto il sistema bancario mondiale si basa su RSA, ma nessun matematico sulla Terra è mai riuscito a scrivere una dimostrazione che garantisca che sia impossibile fattorizzare un numero in tempi veloci. 
Lo usiamo perché, da 50 anni, le menti più brillanti del pianeta (e i supercomputer più potenti) ci provano e falliscono. "Assumiamo" che sia sicuro perché finora ha retto. Ma se domani mattina un genio si svegliasse e pubblicasse un algoritmo veloce per la fattorizzazione, RSA crollerebbe in un pomeriggio. E l'ultima frase del prof è lì a ricordarci proprio questa vulnerabilità filosofica.


---

### 30. Il Gioco dell'Hacker (Il Problema RSA)

**Testo Originale:**

> **Definition 4.11 (RSA problem - computing e-th roots)**
> 
> For each PPT algorithm $\mathcal{A}$ and for each algorithm `GenRSA` [...] let us define the following 2-party game $\text{RSA}_{\text{GenRSA}, \mathcal{A}}$ between $\mathcal{A}$ and the challenger $\mathcal{C}$:
> 
> 1. given $N$ the security parameter, $\mathcal{C}$ executes `GenRSA(N)` and obtains $(n, e, d)$
>     
> 2. $\mathcal{C}$ chooses $c \in_R \mathbb{Z}_n$ uniformly at random
>     
> 3. $\mathcal{A}$ is given $(n, e, c)$
>     
> 4. $\mathcal{A}$ outputs $x \in \mathbb{Z}_n$.
>     
>     $\mathcal{A}$ wins the game $\iff x^e = c \bmod n$.
>     
>     _(Nota laterale: $x$ is the $e$-th root of $c$ modulo $n$)_
>     

**Cosa dice:**

Il professore formalizza la sfida che ogni hacker ($\mathcal{A}$) deve affrontare per craccare RSA.

Lo Sfidante ($\mathcal{C}$) crea le chiavi, prende un messaggio cifrato a caso ($c$), e lo butta in pasto all'hacker insieme alla chiave pubblica ($n$, $e$).

Per vincere, l'hacker deve trovare quel numero $x$ (il messaggio originale) che, elevato alla $e$, dia esattamente $c$. In matematica, questo significa calcolare la **radice $e$-esima modulo $n$**.

**Spiegazione dettagliata:**

Estrarre una radice quadrata o cubica coi numeri normali è facile. Estrarre una radice $e$-esima in un mondo modulare (dove i numeri si "arrotolano" su se stessi raggiunto il limite $n$) è un inferno. Senza la "botola" (la chiave privata $d$), l'unico modo per risolvere $x^e \equiv c \pmod n$ è cercare di indovinare $x$ andando per tentativi, o cercare di fattorizzare $n$. Entrambe le strade richiedono un tempo incalcolabile.

---

### 31. Il Sigillo di Sicurezza (Definition 4.12)

**Testo Originale:**

> **Definition 4.12**
> 
> We say that the RSA problem is hard w.r.t. `GenRSA` if for all PPT algorithms playing the game of the RSA problem (Definition 4.11), there exists a negligible function $\varepsilon(N)$ such that
> 
> $P(\mathcal{A} \text{ wins the game RSA}) < \varepsilon(N)$.
> 
> $(Gen, Enc, Dec)$ as in Def 4.9
> 
> " satisfy (4) of Def 4.1 (**proven** in thm 4.10)
> 
> " " (5) of Def 4.1 (**assumed** to be hard as in Def. 4.12)
> 
> $\Downarrow$
> 
> $(Gen, Enc, Dec)$ of Def. 4.9 (RSA) is a public-key cryptosystem.

**Cosa dice:**

Come anticipato alla fine della lezione precedente, noi **assumiamo** che il Problema RSA sia "difficile" (ovvero che la probabilità di vincere dell'hacker sia praticamente zero, descritta dalla funzione trascurabile $\varepsilon$).

Mettendo insieme la reversibilità (che abbiamo **dimostrato** col sangue) e la sicurezza (che abbiamo **presunto**), il professore dichiara ufficialmente che RSA è un Crittosistema a Chiave Pubblica a tutti gli effetti.

---

### 32. Come Siamo Arrivati Fin Qui (Il Riassunto Definitivo)

**Testo Originale:**

> **How we got here:**
> 
> - Starting from Pohlig-Hellman exponentiation cipher (PHEC) in $\mathbb{Z}_p^*$.
>     
>     **Problem:** knowledge $e \implies$ knowledge $d$
>     
>     **Fix:** work in $\mathbb{Z}_n^*$
>     
> - considering PHEC in $\mathbb{Z}_n^*$.
>     
>     **Problem:** only elements coprime with $n$ can be encrypted
>     
>     **Fix:** take $n$ square-free
>     
> - defining RSA in $\mathbb{Z}_{p \cdot q}$ with $n = p \cdot q$ square-free.
>     

**Cosa dice e cosa significa:**

Questo è il "Bignami" di RSA, il riassunto concettuale di tutta la genialità dell'algoritmo in 3 step:

1. **L'Intuizione Iniziale (Pohlig-Hellman in $\mathbb{Z}_p^*$):** Volevamo usare le potenze come lucchetti in un gruppo con modulo primo.
    
    - _Il Problema:_ Tutti conoscono $p$, quindi tutti conoscono la grandezza dell'ingranaggio ($p-1$). L'hacker si calcola $d$ e ti ruba tutto.
        
    - _La Soluzione:_ Nascondere l'ingranaggio passando a un gruppo con modulo composto ($\mathbb{Z}_n^*$).
        
2. **Il Gruppo Nascondiglio (Pohlig-Hellman in $\mathbb{Z}_n^*$):** Usiamo un modulo composto $n$, di cui nessuno sa calcolare $\varphi(n)$.
    
    - _Il Problema:_ La matematica di questo gruppo ci impediva di cifrare i messaggi che avevano divisori in comune con $n$. Una restrizione inaccettabile per un software reale.
        
    - _La Soluzione Definitiva:_ Scegliere un $n$ specifico che sia _square-free_ (cioè composto da numeri primi tutti diversi, nel nostro caso solo due: $p \cdot q$).
        
3. **Il Trionfo (RSA):** Grazie a questa scelta, il Teorema Cinese del Resto ci ha permesso di scavalcare la restrizione, creando un algoritmo sicuro, cieco ai contenuti, e spaventosamente elegante.
    

Per farti capire quanto sia elegante e "meccanico" questo processo, ti preparo un simulatore interattivo. Potrai inserire tu stesso due piccoli numeri primi (come $p=11$ e $q=13$) e vedere come il computer calcola la botola segreta e cifra un messaggio in tempo reale!

Agli ordini! Il professore qui lancia una vera e propria bomba nucleare su tutto quello che crediamo di sapere. Dopo averci convinto che RSA è un fortino inespugnabile basato sulla fattorizzazione, ci dice: *"Fermi tutti, vi ho raccontato solo mezza verità"*.

Inizia il capitolo sulla Sicurezza. Allacciati le cinture e smontiamo questo colpo di scena!

---
 Il professore qui lancia una vera e propria bomba nucleare su tutto quello che crediamo di sapere. Dopo averci convinto che RSA è un fortino inespugnabile basato sulla fattorizzazione, ci dice: *"Fermi tutti, vi ho raccontato solo mezza verità"*.

Inizia il capitolo sulla Sicurezza. Allacciati le cinture e smontiamo questo colpo di scena!
### 33. L'Illusione della Fattorizzazione (Sezione 4.4)

**Testo Originale:**
> **4.4 Security of (textbook) RSA**
> We have introduced RSA as a public-key cryptosystem, choosing the group where everything is set **because** of the difficulty of finding its order via factorization of a large integer. We may be tempted to believe that the hardness of the RSA problem depends on the hardness of the problem of factorizing large integers.
> However, this is believed to be false. Precisely, it is believed that the RSA problem is easier than the factorization problem.
> In this section we will show elements in support of this thesis, showing that the textbook version of RSA can be broken (even) without relying on factorizing the RSA module $n$, provided that we consider attackers which are (slightly) more capable than simple passive eavesdroppers.

**Cosa dice:**
Il professore sfata il mito più grande della crittografia. Tutti pensano: "Per rompere RSA, devi per forza fattorizzare il modulo $n$". Falso. 
I matematici credono fermamente che decifrare un messaggio RSA (il Problema RSA) sia **più facile** che scoprire i due numeri primi originali (il Problema della Fattorizzazione). 
Nelle prossime lezioni, il prof ci dimostrerà come un hacker furbo possa fregare la versione base di RSA (chiamata *"Textbook RSA"*) senza nemmeno provare a calcolare $p$ e $q$, ma semplicemente sfruttando i punti deboli della matematica se gli concediamo di fare qualcosa in più che "ascoltare passivamente".

**Spiegazione dettagliata:**
Questo è il motivo per cui negli appunti c'è scritto "(textbook) RSA" tra parentesi. La versione da libro di testo ($c = m^e \bmod n$) è matematicamente pura, ma nella vita reale è **bucabile**. Se tu usassi l'RSA nudo e crudo per cifrare una password, un hacker attivo (che magari manipola i messaggi in transito o fa richieste particolari al server) potrebbe ingannare il sistema. Ecco perché nel mondo reale usiamo l'RSA con il "Padding" (aggiungiamo bit casuali al messaggio prima di cifrarlo), per distruggere la struttura matematica pura che l'hacker vuole sfruttare.

---

### 34. Il Gioco della Fattorizzazione (Definition 4.13)

**Testo Originale:**
> **Definition 4.13 (Factor)**
> For each PPT algorithm $\mathcal{A}$ and for each algorithm `GenModulus` as in Definition 4.7 let us define the following 2-party game $\text{Factor}_{\text{GenModulus}, \mathcal{A}}$ between $\mathcal{A}$ and $\mathcal{C}$:
> 1) given a security parameter $N$, $\mathcal{C}$ runs `GenModulus` and obtains $(n, p, q)$
> 2) $\mathcal{A}$ is given $n$
> 3) $\mathcal{A}$ outputs $(p', q')$
> $\mathcal{A}$ wins the game $\iff p' \cdot q' = n$.

**Cosa dice:**
Prima di mostrarci i trucchi degli hacker, il prof deve definire formalmente il problema padre di tutti i problemi. Questo è il gioco "Factor".
Lo Sfidante $\mathcal{C}$ genera i due numeri primi e li moltiplica per ottenere $n$. 
Passa solo il numero gigante $n$ all'Hacker $\mathcal{A}$. 
L'Hacker vince se riesce a sputare fuori i due numeri primi esatti che, moltiplicati, ridanno $n$.

**Spiegazione dettagliata:**
È il gioco più antico della matematica moderna. È facilissimo moltiplicare $137 \times 823$ in un secondo. Ma se io ti do solo il numero $112751$ e ti dico "Quali due numeri primi ho moltiplicato?", devi fare una fatica immensa. Sui numeri a 2048 bit (quelli usati da `GenModulus`), questo gioco è attualmente impossibile da vincere per qualsiasi computer sulla Terra.

---

### 35. La Strada a Senso Unico (Definizione 4.14 e Conclusione)

**Testo Originale:**
> **Definition 4.14**
> We say that Factor is hard w.r.t. `GenModulus` if for each PPT algorithm $\mathcal{A}$ playing the game of Definition 4.13 there exists a negligible function $\varepsilon(N)$ such that
> $P(\mathcal{A} \text{ wins the game Factor}_{\text{GenModulus}, \mathcal{A}}(N)) < \varepsilon(N)$.
> 
> Let us now show formally something that was clear since the beginning of the construction of RSA: an attacker that can efficiently solve Factor can solve the RSA problem.

**Cosa dice:**
Come sempre, assumiamo che il problema Factor sia "duro" (probabilità di vincita trascurabile). 
Ma l'ultima riga stabilisce la regola d'oro della gerarchia dei problemi matematici. Il prof dice: andiamo a dimostrare formalmente che **se sai risolvere la Fattorizzazione, sai risolvere RSA in automatico**.

**Spiegazione dettagliata:**
Questa è la logica stringente della crittografia.
* **Fattorizzazione $\implies$ Rottura di RSA.** Se l'hacker vince il gioco Factor (scopre $p$ e $q$), calcola $\varphi(n)$, calcola $d$, e legge tutti i tuoi messaggi. Partita finita.
* **Rottura di RSA $\not\implies$ Fattorizzazione.** Questo è quello che il prof ha svelato all'inizio della pagina! Puoi trovare un modo furbo per leggere il messaggio decifrato ($m$), vincendo il gioco RSA, senza mai scoprire $p$ e $q$. 

Il professore ci sta preparando a una serie di "Attacchi a RSA" (come l'attacco di Hastad, o gli attacchi scelti-cifrati). Manda le prossime foto, andiamo a fare gli hacker per davvero!

---
Il professore ci porta ora dietro le quinte della teoria della complessità. Avevamo detto che se sai fattorizzare, sai rompere RSA. Ora il prof non si accontenta delle parole: lo dimostra con il rigore di un teorema.

E subito dopo, elenca le armi a disposizione degli hacker per tentare questa impresa disperata. Smontiamo le pagine!
### 36. La Prova della "Non-Superiorità" (Theorem 4.15)

**Testo Originale:**

> **Theorem 4.15**
> 
> The RSA problem w.r.t. GenRSA is **no harder than** Factor w.r.t. GenModulus (where GenModulus is the same used as a subroutine in GenRSA).
> 
> **Proof**
> 
> Let $\mathcal{A}$ be a PPT algorithm that solves $\text{Factor}_{\text{GenModulus}, \mathcal{A}}(N)$ with non-negligible probability.
> 
> Let $(n, e, c)$ be a challenge as in Definition 4.11.
> 
> Then a PPT algorithm $\mathcal{B}$ for the RSA problem can be for example (there can be other ways) defined as follows:
> 
> 1. Runs $\mathcal{A}$ on $n$ and get $p, q$ such that $p \cdot q = n$
>     
> 2. computes $\varphi := (p-1)(q-1)$
>     
> 3. computes $d$ s.t. $de = 1 \bmod \varphi$
>     
> 4. returns $c^d$. (if $\mathcal{A}$ is right, $(c^d)^e = c^{ed} = c$)
>     
>     $\mathcal{B}$ terminates in polynomial time and wins the game of the RSA problem with non-negligible probability since $(c^d)^e = c^{ed} = c \bmod n$. $\blacksquare$
>     

**Cosa dice:**

Questo teorema stabilisce una gerarchia tra i problemi: rompere RSA non può essere matematicamente _più difficile_ che fattorizzare $n$. Al massimo è facile uguale.

La dimostrazione usa una tecnica informatica chiamata **Riduzione**. Il prof inventa un hacker immaginario (l'Algoritmo $\mathcal{B}$) che vuole vincere il gioco di RSA. Questo hacker non è un genio della crittografia, ma ha un asso nella manica: ha comprato sul dark web un software miracoloso (l'Algoritmo $\mathcal{A}$) capace di fattorizzare numeri giganti.

Cosa fa l'hacker $\mathcal{B}$?

1. Prende il modulo pubblico $n$ e lo dà in pasto al software $\mathcal{A}$, che gli sputa fuori $p$ e $q$.
    
2. Con $p$ e $q$ in mano, si calcola la Funzione di Eulero $\varphi$.
    
3. Usa l'Algoritmo di Euclide per trovare la chiave privata $d$.
    
4. Decripta il messaggio $c$ usando $d$.
    
    Visto che tutti questi 4 passaggi sono velocissimi, l'hacker $\mathcal{B}$ vince sempre il gioco RSA. Abbiamo dimostrato che se cade la Fattorizzazione, cade RSA.
    

**Spiegazione dettagliata:**

Presta molta attenzione alle parole "no harder than" (non più difficile di). Il prof **non** sta dicendo che RSA e la Fattorizzazione sono esattamente lo stesso problema. Sta dicendo che RSA è al massimo _difficile quanto_ la fattorizzazione. Torna al discorso della lezione precedente: un hacker potrebbe trovare una scorciatoia per rompere RSA (Algoritmo $\mathcal{B}$) _senza_ aver bisogno di comprare quel software per fattorizzare (Algoritmo $\mathcal{A}$).

---

### 37. Le Armi Contro il Modulo (L'Arsenale dell'Hacker)

**Testo Originale:**

> **How hard is Factor?**
> 
> We will not discuss factorization algorithms in this course. However, to give an idea, these are some of the possible algorithms with the corresponding complexity.
> 
> If $n = p \cdot q$, with $p, q \sim O(2^N)$, factorizing $n$ requires:
> 
> - brute force : $O(2^N)$
>     
> - Pollard's $(p-1)$ (similar to $\rho$) : $O(2^{N/2})$
>     
> - General number field sieve : **subexponential**.
>     

**Cosa dice:**

Visto che RSA dipende così tanto dalla grandezza dei numeri primi, il prof ci fa una carrellata dei migliori metodi conosciuti al mondo per cercare di smontare il modulo $n$ (che è lungo $2N$ bit, perché $p$ e $q$ sono lunghi $N$ bit ciascuno).

- **Forza Bruta:** Provare a dividere $n$ per tutti i numeri dispari esistenti. Tempo esponenziale $O(2^N)$. Morte termica dell'universo.
    
- **Pollard's $p-1$:** Un cugino del Pollard's Rho che abbiamo studiato a fondo. Taglia i tempi alla radice quadrata $O(2^{N/2})$. Velocissimo, ma sui numeri enormi di RSA non basta ancora.
    
- **General Number Field Sieve (GNFS):** L'arma fine di mondo. È l'algoritmo di fattorizzazione più veloce oggi noto all'umanità. Il suo tempo non è esponenziale puro, ma **sub-esponenziale** (una via di mezzo tra i tempi impraticabili degli esponenziali e i tempi fattibili dei polinomiali).
    

**Spiegazione dettagliata:**

Questa singola parolina, "subexponential", è il motivo per cui le chiavi RSA sono così assurdamente ingombranti (2048 o 4096 bit). Mentre per le Curve Ellittiche un attacco "taglia" i tempi al massimo della radice quadrata (permettendoti chiavi piccole da 256 bit), contro RSA l'algoritmo GNFS scava gallerie molto più veloci. Per mantenere la sicurezza sotto la soglia di sbarramento (il famoso limite MTC), siamo costretti a gonfiare il modulo $n$ a dismisura.

---

### 38. La Trinità delle Chiavi RSA

**Testo Originale:**

> It is straightforward to realize that the RSA problem can be efficiently solved at least in these three ways:
> 
> 1. knowing the factorization of $n$,
>     
> 2. knowing $d$,
>     
> 3. knowing $\varphi(n)$.
>     
>     Let us prove that they are **equivalent**:
>     

**Cosa dice:**

Il professore tira le somme su quello che tiene in piedi l'intero sistema. Per leggere un messaggio RSA al volo, ci sono tre segreti che l'hacker potrebbe voler rubare:

1. Scoprire $p$ e $q$ (fattorizzare).
    
2. Rubare direttamente la chiave privata $d$.
    
3. Scoprire il valore dell'ingranaggio centrale $\varphi(n)$.
    

**Spiegazione dettagliata e Cliffhanger:**

Istintivamente, potresti pensare che scoprire la chiave privata $d$ sia molto più "facile" o molto diverso dal fattorizzare l'intero numero $n$. Ma la matematica è spietata: il prof lancia il guanto di sfida. Ci dice che proverà a dimostrare che queste tre cose **sono la stessa identica cosa**.

Se tu mi sveli la chiave $d$, io posso usarla come grimaldello matematico per risalire all'indietro e fattorizzare $n$. È un effetto domino: se cade un segreto, gli altri due collassano istantaneamente in modo matematico.

---
Ci addentriamo nel cuore dell'algebra astratta. Il professore aveva lanciato la sfida: dimostrare che conoscere la chiave privata $d$, conoscere $\varphi(n)$ o conoscere la fattorizzazione di $n$ (cioè $p$ e $q$) **sono esattamente la stessa cosa**. 

In queste tre pagine, il prof esegue la dimostrazione matematica di questa "Trinità". Smontiamo questo capolavoro di hacking teorico!

### 39. Le Strade Facili e l'Equazione di Secondo Grado ($\varphi \Rightarrow p, q$)

**Testo Originale (Teorema 4.16 - Parte 1):**
> **Theorem 4.16**
> The knowledge of the following three pieces of information can be obtained in polynomial-time by the others:
> $p/q$ (factorization of $n$)
> $\varphi$ (the value of $\varphi(n)$)
> $d$ (the value of the decryption exponent $d$)
> **Proof**
> $p/q \implies d$ (trivial)
> $\varphi \implies d$ (trivial)
> $\varphi \implies p/q$ : Let $\varphi(n) = (p-1)(q-1)$
> $\begin{cases} \varphi(n) = pq - p - q + 1 \\ n = pq \end{cases} \implies \begin{cases} q = n - \varphi(n) - p + 1 \\ n = p(n - \varphi(n) - p + 1) \end{cases}$
> $\implies p^2 + (\varphi - n - 1)p + n = 0$.
> This is an equation with $\Delta > 0$ a perfect square, so $p$ is found, and so it is $q$.

**Cosa dice:**
Il prof inizia con i collegamenti ovvi: se hai $p$ e $q$, calcoli $\varphi(n)$ e poi trovi $d$ (è letteralmente come generiamo le chiavi RSA!). E se hai $\varphi(n)$, usi l'Algoritmo di Euclide e trovi $d$ all'istante.
Ma cosa succede se un hacker ruba dal tuo server il valore di $\varphi(n)$ e vuole scoprire $p$ e $q$? 
Il prof imposta un banale sistema a due incognite. Sostituendo $q$ nella seconda equazione, ottiene una classica equazione di secondo grado:
$$p^2 + (\varphi(n) - n - 1)p + n = 0$$
Risolvendo questa equazione con la formula del delta ($\Delta$) che si impara al liceo, l'hacker trova istantaneamente $p$ e $q$. 

**Spiegazione dettagliata:**
Questo dimostra che tenere segreto $\varphi(n)$ è cruciale quanto tenere segreta la chiave privata. Se $\varphi(n)$ trapela, l'intera cassaforte viene smontata in frazioni di millisecondo usando la matematica delle scuole superiori. 

---

### 40. L'Incubo Finale: Dalla Chiave Privata ai Numeri Primi ($d \Rightarrow p, q$)

**Testo Originale (Teorema 4.16 - Parte 2):**
> $d \implies p/q$
> Let us start by noting the following facts:
> 1) there exist only two **roots of unity modulo $p$**, namely $x = \pm 1$. [...]
> 2) there exists four roots of unity modulo $p \cdot q$. Two of them (called trivial) are $\pm 1$. The others comes from the solution of [...]
> Now, since $ed = 1 \bmod \varphi(n)$, $ed - 1$ is a multiple $k$ of $\varphi(n)$ and, since $\varphi(n)$ is even, $k$ is even. Therefore we write $k = 2^t \cdot r$, where $t \ge 1$ and $r$ is odd.

**Cosa dice:**
Questo è il pezzo forte. L'hacker ha rubato solo la tua chiave privata $d$ (oltre al tuo lucchetto pubblico $e$). Come fa a smontare $n$?
Il prof introduce il concetto di **Radici dell'Unità** (i numeri che, elevati al quadrato, danno $1$). 
* Nel mondo del modulo primo $p$, ci sono solo due radici quadrate di $1$: sono $+1$ e $-1$.
* Ma nel mondo del modulo composto $n = pq$, si aprono delle fratture. Esistono **4 radici quadrate di $1$**. Due sono banali ($+1$ e $-1$), ma due sono **non-banali** (numeri strani che, se moltiplicati per se stessi, fanno incredibilmente $1 \bmod n$).

L'hacker sa che $e \cdot d - 1$ è un multiplo pari di $\varphi(n)$. Lo chiama $k$. Essendo pari, lo si può dividere per $2$ per un po' di volte (esattamente $t$ volte).

---

### 41. L'Estrazione e il Casinò (Algoritmo di Las Vegas)

**Testo Originale:**
> $y_0 := x^k = 1$ (is a multiple of $\varphi(n) = \#\mathbb{Z}_n^*$)
> $y_1 := x^{k/2}$
> $y_2 := x^{k/4}$
> $\vdots$
> $y_t := x^{k/2^t} = x^r$
> Let us assume that $i$ is such that $y_i = 1$ and $y_{i+1} \neq 1$. Then $y_{i+1}^2 = y_i = 1$, and so $y_{i+1}$ is a **non-trivial root of unity**.
> Since $y_{i+1}^2 = 1 \bmod n \implies n \mid y_{i+1}^2 - 1 = (y_{i+1} - 1)(y_{i+1} + 1)$
> $\gcd(n, y_{i+1} - 1)$ shows the factorization of $n$. If all $y_i = 1$, then do this again with another element $x$.
> The algorithm coming from Theorem 4.16 ($d \implies p/q$) is called an algorithm of **Las-Vegas type**. It means that it is a probabilistic algorithm that may not give an answer [...] but when it gives an answer, then that answer is correct.

**Cosa dice:**
Ecco l'algoritmo letale:
1. Scegli un numero a caso $x$.
2. Elevalo alla $k$. Il risultato è $1$ (per il Teorema di Eulero).
3. Inizia a "dimezzare" l'esponente (calcolando le radici quadrate $y_1, y_2 \dots$). 
4. Stai cercando il momento esatto in cui fai la radice quadrata di $1$ e il risultato **non è né $1$ né $-1$**, ma una delle due famose **radici non-banali** (chiamiamola $y$).
5. Se trovi questa $y$, hai vinto. Siccome $y^2 \equiv 1 \pmod n$, significa che $n$ divide $(y-1)(y+1)$. Calcoli il **Massimo Comune Divisore** tra il modulo $n$ e $(y-1)$, e il computer ti sputerà fuori esattamente $p$ o $q$!

Perché si chiama **Algoritmo di Las Vegas**? Perché dipende dalla fortuna! Hai scelto un numero a caso $x$ all'inizio. A volte, calcolando le radici, atterri sui noiosi $+1$ o $-1$ (hai perso, la slot machine non ha pagato). In tal caso, il prof dice: "do this again", butta un altro gettone e scegli un altro numero $x$. 
La matematica garantisce che hai **almeno il 50% di probabilità di vincita** ad ogni tentativo. Dopo 3 o 4 gettoni, sbanchi il casinò, trovi la radice non-banale e disintegri il modulo RSA.

**Spiegazione dettagliata:**
Questa è una delle dimostrazioni più eleganti di tutta la crittografia. Dimostra che se per caso il tuo server viene bucato e ti rubano la chiave privata $d$, **non puoi semplicemente generare un nuovo $e$ e un nuovo $d$ mantenendo lo stesso modulo $n$**. L'hacker, avendo il vecchio $d$, applicherà questa slot machine di Las Vegas, fattorizzerà $n$, e leggerà anche tutti i messaggi futuri! Se perdi $d$, l'intero sistema è compromesso alla radice e devi buttare via anche $n$.

Siamo arrivati alla fine di questa trinità. Se hai le prossime slide, andiamo avanti: scommetto che inizieremo a vedere gli attacchi pratici contro l'RSA "da libro di testo"!

---

##### <p align="right"><font color="#00b050">1. Il Principio Fondamentale: Conoscere d =  Conoscere p, q</font></p>

In RSA, la sicurezza si basa sulla difficoltà di fattorizzare $n$. Tuttavia, se un attaccante ottiene la chiave privata $d$ (o l'esponente $\phi(n)$), può smontare $n$ istantaneamente. Questo accade perché $d$ contiene la "mappa" per trovare le **radici non-banali dell'unità**.

---

##### <p align="right"><font color="#00b050">2. Le Radici dell'Unità: Banali vs "Strane"</font></p>

In un modulo primo $p$, l'equazione $y^2 \equiv 1 \pmod p$ ha solo due soluzioni: $1$ e $-1$ (chiamate **radici banali**).

Ma in un modulo composto $n = p \cdot q$, esistono sempre **quattro** radici quadrate di $1$.

- **Radici Banali:** $1$ e $-1$ ($n-1$). Non danno informazioni.
    
- **Radici Non-Banali (le "Strane"):** Sono numeri $y$ tali che $y^2 \equiv 1 \pmod n$, ma $y \neq 1$ e $y \neq -1$. Queste radici sono "ibride": si comportano come $1$ per uno dei fattori (es. $p$) e come $-1$ per l'altro (es. $q$).
    

---

##### <p align="right"><font color="#00b050">3. L’Algoritmo di Las Vegas (Passaggi Operativi)</font></p>

L'attacco segue questi passi logici:

1. **Calcolo di $k$:** Si ottiene $k = ed - 1$. Poiché $ed \equiv 1 \pmod{\phi(n)}$, allora $k$ è un multiplo di $\phi(n)$.
    
2. **Scelta casuale:** Si sceglie un numero casuale $x \in \mathbb{Z}_n^*$.
    
3. **Dimezzamento dell'esponente:** Si calcola la sequenza $y = x^{k/2}, x^{k/4} \dots$ finché l'esponente è pari.
    
4. **Caccia allo "strano":** Si cerca il momento in cui, partendo da un valore che fa $1 \pmod n$, si ottiene come radice quadrata un numero $y$ che **non è** né $1$ né $-1$.
    
5. **Estrazione:** Trovata la radice strana $y$, si calcola $\gcd(n, y-1)$. Il risultato sarà esattamente $p$ o $q$.
    

---

##### <p align="right"><font color="#00b050">4. La Logica della Scomposizione (Perché funziona?)</font></p>

Questa è la parte cruciale per l'esame. Se $y$ è una radice strana:

1. Sappiamo che $y^2 \equiv 1 \pmod n \implies n$ divide il prodotto $(y-1)(y+1)$.
    
2. Dato che $y$ è "strana" ($y \neq 1$ e $y \neq -1$):
    
    - $n$ **non divide** $(y-1)$.
        
    - $n$ **non divide** $(y+1)$.
        
3. Se $n = p \cdot q$ deve dividere il prodotto, ma non può stare interamente in una delle due parentesi, l'unica possibilità è che **$p$ divida una parentesi e $q$ divida l'altra**.
###### 1. La doppia faccia di $y$ (Il segreto del Teorema Cinese)

Ogni numero $y$ nel modulo $n = p \cdot q$ ha due "ombre": una nel mondo di $p$ e una nel mondo di $q$.

Per essere una **radice quadrata di 1**, $y^2$ deve fare $1$ in entrambi i mondi. Ma nei mondi primi ($p$ e $q$), le uniche radici sono $1$ e $-1$.

Le radici "strane" nascono quando $y$ sceglie **risultati diversi** nei due mondi:

- **Nel mondo di $p$:** $y \equiv 1 \pmod p$
    
- **Nel mondo di $q$:** $y \equiv -1 \pmod q$
###### 2. Perché $(y-1)$ è multiplo di $p$?

Guarda cosa succede alla prima parentesi $(y-1)$ se usiamo la definizione qui sopra:

- Se $y \equiv 1 \pmod p$, allora, per definizione di congruenza, **$y - 1$ è divisibile per $p$**.
    
- In altre parole: $y - 1 = k \cdot p$.
    
- Quindi **$p$ è "dentro" la parentesi $(y-1)$**.
    

###### 3. Perché $(y+1)$ è multiplo di $q$?

Guarda ora la seconda parentesi $(y+1)$ usando l'altra faccia di $y$:

- Se $y \equiv -1 \pmod q$, allora $y + 1 \equiv 0 \pmod q$.
    
- Questo significa che **$y + 1$ è divisibile per $q$**.
    
- In altre parole: $y + 1 = j \cdot q$.
    
- Quindi **$q$ è "dentro" la parentesi $(y+1)$**.
    

---

###### 4. Il "Divorzio" tra $p$ e $q$

Ora mettiamo tutto insieme. Sappiamo che $n$ (ovvero $p \cdot q$) deve dividere il prodotto:

$$(y-1)(y+1)$$

Ma ecco il colpo di scena: **$p$ non può stare in entrambe le parentesi!**

Perché? Se $p$ dividesse sia $(y-1)$ che $(y+1)$, allora dovrebbe dividere anche la loro differenza:

$$(y+1) - (y-1) = 2$$

Ma $p$ è un numero primo enorme (molto più grande di 2), quindi è impossibile che $p$ divida 2.

**Conclusione:** $p$ è obbligato a stare **solo** in una parentesi (es. $y-1$) e $q$ è obbligato a stare **solo** nell'altra (es. $y+1$). 
    

###### Esempio Pratico ($n=15, p=3, q=5$):

- Sia $y=11$. Notiamo che $11^2 = 121 \equiv 1 \pmod{15}$. È una radice di 1.
    
- $y$ è strana? Sì, $11 \neq 1$ e $11 \neq 14$ ($mod\ 15$).
    
- Calcoliamo $(y-1) = 10$ e $(y+1) = 12$.
    
- **Analisi:** $15$ non divide $10$ e non divide $12$. Ma $15$ divide il loro prodotto ($120$).
    
- **Separazione:** Il fattore $5$ si è "nascosto" nel $10$, il fattore $3$ si è "nascosto" nel $12$.
    
- **Uso del MCD:** $\gcd(15, 10) = \mathbf{5}$. Abbiamo trovato $q$.
    

---

##### <p align="right"><font color="#00b050">5. Perché si chiama "Las Vegas"?</font></p>

È un algoritmo probabilistico.

- Se scegliamo un $x$ "sfortunato", potremmo atterrare su radici banali ($1$ o $-1$) che non separano i fattori.
    
- La matematica garantisce però che almeno il **50%** dei numeri $x$ porti a una radice strana.
    
- Se fallisci, "do this again": basta cambiare $x$ e riprovare. In 2 o 3 tentativi la probabilità di successo è quasi del 100%.
    

##### <p align="right"><font color="#00b050">6. Conclusione per gli appunti</font></p>

- **Punto chiave:** La radice strana $y$ "spacca" il modulo $n$ perché è l'unico numero che "sincronizza" uno dei fattori primi sullo zero (nella parentesi $y-1$) e l'altro fattore nell'altra parentesi.
    
- **Conseguenza:** Mai riutilizzare un modulo $n$ se la chiave $d$ è stata compromessa, anche se si cambia l'esponente $e$. Con $d$ si ottiene la fattorizzazione, e con la fattorizzazione il sistema è distrutto per sempre.

##### <p align="right"><font color="#00b050">7. L'Analogia della Bicicletta (Il Segreto di p e q)</font></p>

Immagina che il modulo $n$ sia una **bicicletta con due ruote di dimensioni diverse**: la ruota **$p$** e la ruota **$q$**.

Perché la bicicletta sia in "posizione 1" (il traguardo), **entrambe** le ruote devono toccare terra esattamente sul punto di partenza.

##### <p align="right"><font color="#00b050">8. I Giri Completi ($x^k \equiv 1 \pmod n$)</font></p>

Quando elevi $x$ alla $k$ (multiplo di $\phi(n)$), stai facendo fare alla bici tantissimi giri. Alla fine, $x^k \equiv 1$ significa che **entrambe le ruote** sono tornate perfettamente sul traguardo.

- **Ruota $p$:** è sul traguardo ($1 \pmod p$).
    
- **Ruota $q$:** è sul traguardo ($1 \pmod q$).
    

##### <p align="right"><font color="#00b050">9. Fare la Radice Quadrata (Il Dimezzamento)</font></p>

Fare la radice quadrata significa "tornare indietro di mezzo giro" rispetto alla posizione precedente. Qui cerchiamo la **Radice Strana**:

- **Le Radici Banali (Inutili):**
    
    - **Tutte e due al traguardo:** Entrambe le ruote sono su $1$. La bici è in posizione $1 \pmod n$. Non hai scoperto nulla.
        
    - **Tutte e due a metà giro:** Entrambe le ruote sono a metà strada (posizione $-1$). La bici è in posizione $-1 \pmod n$. Anche qui, sono "sincronizzate", quindi non puoi separarle.
        
- **La Radice Strana (Vittoria):**
    
    Succede quando una ruota è sul traguardo e l'altra è a metà giro.
    
    - **Ruota $p$:** è sul traguardo ($1 \pmod p$).
        
    - **Ruota $q$:** è a metà giro ($-1 \pmod q$).
        
        **Perché è letale?** Perché la bicicletta non è né al traguardo né a metà giro nel suo insieme; è in una posizione "ibrida" ($y$) che non soddisfa entrambi i fattori allo stesso modo.


---

Dopo tutta questa intensa teoria della complessità, il professore ci fa finalmente sedere al tavolo del casinò.

Questo è l'**Esempio 4.17**: l'esecuzione numerica dal vivo dell'Algoritmo di Las Vegas che abbiamo studiato nella pagina precedente. Vediamo l'hacker in azione mentre disintegra un modulo RSA avendo rubato la chiave privata.

Smontiamo questa giocata magistrale!
### 42. Il Setup della Slot Machine (I parametri rubati)

**Testo Originale:**

> **Example 4.17**
> 
> Let us consider the following RSA parameters $(n, e, d)$ (remember that in general $d$ is not of public knowledge):
> 
> $n = 1441499, \quad e = 17, \quad d = 507905$.
> 
> Let $k = ed - 1 = 8634384 = 2^4 \cdot r$, $r$ an odd number.

**Cosa dice:**

L'hacker ha in mano il modulo pubblico $n$, l'esponente pubblico $e$, e in qualche modo è riuscito a rubare la tua chiave privata $d$.

Il primo passo dell'algoritmo è calcolare il numero magico $k$, che è semplicemente $e \cdot d - 1$. In questo caso fa $8634384$.

Siccome è un numero pari, l'hacker lo divide per $2$ finché non ottiene un numero dispari ($r$). Scopre che può dividerlo per $2$ esattamente **4 volte** (ecco perché $2^4$). Quei 4 step saranno le 4 "calate di leva" della slot machine.

---

### 43. Il Primo Gettone: Ritenta, sarai più fortunato

**Testo Originale:**

> If we take $x_0 = 1205486$ we can compute
> 
> $y_0 = x_0^k = 1 \bmod n$
> 
> $y_1 = x_0^{k/2} = 1 \bmod n$
> 
> $y_2 = x_0^{k/4} = 1 \bmod n$
> 
> $y_3 = x_0^{k/8} = 1 \bmod n$
> 
> $y_4 = x_0^{k/16} = 1 \bmod n$

**Cosa dice:**

L'hacker inserisce il primo gettone: sceglie un numero totalmente a caso, $x_0 = 1205486$.

Inizia a calcolarne le radici in sequenza dimezzando l'esponente. Ma è sfortunato: ad ogni singolo passaggio, il risultato ($y_i$) rimane incollato al numero $1$.

Non ha trovato nessuna "radice non-banale". Ha beccato solo le radici ovvie. La slot machine ha restituito zero monete. Come diceva il teorema: se succede, butta un altro gettone e prova con un altro numero.

---

### 44. Il Secondo Gettone: Jackpot e Fattorizzazione

**Testo Originale:**

> Let us take $x_1 = 123$. We can compute
> 
> $y_0 = y_1 = y_2 = y_3 = 1$ and $y_4 = 119533$.
> 
> Then computing
> 
> $\gcd(n, y_4 - 1) = 1423 = p$.
> 
> to conclude $q = n/p = 1013$.
> 
> So the factorization of $n$ is found.

**Cosa dice:**

L'hacker non demorde. Sceglie un altro numero a caso, un banalissimo $x_1 = 123$.

Il computer macina le radici. I primi quattro tentativi ($y_0$ fino a $y_3$) restituiscono ancora $1$. Sembra un altro fallimento.

Ma all'ultimo step possibile ($y_4$, corrispondente all'esponente dimezzato 4 volte), il risultato non è né $1$ né $-1$. Esce il numero **119533**.

**Jackpot.**

**Spiegazione dettagliata:**

Cosa ha di speciale questo numero 119533? Sappiamo che è la radice quadrata del passo precedente ($y_3$), che valeva $1$. Quindi, se tu prendi $119533$ e lo elevi al quadrato, otterrai esattamente $1 \bmod 1441499$. Questa è la famigerata **radice non-banale dell'unità**.

Non appena l'hacker vede questo numero, sa di aver vinto. Applica la formula finale: fa il Massimo Comune Divisore tra l'intero modulo $n$ e $(119533 - 1)$.

Il calcolo $\gcd(1441499, 119532)$ restituisce istantaneamente **1423**. Questo è $p$!

A questo punto, basta fare una divisione da scuola elementare ($1441499 / 1423$) per trovare $q = 1013$.

L'hacker ha smontato la tua cassaforte RSA usando solo due tentativi casuali. Questo conferma empiricamente l'avvertimento del professore: se perdi la chiave privata $d$, hai perso l'intero sistema.

Per farti toccare con mano quanto sia letale e veloce questo attacco, ti ho preparato la slot machine interattiva. Prova tu stesso a variare la "X" casuale e vedi quanti tentativi ti servono per sbancare il casinò!



---

![[Pasted image 20260420234335.png|241]]![[Pasted image 20260420235524.png|244]]

Analizziamo subito questa lavagna, che è il pezzo mancante perfetto per chiudere il cerchio su tutto il blocco teorico di RSA che abbiamo appena smontato. 

Il professore sta affrontando visivamente e matematicamente il "fastidioso ostacolo" di cui avevamo parlato: cosa succede se un messaggio cade fuori dal gruppo protetto?

Ecco l'analisi spietata dei tre pannelli.
### 45. Il Bersaglio (Pannello Centrale)

**Cosa c'è scritto:**
> RSA in $\mathbb{Z}_n$
> - thm 4.10
> EFT [Euler-Fermat Theorem]
> $n \sim 2^{1024} \implies p,q \sim 2^{512}$
> *(Un grande cerchio $\mathbb{Z}_n$ con un cerchio interno tratteggiato $\mathbb{Z}_n^*$. Un puntino $m$ si trova nello spazio tra i due).*

**Spiegazione e Collegamenti:**
Il prof disegna la mappa del campo di battaglia. 
* Il cerchio interno tratteggiato è $\mathbb{Z}_n^*$: la **"Zona Sicura"**. Qui dentro ci sono tutti i messaggi coprimi con $n$. Se il messaggio è qui, l'algoritmo funziona in automatico grazie al Teorema di Eulero-Fermat (EFT).
* L'anello esterno (dove si trova il punto $m$) è la **"Zona di Pericolo"** ($\mathbb{Z}_n \setminus \mathbb{Z}_n^*$). Qui ci sono i messaggi che condividono un divisore con $n$. 

Sotto, ricorda le grandezze mostruose in gioco: se la chiave pubblica $n$ è di 1024 bit, i due numeri primi segreti $p$ e $q$ sono grandi circa 512 bit ciascuno. Fa anche riferimento al **Teorema 4.10** (la dimostrazione epica che abbiamo visto due pagine fa), ricordando che l'algoritmo matematicamente non si rompe nemmeno se il messaggio finisce in quella zona di pericolo.

---

### 46. La Minaccia Letale (Pannello di Destra, estrema destra)

**Cosa c'è scritto:**
> $m \in \mathbb{Z}_n \setminus \mathbb{Z}_n^*$
> $\gcd(m,n) = \{p,q\}$
> $\downarrow$
> break RSA ($\varphi(n)$)

**Spiegazione e Collegamenti:**
Qui il prof svela perché quella zona esterna è un incubo per la sicurezza. 
Se per puro caso o per malizia tu cerchi di cifrare un messaggio $m$ che si trova nell'anello esterno, significa che quel messaggio è un multiplo di $p$ o di $q$. 
Se un hacker intercetta quel messaggio in chiaro (o ti costringe a firmarlo), gli basta calcolare il Massimo Comune Divisore tra il tuo messaggio $m$ e il tuo modulo pubblico $n$ ($\gcd(m,n)$). Questo calcolo gli sputerà fuori esattamente $p$ o $q$. 
Come abbiamo visto nel **Teorema 4.16** (la "Trinità" delle chiavi), se l'hacker trova $p$ o $q$, calcola $\varphi(n)$, poi calcola $d$, e ha "rotto RSA" (*break RSA*). 

Se questa zona è così pericolosa, perché il software non controlla ogni singolo messaggio prima di inviarlo? La risposta è nel pannello successivo.

---

### 47. Lo Scudo Statistico (Pannello di Destra, calcolo centrale)

**Cosa c'è scritto:**
> $\mathbb{P}(x \in \mathbb{Z}_n \setminus \mathbb{Z}_n^*) =$
> $\frac{n - \varphi(n)}{n} =$
> $\frac{pq - (p-1)(q-1)}{pq} =$
> $\frac{pq - pq + p + q - 1}{pq} =$
> $\frac{p + q - 1}{pq} \sim \frac{1}{q} + \frac{1}{p}$
> $= \frac{1}{2^{512}} \simeq 0$

**Spiegazione e Collegamenti:**
Questa è la giustificazione ingegneristica finale. Il professore calcola la probabilità esatta ($\mathbb{P}$) di pescare a caso un messaggio fatale che finisca nella Zona di Pericolo.
La matematica è inesorabile:
1.  Prende il numero totale di elementi ($n$) e sottrae quelli sicuri ($\varphi(n)$). Divide tutto per il totale ($n$).
2.  Sostituisce le formule: $n = pq$ e $\varphi(n) = (p-1)(q-1)$.
3.  Svolge la moltiplicazione al numeratore: i $pq$ si annullano, lasciando solo $p + q - 1$.
4.  Spezza la frazione. Il risultato finale è approssimabile a $\frac{1}{p} + \frac{1}{q}$.

Ed ecco il trionfo: siccome $p$ e $q$ sono numeri a 512 bit, quella probabilità è pari a circa $1$ diviso $2^{512}$. 
Un numero così infinitamente piccolo che, per gli standard della fisica e dell'informatica, è **matematicamente zero**. 

Ecco perché abbiamo eliminato il "fastidioso ostacolo" (il controllo di appartenenza al gruppo) visto nel **Corollario 4.5**. Non ci serve sprecare tempo di calcolo per controllare se il messaggio è sicuro, perché le probabilità di beccarne uno pericoloso sono letteralmente inferiori alle probabilità che un meteorite distrugga il tuo computer mentre premi "Invia".

La lavagna ha perfettamente fuso la teoria dei gruppi, i teoremi di correttezza e la crittografia applicata. Pronto per il prossimo argomento!