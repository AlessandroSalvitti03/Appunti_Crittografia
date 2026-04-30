In queste due pagine abbandoniamo il problema della fattorizzazione e torniamo al nostro vecchio nemico: il **Logaritmo Discreto (DLOG)**. Stiamo per scoprire come il protocollo di Diffie-Hellman (che serviva solo per scambiarsi una chiave) è stato trasformato in un vero e proprio sistema postale blindato.

Benvenuto nel **Crittosistema ElGamal**. Smontiamolo pezzo per pezzo!

---

### 1. Il Difetto di RSA e la "Magia" del Caso (Immagine 1)

**Testo Originale:**

> **4.6 ElGamal cryptosystem**
> 
> We have seen that some of the problems of textbook RSA come from the fact that encryption is **deterministic**.
> 
> Let us now introduce a public-key cryptosystem, designed in 1985 by ElGamal, which has **natively randomized encryption**.
> 
> Unlike in RSA, where every user must have their own modulus $n$ [...], in the ElGamal cryptosystem there are some parameters called "domain parameters" that can be used by everyone.

**Cosa dice:**

Il prof svela il tallone d'Achille del "Textbook RSA". RSA è **deterministico**: se cripti la parola "CIAO" mille volte con la stessa chiave pubblica, otterrai sempre e invariabilmente lo stesso identico testo cifrato (es. "X7Y9"). Un hacker in ascolto potrebbe non sapere cosa c'è scritto, ma noterebbe che stai mandando lo stesso messaggio più volte, deducendo preziose informazioni sul tuo traffico dati.

Nel 1985, Taher ElGamal inventa un sistema **randomizzato**: se cripti "CIAO" mille volte, otterrai mille testi cifrati completamente diversi! Eppure, il destinatario riuscirà a decifrarli tutti ottenendo sempre "CIAO".

Inoltre, c'è un vantaggio logistico enorme: in RSA ogni singola persona deve generare e comunicare il proprio gigantesco modulo $n=pq$. In ElGamal, il campo di battaglia matematico ("Domain Parameters") è un **parco pubblico condiviso**. Tutti usano lo stesso modulo, ognuno sceglie solo la propria tenda segreta.

---

### 2. I Parametri di Dominio e le Chiavi (Immagine 2 - Gen)

**Testo Originale:**

> **Definition 4.21 (ElGamal public-key cryptosystem)**
> 
> Let $N$ be a security parameter. Let $p \in \mathbb{P}$ and $q \in \mathbb{P}$ be an $N$-bit number s.t. $q \mid p-1$.
> 
> Let $g \in \mathbb{Z}_p^*$ an element of order $q$ and let $p, q, g$ be the **domain parameters**.
> 
> **Gen (PPT):** on input $p, q, g$ returns
> 
> - the private key $sk = x \in \{0, 1, \dots, q-1\}$
>     
> - the public key $pk = h = g^x \in \langle g \rangle$
>     
>     _(Nota verde a lato: recovering $x$ from $h$ takes solving a DLOG in a group of order $q$)_
>     

**Cosa dice:**

Il prof definisce il "parco pubblico": un grande numero primo $p$, un sottogruppo di grandezza prima $q$ e un generatore $g$. Questi tre numeri ($p, q, g$) sono pubblici, stampati su internet per tutti.

Come fai a crearti le chiavi?

1. **Chiave Privata ($x$):** Scegli un numero a caso tra $0$ e $q-1$. Te lo tieni per te.
    
2. **Chiave Pubblica ($h$):** Prendi il generatore pubblico $g$ e lo elevi al tuo numero segreto $x$ ($h = g^x \bmod p$). Urli $h$ al mondo.
    

**Spiegazione dettagliata:**

Ti ricorda qualcosa? **È l'esatto setup di Diffie-Hellman!** La nota verde a lato te lo conferma: l'hacker conosce $g$ e conosce la tua chiave pubblica $h$. Per trovare la tua password $x$, deve risolvere l'equazione $g^x = h$. Ovvero, deve calcolare il **Logaritmo Discreto**. Se i parametri sono grandi abbastanza, l'algoritmo di Pollard's Rho ci metterà un tempo pari a $\sqrt{q}$ (radice quadrata), e se $q$ è enorme, l'hacker perde.

---

### 3. Il Dado Truccato: La Cifratura (Immagine 2 - Enc)

**Testo Originale:**

> **Enc (PPT):** on input the message $m \in \mathbb{Z}_p^*$ and the public key $h$, and using an **ephemeral key** $k \in \mathbb{Z}_q$ (randomizer) (needed for randomizing the encryption) and returns $(c_1, c_2) \in \mathbb{Z}_p^* \times \mathbb{Z}_p^*$ where
> 
> $\begin{cases} c_1 := g^k \\ c_2 := m \cdot h^k \end{cases}$

**Cosa dice:**

Io voglio mandarti un messaggio $m$. Scarico la tua chiave pubblica $h$.

Qui avviene la magia di ElGamal. Invece di fare una formula secca come RSA, io **tiro un dado a un milione di facce** e ottengo un numero casuale $k$. Questa è la mia **Chiave Effimera** (usa e getta).

A questo punto impacchetto per te non uno, ma **due numeri** (il testo cifrato è una coppia $c_1, c_2$):

- $c_1 = g^k$ (Ti mando "l'indizio" del mio lancio di dadi).
    
- $c_2 = m \cdot h^k$ (Prendo la tua chiave pubblica, la elevo al mio dado, e la moltiplico per il messaggio $m$).
    

**Spiegazione dettagliata:**

Questa operazione è brillante. Siccome la $k$ cambia ogni singola volta che invio un messaggio, il pezzo $h^k$ cambierà sempre, rendendo la scatola chiusa $c_2$ sempre diversa (come dice la nota: _"makes $c_2$ look random"_), anche se il messaggio $m$ all'interno è identico al precedente! Ecco perché sconfigge i limiti del "Textbook RSA".

---

### 4. Lo Sblocco Matematico (Immagine 2 - Dec)

**Testo Originale:**

> **Dec (deterministic):** on input a ciphertext $(c_1, c_2) \in \mathbb{Z}_p^* \times \mathbb{Z}_p^*$ and the private key $x \in \mathbb{Z}_q$
> 
> returns $c_2 / c_1^x$.

**Cosa dice:**

Ricevi i due pacchetti $c_1$ e $c_2$. Tu sei l'unico nell'universo ad avere la chiave privata $x$.

Per leggere il messaggio, prendi la scatola col messaggio ($c_2$) e la "dividi" per la scatola indizio elevata alla tua chiave privata ($c_1^x$). Fine.

**Spiegazione dettagliata (La Prova del Nove):**

Il professore lo dà per scontato negli appunti, ma facciamo il calcolo algebrico per vedere _perché_ funziona:

L'equazione di decrittazione è: $\frac{c_2}{c_1^x}$

Sostituiamo $c_1$ e $c_2$ con le loro formule originali:

$$\frac{m \cdot h^k}{(g^k)^x}$$

Per le regole delle potenze, $(g^k)^x = g^{kx}$.

Ma aspetta, la tua chiave pubblica era $h = g^x$! Quindi $h^k = (g^x)^k = g^{kx}$.

Le potenze sono letteralmente identiche! Quindi il denominatore e il moltiplicatore si annullano a vicenda:

$$\frac{m \cdot g^{kx}}{g^{kx}} = m$$

Il lucchetto si volatilizza e rimane solo il messaggio $m$ puro.

Per farti vedere con i tuoi occhi quanto è geniale la chiave "effimera" $k$, ti ho costruito un simulatore di ElGamal. Prova a criptare lo stesso messaggio due volte usando due $k$ diversi: vedrai che i pacchetti $c_1$ e $c_2$ cambiano totalmente, ma la decifratura estrae sempre il messaggio corretto!
##### <p align="right"><font color="#00b050">Chi è chi? (I protagonisti)</font></p>

Per capire la tua chiave pubblica $h$, dobbiamo guardare come nasce:

1. **$g$ (La Base):** È un numero pubblico, deciso all'inizio da tutti (come il "terreno" su cui costruisci la casa).
    
2. **$x$ (La tua Chiave Privata):** Questo è il numero che **solo tu conosci**. È il tuo segreto personale, scelto a caso. È l'esponente che tieni nascosto.
    
3. **$h$ (La tua Chiave Pubblica):** È il risultato del calcolo $g^x$. Questo numero lo mandi a tutto il mondo.
    
    - **Perché è sicuro?** Anche se tutti vedono $h$ e conoscono $g$, nessuno riesce a scoprire $x$ (perché calcolare l'esponente in una potenza è difficilissimo, è il famoso problema del logaritmo discreto).
        

---

##### <p align="right"><font color="#00b050">La formula della "magia"</font></p>

La tua confusione nasce dal fatto che il messaggio viene cifrato con la $h$ (pubblica), ma decifrato con la $x$ (privata). Guarda come si collegano:

**Il Mittente usa la tua chiave pubblica $h$:**

Il mittente non conosce la tua $x$. Lui vede solo $h$. Quindi prepara il "mascheramento" facendo:

$$h^k$$

_(Ricorda: $k$ è il numero casuale che ha scelto lui col dado)._

**Tu usi la tua chiave privata $x$:**

Quando ricevi il pacchetto $c_1$ (che è $g^k$), tu non sai cosa sia $k$, ma hai la tua $x$. Fai questo calcolo:

$$(c_1)^x = (g^k)^x = g^{kx}$$

**Perché i due conti tornano?**

Perché, per la proprietà delle potenze:

$$(g^x)^k \text{ è uguale a } (g^k)^x$$

Entrambi i calcoli portano allo stesso risultato: **$g^{kx}$**.

In sintesi:

- **$x$** è il tuo segreto (la **chiave privata**).
    
- **$h$** è la tua **chiave pubblica** ($g^x$).
    
- Il mittente usa **$h$** per "chiudere" il messaggio.
    
- Tu usi **$x$** per "aprire" l'indizio $c_1$ e annullare la chiusura.

---
In questa pagina il professore mette nero su bianco, con il rigore matematico di un teorema, l'esatto calcolo che avevamo anticipato a voce alla fine della lezione precedente.

Ma la vera "bomba" non è la formula in sé, bensì il paragrafo finale, che segna una vittoria schiacciante di ElGamal rispetto a RSA.


### 5. La Prova del Nove Ufficiale (Theorem 4.22)

**Testo Originale:**

> **Theorem 4.22 (Correctness of ElGamal pubCK)**
> 
> The algorithms of definition 4.21 satisfy reversibility (condition 4 of Definition 4.1), that is
> 
> $\forall m \in \mathbb{Z}_p^* \quad Dec(Enc(m, h), x) = m$, **for each choice of the randomizer**.
> 
> _(Segue la dimostrazione matematica)_
> 
> $\frac{c_2}{c_1^x} = \frac{m \cdot h^k}{(g^k)^x} = \frac{m \cdot g^{xk}}{g^{xk}} = m \quad \blacksquare$

**Cosa dice:**

Questo è il sigillo di garanzia (il teorema di correttezza). Il prof dimostra che l'algoritmo di decifratura restituisce sempre il messaggio originale $m$.

La riga di calcolo è esattamente quella che abbiamo sviscerato prima: si sostituisce la chiave pubblica $h$ con la sua vera identità ($g^x$), facendo collassare l'esponente di $h^k$ in $g^{xk}$. A quel punto, il denominatore (la chiave effimera $g^k$ elevata alla chiave privata $x$) diventa anch'esso $g^{xk}$. I due blocchi si disintegrano a vicenda, lasciando il messaggio $m$ intatto e pulito.

**Spiegazione dettagliata:**

Fai molta attenzione a quell'appunto scritto in verde a destra: **"for each choice of the randomizer"**. Questa è la potenza del dado truccato. Non importa quale numero casuale $k$ l'algoritmo scelga in quel millisecondo per cifrare il messaggio. L'algebra modulare fa in modo che la chiave effimera $k$ finisca sia al numeratore che al denominatore, autodistruggendosi sempre. Questo garantisce che tu possa cifrare lo stesso messaggio mille volte, ottenendo mille testi cifrati diversi, ma decifrandoli sempre in modo impeccabile.

---

### 6. La Rivincita su RSA (Il Paragrafo Finale)

**Testo Originale:**

> **Instead of assuming** that the algorithms of Definition 4.21 (ElGamal PubKC) satisfy condition (5) of definition 4.1 (CPA security), **like we did for RSA**, we can **prove** the CPA security of ElGamal pubkc assuming that CDHP is hard in $G = \mathbb{Z}_p^*$.

**Cosa dice:**

Questo è il passaggio teorico più importante del capitolo.

Ricordi la "doccia fredda" alla fine del Teorema 4.10 su RSA? Il prof ci aveva detto che la sicurezza di RSA contro gli hacker in ascolto (CPA security) _non si poteva dimostrare_, ma doveva essere semplicemente **assunta** come atto di fede (perché non sapevamo collegarla matematicamente e in modo assoluto al problema della fattorizzazione).

Con ElGamal, le regole del gioco cambiano. Il professore annuncia che non c'è bisogno di alcun "atto di fede" cieco sull'algoritmo. Possiamo letteralmente **dimostrare matematicamente** la sicurezza del sistema postale di ElGamal, a patto di assumere come vera una sola cosa: che il caro vecchio **CDHP (Computational Diffie-Hellman Problem)** sia difficile da risolvere.

**Spiegazione dettagliata:**

Ecco che il corso si chiude in un cerchio perfetto. Il CDHP è il primissimo problema irrisolvibile che abbiamo studiato (capitolo 3, quando Alice e Bob si scambiavano i colori per creare il segreto $g^{ab}$).

Il prof ci sta dicendo: _"Vi fidate che Diffie-Hellman sia sicuro? Se la risposta è sì, allora vi dimostro che anche ElGamal lo è, perché i due problemi sono matematicamente legati a doppio filo"_.

In crittografia, questa si chiama **Dimostrazione per Riduzione** ed è il Graal della sicurezza: hai collegato un sistema complesso (spedire messaggi cifrati randomizzati) a un blocco di cemento armato fondamentale (l'impossibilità di calcolare il Logaritmo Discreto di Diffie-Hellman). Se cade uno, cade l'altro. Ma finché il DLOG resiste, ElGamal è uno scudo impenetrabile.

---

 Questa è una delle dimostrazioni più eleganti di tutto il corso. Avevamo chiuso la lezione precedente con una promessa: dimostrare che ElGamal è sicuro _senza_ dover fare atti di fede, ma legandolo matematicamente all'impossibilità del CDHP (il problema di Diffie-Hellman).

Il professore qui esegue una **Dimostrazione per Riduzione** da manuale. È lo stesso identico trucco concettuale che ha usato nel Teorema 4.15 (quando l'hacker di RSA comprava il software per fattorizzare sul dark web), ma applicato a ElGamal.

Smontiamo questa magia passo dopo passo!
### 7. L'Inception Crittografico (Il Teorema 4.23)

**Testo Originale:**

> **Theorem 4.23 (breaking CPA ElGamal $\implies$ solving CDHP)**
> 
> Let $A$ be a PPT algorithm winning with non-negligible probability the CPA security game of ElGamal Pubkc...
> 
> then there exists a PPT algorithm $B$ that wins the game of the CDHP in $\langle g \rangle$ with the same probability.
> 
> _(Sotto, in verde)_
> 
> We HAVE A that breaks ElGamal: $((c_1, c_2), h) \mapsto m$
> 
> We WANT B that breaks CDHP: $(g^x, g^y) \mapsto g^{xy}$

**Cosa dice:**

Il prof imposta la sfida. Immagina che esista un hacker geniale (Algoritmo $A$) che sa craccare ElGamal. Gli dai in pasto un testo cifrato $(c_1, c_2)$ e la chiave pubblica $h$, e lui ti sputa fuori il messaggio in chiaro $m$.

Noi (Algoritmo $B$) siamo degli hacker un po' meno furbi: stiamo cercando disperatamente di risolvere il vecchio problema di Diffie-Hellman (CDHP), ovvero calcolare il segreto condiviso $g^{xy}$ avendo solo i pezzi separati $X=g^x$ e $Y=g^y$.

Il teorema dice: se io assumo l'hacker $A$, posso ingannarlo per fargli risolvere il mio problema $B$!

---

### 8. L'Inganno Perfetto (La Dimostrazione)

**Testo Originale:**

> Let $X = g^x$ and $Y = g^y$ be the challenge of the CDHP security game.
> 
> We set
> 
> $\begin{cases} c_1 := Y = g^y \\ c_2 \in_R \mathbb{Z}_p^* \quad \text{(a random element)} \\ h := X = g^x \end{cases}$
> 
> and given $((c_1, c_2), h)$ to $A$ we obtain $m^*$.
> 
> Notice that $m^*$ must satisfy $c_2 = m^* \cdot h^y$ _(because $y$ is the exponent appearing in $c_1$)_.
> 
> Then computing $\frac{c_2}{m^*} = \frac{m^* \cdot h^y}{m^*} = h^y = g^{xy}$
> 
> we win the CDHP game.

**Cosa dice e come funziona l'inganno:**

Questa è pura arte. Noi (Algoritmo $B$) abbiamo i nostri due pezzi del puzzle Diffie-Hellman: $X$ e $Y$.

Chiamiamo l'hacker $A$ (l'esperto di ElGamal) e gli diamo dei dati **falsificati**:

1. Gli diciamo che la chiave pubblica del bersaglio è $X$ (quindi per lui $h = g^x$).
    
2. Gli diciamo che la scatola dell'indizio è $Y$ (quindi per lui la chiave effimera usata è $g^y$. Significa che il dado truccato $k$ è segretamente $y$!).
    
3. Gli diamo una scatola messaggio $c_2$ riempita con **spazzatura casuale**.
    

L'hacker $A$ prende questi dati, fa la sua magia nera per rompere ElGamal, ed è convinto di aver trovato il messaggio originale. Ci restituisce un messaggio finto $m^*$.

L'hacker $A$ non lo sa, ma il suo programma per trovare $m^*$ ha dovuto obbedire alla matematica di ElGamal: ha calcolato un $m^*$ tale che $c_2 = m^* \cdot h^y$.

A questo punto noi sorridiamo, prendiamo la nostra spazzatura casuale $c_2$ e la dividiamo per la risposta dell'hacker $m^*$.

Guarda cosa succede algebricamente: i due $m^*$ si semplificano. Ci rimane in mano solo $h^y$. Ma noi avevamo impostato $h = g^x$! Quindi abbiamo in mano **$(g^x)^y = g^{xy}$**.

Abbiamo appena risolto il problema di Diffie-Hellman senza saper fare i calcoli, semplicemente sfruttando l'hacker di ElGamal come calcolatrice.

**Conclusione:** Visto che risolvere Diffie-Hellman è (fino a prova contraria) impossibile, allora deve essere impossibile anche craccare ElGamal. La cassaforte è ufficialmente blindata con un teorema.

---

### 9. La Resa dei Conti Finale: RSA vs ElGamal (Ultima Foto)

**Testo Originale:**

> It can be proven that ElGamal Pubkc is also **IND-CPA secure**, assuming that the Decisional Diffie-Hellman problem (DDHP) is hard in $\langle g \rangle$.
> 
> _(Tabella di confronto)_
> 
> **(textbook) RSA:** CPA [V] | IND-CPA [X] _(CPA assumed)_
> 
> **ElGamal:** CPA [V] | IND-CPA [V] _(CPA over CDHP proved) (IND-CPA over DDHP proved)_

**Cosa dice:**

Il professore tira le somme tirando una potentissima frecciatina a RSA. Disegna il "tabellone dei punteggi" definitivo:

- **Textbook RSA:** * Passa il test di sicurezza base (CPA), ma lo dobbiamo **assumere** per fede, perché non sappiamo legarlo alla fattorizzazione.
    
    - **FALLISCE** il test di sicurezza avanzato (IND-CPA). Perché? Lo avevamo detto nella prima pagina su ElGamal: RSA è _deterministico_. Se l'hacker ti chiede di criptare "A" e "B", e poi vede passare il criptato di "A", lo riconosce subito perché è identico. RSA da libro di testo non ha randomizzazione.
        
- **ElGamal:** * Passa il test base (CPA), e lo fa a testa alta perché lo abbiamo appena **provato** matematicamente sul CDHP.
    
    - **PASSA** anche il test avanzato (IND-CPA). Grazie alla sua chiave effimera (il famoso dado truccato $k$), ogni crittografia è diversa. L'hacker non può riconoscere due messaggi uguali. Questa sicurezza assoluta è provata su un altro problema affine (il DDHP - Decisional Diffie-Hellman Problem).
        

**In sintesi:** La matematica ha decretato un vincitore chiaro. RSA da libro di testo è fragile e poggia su basi teoriche incerte. ElGamal è randomizzato nativamente e ha fondamenta d'acciaio dimostrabili. (Nella realtà, ovviamente, usiamo versioni ultra-modificate di RSA con "Padding" per tappare questi buchi, ma da un punto di vista puramente teorico, ElGamal è il vero capolavoro).


---

Eccoci alla definizione formale del "motore" che garantisce la sicurezza assoluta (IND-CPA) di ElGamal, come ci aveva anticipato la tabella riassuntiva della lezione precedente.

Passiamo dal **CDHP** (Computational Diffie-Hellman Problem) al **DDHP** (Decisional Diffie-Hellman Problem). La differenza è sottile ma fondamentale. Smontiamo il gioco!

### 10. Riconoscere vs Calcolare (Il Concetto)

Nel CDHP (che abbiamo già studiato), l'hacker vede le chiavi pubbliche di Alice ($g^a$) e Bob ($g^b$) e deve _calcolare_ da zero il loro segreto condiviso ($g^{ab}$). È come chiedere a un ladro di fabbricare la chiave della cassaforte guardando solo la serratura.

Nel **DDHP** (quello di questa pagina), il gioco è molto più crudele. Lo Sfidante non chiede all'hacker di fare calcoli impossibili. Gli mette davanti agli occhi tre numeri e gli chiede semplicemente: _"Secondo te, questo terzo numero è il vero segreto di Alice e Bob, o è un numero totalmente a caso?"_

Se l'hacker non riesce a distinguere il vero segreto da un numero casuale, significa che per lui il segreto è indistinguibile dal rumore di fondo.

---

### 11. L'Impostazione della Trappola (I 7 Passaggi)

**Testo Originale (Definizione 4.24):**

> For each PPT algorithm $\mathcal{A}$ and for each cyclic group $G$ of order $n$ let us define the following 2-party game $\text{DDH}_{G, \mathcal{A}}(n)$ between $\mathcal{A}$ and the challenger $\mathcal{C}$:

Il prof scompone il gioco in 7 step, brillantemente riassunti dal diagramma in basso:

1. **Il Campo di Battaglia:** Lo Sfidante ($\mathcal{C}$) prepara il gruppo ciclico $G$ e il generatore pubblico $g$.
    
2. **Le Chiavi Private:** $\mathcal{C}$ sceglie due numeri segreti a caso, $a$ e $b$ (che sarebbero le chiavi private di Alice e Bob).
    
3. **Le Chiavi Pubbliche:** $\mathcal{C}$ calcola $g^a$ e $g^b$ (le chiavi pubbliche che tutti possono vedere).
    
4. **Il Lancio della Moneta (La Trappola):** $\mathcal{C}$ lancia una moneta segreta, che chiama $Z$ (può valere solo 0 o 1).
    
5. **Il Pacco per l'Hacker:** $\mathcal{C}$ invia all'Hacker ($\mathcal{A}$) un pacchetto con tre numeri: $(g^a, g^b, h)$.
    
    - Se è uscito **Testa ($Z=0$)**: $h$ è il VERO segreto condiviso ($g^{ab}$).
        
    - Se è uscito **Croce ($Z=1$)**: $h$ è un pezzo di spazzatura casuale pescato dal gruppo.
        
6. **La Scelta:** L'Hacker deve guardare i tre numeri e sputare fuori il suo tentativo $Z'$. Deve dire "Per me è Testa (Vero)" oppure "Per me è Croce (Falso)".
    
7. **Il Verdetto:** Se l'hacker indovina ($Z = Z'$), vince 1. Se sbaglia, perde (0).
    

---

### 12. Perché questo ci interessa per ElGamal?

Se ricordi la cifratura di ElGamal, il testo cifrato è composto da due pezzi:

$c_1 = g^k$ (L'indizio)

$c_2 = m \cdot h^k$ (Il messaggio nascosto)

Ma sappiamo che la chiave pubblica $h$ è uguale a $g^x$. Quindi $h^k$ è in realtà **$g^{xk}$**.

Guarda bene: abbiamo $g^x$ (la chiave pubblica), abbiamo $g^k$ (la chiave effimera $c_1$), e abbiamo $g^{xk}$ (il pezzo che maschera il messaggio). **Questo è esattamente il setup del DDHP!**

Se il problema DDHP è "difficile", significa che per un hacker la maschera $g^{xk}$ sembra un numero totalmente casuale, indistinguibile da pura spazzatura. E se la maschera sembra spazzatura casuale, anche l'intero testo cifrato $c_2$ sembrerà spazzatura casuale, garantendo la famosa **sicurezza IND-CPA** (Indistinguibilità). L'hacker non capirà mai se stai inviando "ATTACCO" o "RITIRATA", perché entrambi sembreranno sequenze di bit tirate a sorte.

Per farti capire quanto sia frustrante essere nei panni dell'hacker in questo scenario, ti ho preparato un simulatore del gioco DDHP. Provaci tu stesso: guarda le chiavi pubbliche e prova a indovinare se il terzo numero è il segreto matematicamente collegato o un falso!

---
Preparati, perché questa pagina inizia con una formale definizione statistica, passa per una scappatoia accademica esilarante, e finisce con **uno dei più grandi colpi di scena di tutto il corso**.

Il professore ci tira letteralmente il tappeto da sotto i piedi. Smontiamo il gran finale di ElGamal!


### 13. La Matematica della Fortuna (Definizione 4.25)

**Testo Originale:**

> **Definition 4.25**
> 
> We say that the DDHP is hard in $(G, \cdot)$ [...] if for all PPT algorithms $\mathcal{A}$ playing the game [...] there exist a negligible function $\epsilon$ such that
> 
> $2 \cdot |\mathbb{P}(\text{DDH}_{G, \mathcal{A}}(n) = 1) - 1/2| < \epsilon(N)$.

**Cosa dice:**

Ti ricordi il gioco del DDHP della pagina precedente? L'hacker doveva indovinare se il numero fosse il Vero Segreto o Spazzatura Casuale (Testa o Croce).

Se io non so assolutamente nulla di crittografia e tiro una moneta, ho esattamente il **50% di probabilità (1/2)** di vincere.

Questa formula matematica definisce cos'è un problema "Difficile" (Hard). Dice: prendi la probabilità di vittoria dell'hacker ($\mathbb{P}$) e togligli $1/2$. Quello che rimane è il suo "vantaggio" rispetto a uno che tira a caso.

Se il DDHP è veramente difficile, questo vantaggio deve essere **minore di $\epsilon$** (cioè praticamente zero). L'hacker, nonostante tutti i suoi supercomputer, non riesce a fare meglio di una scimmia che lancia una moneta.

---

### 14. La Promessa Mantenuta... e Subito Ignorata (Teorema 4.26)

**Testo Originale:**

> **Theorem 4.26 [informal] (breaking IND-CPA ElGamal $\implies$ solving DDHP)**
> 
> From a PPT algorithm $\mathcal{A}$ which wins the IND-CPA game [...] we can construct a PPT algorithm which wins the game of the DDHP with non-negligible probability.
> 
> **Proof**
> 
> Omitted. (easy, but we don't have time)

**Cosa dice:**

Questo è il teorema che giustifica la spunta "V" nella tabella della lezione precedente. È la dimostrazione gemella del Teorema 4.23: se riesci a craccare la sicurezza assoluta di ElGamal (IND-CPA), significa che sai risolvere il DDHP. Le due cose sono matematicamente legate.

E la dimostrazione? Il prof ci regala un classico momento universitario: _"È facile, ma non abbiamo tempo, la saltiamo"_. Ma a noi va bene così, il concetto logico ormai lo abbiamo interiorizzato.

---

### 15. IL COLPO DI SCENA: Il Gruppo Traditore (Ultimo Paragrafo)

**Testo Originale:**

> Of course DDHP is hard in $G \implies$ CDHP is hard in $G$, and $\not\Leftarrow$.
> 
> We will now show that $\mathbb{Z}_p^*$ is an example of group where CDHP is hard and DDHP is easy.
> 
> As a consequence, ElGamal Pubkc (that can potentially be IND-CPA secure) **is not IND-CPA secure in $\mathbb{Z}_p^*$**.
> 
> There are several groups where the DDHP is hard, but there is no time to describe them in this course.

**Cosa dice e perché è devastante:**

Fermati a leggere bene questo paragrafo, perché distrugge l'illusione di sicurezza in cui cullavamo da tre lezioni.

1. **La Gerarchia:** Il prof chiarisce una regola logica. Se per te è difficile persino _riconoscere_ il segreto (DDHP), allora sarà sicuramente impossibile _calcolarlo_ (CDHP). Ma attenzione al simbolo barrato ($\not\Leftarrow$): **il contrario non è vero!** Potrebbe esserci un mondo in cui calcolare il segreto è impossibile, ma riconoscerlo a colpo d'occhio è facilissimo.
    
2. **Il Tradimento di $\mathbb{Z}_p^*$:** Qual è il gruppo matematico che abbiamo usato fin dal primo giorno per Diffie-Hellman e per ElGamal? Il gruppo $\mathbb{Z}_p^*$. Il prof ci butta addosso una doccia ghiacciata: in questo gruppo specifico, calcolare il logaritmo (CDHP) è difficilissimo, ma **riconoscere se un numero è il segreto o no (DDHP) È FACILE**.
    
3. **Il Crollo di ElGamal:** E qual è la conseguenza? Siccome per avere la sicurezza totale (IND-CPA) ElGamal ha disperatamente bisogno che il DDHP sia difficile, se lo facciamo girare sul classico gruppo $\mathbb{Z}_p^*$, **ElGamal diventa vulnerabile**. Un hacker attivo può craccarlo!
    

**In sintesi:** Il "Textbook ElGamal", proprio come il "Textbook RSA", ha un difetto fatale nella sua versione base. Per renderlo davvero sicuro e inespugnabile, non possiamo usare il normale gruppo $\mathbb{Z}_p^*$. Dobbiamo usare dei "sottogruppi" speciali o passare direttamente a un tipo di matematica molto più avanzata, come le **Curve Ellittiche** (che probabilmente esulano da questo corso).

Il messaggio finale del professore è una lezione di vita per ogni crittografo: _non basta avere una formula matematicamente perfetta se poi la fai girare sul campo di battaglia sbagliato._

Se c'è un'altra pagina in cui ci fa vedere _come_ un hacker riesce a riconoscere facilmente il segreto in $\mathbb{Z}_p^*$, mandala pure: è il momento di fare danni!