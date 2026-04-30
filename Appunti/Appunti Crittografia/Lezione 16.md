Come avevamo previsto, dopo aver fatto a pezzi il "Textbook RSA" per colpa della sua prevedibilità, il professore cala l'asso.

Ti presento il **Crittosistema ElGamal** (nato nel 1985). La sua caratteristica fondamentale? È **nativamente randomizzato**. Ogni volta che cripti lo stesso messaggio, esce un risultato completamente diverso. L'incubo dell'hacker IND-CPA.

Smontiamo questa meraviglia matematica, che si basa esattamente sulla logica di Diffie-Hellman che abbiamo visto all'inizio del corso!

---

### 1. Il Terreno di Gioco Condiviso (Domain Parameters)

**Testo Originale:**

> Unlike in RSA, where every user must have their own modulus $n$ (see A1), in the ElGamal cryptosystem there are some parameters called "domain parameters" that can be used by everyone.
> 
> Let us describe the ElGamal cryptosystem in a prime-order subgroup of $\mathbb{Z}_p^*$.

**Cosa dice:**

Ti ricordi il disastro "A1" di RSA? Se due colleghi usavano lo stesso modulo $n$, il sistema esplodeva.

In ElGamal la filosofia si ribalta: si crea un "terreno di gioco" pubblico e sicuro, chiamato **Parametri di Dominio**. Tutta la tua azienda (o addirittura tutto il mondo) può usare gli stessi identici parametri di base senza alcun rischio per la sicurezza.

Questi parametri sono tre numeri:

- **$p$** e **$q$**: Due numeri primi enormi (dove $q$ divide $p-1$).
    
- **$g$**: Un generatore di un sottogruppo di ordine $q$ (il "motore" delle nostre potenze).
    

---

### 2. La Fabbrica delle Chiavi (La Funzione `Gen`)

**Testo Originale:**

> Gen (PPT): on input $p, q, g$ returns
> 
> - the private key $sk = x \in \{0, 1, \dots, q-1\}$
>     
> - the public key $pk = h = g^x \in \langle g \rangle$
>     
>     _(Nota laterale: recovering $x$ from here takes solving a dlog in a group of order $q$)_
>     

**Cosa dice:**

La creazione delle chiavi è letteralmente la prima mossa del protocollo Diffie-Hellman!

Tu scegli un numero segreto a caso, **$x$**, e questa è la tua **chiave privata**.

Poi prendi il generatore $g$, lo elevi alla tua chiave privata, e ottieni **$h = g^x$**. Questa è la tua **chiave pubblica**, che puoi urlare ai quattro venti.

L'hacker che vede $h$ e $g$ e vuole scoprire la tua chiave privata $x$, sbatte la faccia contro il muro del **Problema del Logaritmo Discreto (dlog)**. Come abbiamo visto, senza un computer quantistico, questo muro è invalicabile.

---

### 3. La Genialata: La Chiave Usa-e-Getta (La Funzione `Enc`)

**Testo Originale:**

> Enc (PPT): on input the message $m \in \mathbb{Z}_p^*$ and the public key $h$,
> 
> and using an **ephemeral key** $k \in \mathbb{Z}_q$ (randomizer)
> 
> (needed for randomizing the encryption) and returns
> 
> $(C_1, C_2) \in \mathbb{Z}_p^* \times \mathbb{Z}_p^*$ where
> 
> $\begin{cases} C_1 := g^k \\ C_2 := m \cdot h^k \end{cases}$

**Cosa dice:**

Questo è il cuore di ElGamal. Se io voglio mandarti un messaggio $m$, non mi limito a usare la tua chiave pubblica $h$.

Prima di tutto, "lancio un dado" e genero una **chiave effimera $k$** (un numero a caso che userò _solo_ per questo specifico messaggio e poi distruggerò).

Il pacchetto cifrato che ti spedisco non è un solo numero (come in RSA), ma una **coppia di numeri $(C_1, C_2)$**:

- **$C_1$** è il generatore elevato alla mia chiave effimera ($g^k$). È come se ti stessi lasciando un indizio pubblico per farti capire quale "dado" ho tirato.
    
- **$C_2$** è il messaggio vero e proprio ($m$) moltiplicato per la tua chiave pubblica elevata alla mia chiave effimera ($h^k$).
    

**Perché è una genialata?**

Se ti mando la parola "CIAO" oggi, tirerò un dado e uscirà $k=5$. Domani ti mando di nuovo "CIAO", tirerò il dado e uscirà $k=982$. La formula userà un esponente diverso, quindi la scatola chiusa $(C_1, C_2)$ avrà un aspetto _totalmente differente_ dal giorno prima! L'hacker che ci ascolta vedrà due pacchetti dati che non si somigliano per niente, rendendo l'attacco IND-CPA matematicamente impossibile.

---

### 4. L'Apertura della Scatola (La Funzione `Dec`)

**Testo Originale:**

> Dec (deterministic): on input a ciphertext $(C_1, C_2) \in \mathbb{Z}_p^* \times \mathbb{Z}_p^*$
> 
> and the private key $x \in \mathbb{Z}_q$
> 
> returns $C_2 / C_1^x$.

**Cosa dice:**

Tu ricevi le due scatole $(C_1, C_2)$. Come fai a estrarre il messaggio $m$?

È facilissimo (e deterministico): prendi la scatola $C_2$ e la dividi per la scatola $C_1$ elevata alla tua chiave privata segreta $x$. La matematica garantisce che questa divisione annienterà tutta la casualità inserita col "dado", lasciando sul tavolo solo il messaggio in chiaro originale $m$.

Ecco la risposta alla domanda che ci eravamo fatti alla fine della pagina precedente: come fa quella divisione strana ($c_2 / c_1^x$) a distruggere la casualità del "dado" e a far riapparire magicamente il messaggio pulito?

Il professore lo dimostra qui con un'eleganza matematica assoluta. Smontiamo questo teorema e prepariamoci al colpo di scena finale della pagina!

---

### 5. La Magia della Cancellazione (Theorem 4.22)

**Testo Originale:**

> **Theorem 4.22 (Correctness of ElGamal PubKC)**
> 
> The algorithms of definition 4.21 satisfy reversibility [...] that is
> 
> $\forall m \in \mathbb{Z}_p^* \quad Dec(Enc(m, h), x) = m$, **for each choice of the randomizer**.
> 
> **Proof**
> 
> [...] $c_1 = g^k$ and $c_2 = m \cdot h^k$.
> 
> Then
> 
> $c_2 / c_1^x = \frac{m \cdot h^k}{(g^k)^x} = \frac{m \cdot g^{xk}}{g^{xk}} = m. \quad \blacksquare$

**Cosa dice:**

Il prof dimostra che la decifratura funziona **sempre**, indipendentemente da quale chiave effimera ($k$, il nostro "dado casuale") il mittente abbia scelto.

Guarda la dimostrazione, è di una semplicità disarmante:

1. Al numeratore abbiamo la seconda scatola, $c_2$, che contiene il messaggio moltiplicato per la chiave pubblica elevata alla $k$: $m \cdot h^k$.
    
2. Ma ricordiamo chi è $h$: la chiave pubblica è stata generata come $h = g^x$. Quindi possiamo sostituire $h^k$ con $(g^x)^k$, che per le regole delle potenze diventa **$g^{xk}$**. Il numeratore è quindi $m \cdot g^{xk}$.
    
3. Al denominatore abbiamo la prima scatola, $c_1$, elevata alla chiave privata $x$: $(g^k)^x$. Anche qui, le potenze si moltiplicano e otteniamo esattamente **$g^{xk}$**.
    
4. Il termine $g^{xk}$ appare identico sia sopra che sotto. Si elidono a vicenda. La casualità evapora, lasciando solo $m$.
    

**Spiegazione dettagliata:**

È letteralmente il trucco di Diffie-Hellman applicato a una spedizione postale!

Il mittente "sporca" il messaggio moltiplicandolo per un segreto condiviso ($g^{xk}$). Il destinatario, usando la sua chiave privata, riesce a calcolarsi da solo quello stesso identico segreto condiviso ($g^{xk}$) e lo usa per "pulire" il messaggio dividendo. Il dado casuale $k$ ha fatto il suo lavoro di confondere l'hacker durante il viaggio, ma si è auto-distrutto all'arrivo.

---

### 6. Lo Schiaffo Morale a RSA (L'Ultimo Paragrafo)

**Testo Originale:**

> Instead of **assuming** that the algorithms of Definition 4.21 (ElGamal PubKC) satisfy condition (5) of definition 4.1 (CPA security), like we did for RSA, we can **prove** the CPA security of ElGamal PubKC assuming that CDHP is hard in $G = \mathbb{Z}_p^*$.

**Cosa dice:**

Questa è una dichiarazione di guerra al Textbook RSA.

Ti ricordi quando per RSA il prof aveva detto in tono un po' sconfitto: _"Dobbiamo semplicemente assumere che sia sicuro perché non sappiamo dimostrarlo"_?

Con ElGamal la musica cambia completamente. Il prof dice che non ha bisogno di "assumere" che ElGamal sia CPA Secure. È pronto a **dimostrarlo matematicamente**.

L'unica cosa che dobbiamo assumere è che il problema matematico di base, il **CDHP** (Computational Diffie-Hellman Problem: trovare $g^{ab}$ conoscendo solo $g^a$ e $g^b$), sia intrattabile. Se quello tiene, ElGamal è un bunker certificato.

Per farti toccare con mano quanto sia potente la "randomizzazione" (il dado $k$) di ElGamal rispetto al determinismo noioso di RSA, ti ho preparato un simulatore. Prova a criptare lo stesso messaggio più volte cliccando su "Tira il dado": vedrai i file cifrati ($C_1, C_2$) cambiare totalmente, ma la decifratura ritroverà sempre e infallibilmente il tuo messaggio!


---

Questa lezione chiude un cerchio perfetto. Il professore sta per dimostrare quello che aveva promesso alla fine della lezione precedente: se qualcuno riesce a rompere la sicurezza CPA di ElGamal, allora quel qualcuno ha risolto il famigerato problema matematico CDHP (Computational Diffie-Hellman Problem).

Si tratta di un'altra dimostrazione per riduzione. Allacciati le cinture, smontiamo questa meraviglia logica!
### 7. Il Teorema e l'Hacker Immaginario (Theorem 4.23)

**Testo Originale:**

> **Theorem 4.23 (breaking CPA ElGamal $\implies$ solving CDHP)**
> 
> Let $\mathcal{A}$ be a PPT algorithm winning with non-negligible probability the CPA security game of ElGamal PubKC [...]
> 
> Then there exists a PPT algorithm $\mathcal{B}$ that wins the game of the CDHP (Definition 3.6) in $\langle g \rangle$ with the same probability.

**Cosa dice:**

Il teorema è netto: se cade ElGamal (sotto attacco CPA passivo), allora cade il CDHP.

Come lo dimostriamo? Inventiamo due personaggi:

- **L'Hacker $\mathcal{A}$**: Un genio criminale (o un software miracoloso) che sa leggere i messaggi cifrati con ElGamal, e ci riesce con una probabilità bella alta.
    
- **L'Hacker $\mathcal{B}$**: Un matematico che vuole disperatamente vincere la sfida CDHP (che consiste nel ricevere due numeri $g^x$ e $g^y$, e dover trovare l'impossibile valore combinato $g^{xy}$).
    

**Spiegazione dettagliata:**

L'Hacker $\mathcal{B}$ ha un piano: lui non sa risolvere il CDHP da solo, ma ha scaricato da internet il software dell'Hacker $\mathcal{A}$. Il suo obiettivo è "ingannare" il software $\mathcal{A}$, dandogli in pasto i numeri del CDHP travestiti da messaggio ElGamal, per fargli fare il lavoro sporco.

---

### 8. Il Travestimento (La Dimostrazione)

**Testo Originale:**

> **Proof**
> 
> Let $X = g^x$ and $Y = g^y$ be the challenge of the CDHP security game. Let us show how to (using $\mathcal{A}$) compute $g^{xy}$.
> 
> $\mathcal{A}$ is a PPT algorithm that, given $((c_1, c_2), h)$, will return $m$ such that $\begin{cases} c_1 = g^k \\ c_2 = m \cdot h^k \end{cases}$
> 
> We set
> 
> $\begin{cases} c_1 := Y = g^y \\ c_2 \in_R \mathbb{Z}_p^* \quad (\text{a random element}) \\ h := X = g^x \end{cases}$
> 
> and given $((c_1, c_2), h)$ to $\mathcal{A}$ we obtain $m^*$.

**Cosa dice:**

L'Hacker $\mathcal{B}$ riceve la sua sfida CDHP: i due numeri pubblici $X = g^x$ e $Y = g^y$. Vuole trovare $g^{xy}$.

Prende il software $\mathcal{A}$ e gli prepara una finta intercettazione ElGamal:

1. Come "chiave pubblica di Alice" ($h$), l'hacker $\mathcal{B}$ inserisce la prima parte della sfida CDHP: **$X$** (quindi finge che $x$ sia la chiave privata).
    
2. Come "prima parte del messaggio intercettato" ($c_1$), inserisce la seconda parte della sfida CDHP: **$Y$** (quindi finge che $y$ sia la chiave effimera "dado").
    
3. Come "seconda parte del messaggio intercettato" ($c_2$), inserisce un numero completamente a caso.
    

L'Hacker $\mathcal{B}$ preme "Invio" e il software $\mathcal{A}$ inizia a craccare, convinto di stare leggendo un messaggio di Alice. Dopo un po', il software $\mathcal{A}$ sputa fuori quello che lui crede essere il testo in chiaro decifrato: lo chiameremo $m^*$.

---

### 9. Lo Scacco Matto Matematico

**Testo Originale:**

> Notice that $m^*$ must satisfy $c_2 = m^* \cdot h^y$. (because $y$ is the exponent appearing in $c_1$)
> 
> Then computing
> 
> $\frac{c_2}{m^*} = \frac{m^* \cdot h^y}{m^*} = h^y = g^{xy}$
> 
> we win the CDHP game in $\langle g \rangle$. $\blacksquare$

**Cosa dice e cosa significa:**

L'Hacker $\mathcal{B}$ prende il finto testo in chiaro ($m^*$) sputato dal software $\mathcal{A}$ e applica una logica geniale.

Il software $\mathcal{A}$ è stato programmato per trovare quel valore $m$ che risolve l'equazione di ElGamal $c_2 = m \cdot h^k$.

Ma ricordiamoci come avevamo truccato i dati! L'esponente effimero $k$ era stato sostituito da $y$, e la chiave pubblica $h$ era stata sostituita da $X$ (cioè $g^x$).

Quindi, il software $\mathcal{A}$ ha faticosamente calcolato un $m^*$ tale che $c_2 = m^* \cdot (g^x)^y$.

A questo punto, l'Hacker $\mathcal{B}$ ha tutto quello che gli serve. Prende il numero casuale $c_2$ che aveva inventato prima e lo divide per il risultato del software $m^*$.

I due $m^*$ si semplificano. Cosa rimane? **Il puro e intatto $g^{xy}$**.

L'Hacker $\mathcal{B}$ ha vinto la sfida CDHP!

**La conclusione logica:**

Poiché crediamo fermamente che nessuno al mondo possa risolvere il CDHP, allora per forza nessuno al mondo può aver inventato il software $\mathcal{A}$. Quindi, ElGamal è matematicamente inattaccabile dal punto di vista CPA.

---

### 10. Il Trionfo Finale (La Pagella Definitiva)

**Testo Originale:**

> It can be proved that ElGamal PubKC is also **IND-CPA secure**, assuming that the **Decisional Diffie-Hellman problem (DDHP)** is hard in $\langle g \rangle$.
> 
> _(Tabella riassuntiva)_
> 
> (textbook) RSA: CPA $\color{blue}{\text{V}}$, IND-CPA $\color{red}{\text{X}}$ (CPA assumed)
> 
> ElGamal: CPA $\color{blue}{\text{V}}$ (4.23), IND-CPA $\color{blue}{\text{V}}$ (4.26) (CPA over CDHP proved, IND-CPA over DDHP proved)

**Cosa dice:**

La pagina si chiude con il confronto finale. Il prof ci anticipa che, in modo molto simile a quanto appena fatto, si può dimostrare che ElGamal batte anche l'incubo supremo: il gioco IND-CPA (quello in cui l'hacker cerca di indovinare anche solo un bit del messaggio). Lo batte basandosi su un'altra variante della matematica dei logaritmi discreti: il problema Decisionale (DDHP).

Guarda la pagella in fondo. È la vittoria su tutta la linea.

- **RSA:** Sicuro a metà, non sa gestire il test del "bit", e dobbiamo affidarci ciecamente alla fede (Assumed) che la fattorizzazione sia difficile.
    
- **ElGamal:** Sicuro al 100%, supera entrambi i test, ed entrambe le sicurezze sono state **dimostrate matematicamente (Proved)** riducendole a problemi impossibili già noti.
    

Ecco perché il mondo della crittografia adora i logaritmi discreti e Diffie-Hellman. Sono la roccia su cui è costruita la nostra sicurezza su Internet! E, come dicevi, a L'Aquila avrete sicuramente server e connessioni protette anche grazie a questi stessi principi che stiamo studiando.



---

 Il "poro" prof qui sta solo definendo matematicamente quello che ci aveva spoilerato letteralmente **nell'ultima riga della pagina precedente**!

Alla tua domanda _"Ne abbiamo già parlato o no e dove?"_, la risposta è **Assolutamente sì, ben due volte!**

1. **Ieri (o pochi minuti fa):** Alla fine della pagina scorsa, per dimostrare la pagella perfetta di ElGamal (che supera il temibile test IND-CPA), il prof aveva scritto che tutto si regge sull'assunzione che il **DDHP** (Decisional Diffie-Hellman Problem) sia intrattabile.
    
2. **All'inizio del corso (Capitolo 3):** Quando avete studiato l'accordo di chiavi di Diffie-Hellman, avevate distinto il problema _Computazionale_ (CDHP, ovvero "calcolare" il segreto $g^{ab}$) dal problema _Decisionale_ (DDHP, ovvero "riconoscere" il segreto).
    

In questa lavagna (Definition 4.24) il prof sta semplicemente descrivendo **le regole del gioco del DDHP**. Smontiamolo al volo!

### 11.Il Gioco delle Tre Carte Quantistiche (Definition 4.24)

**Cosa c'è scritto:**

Il professore definisce la "sfida" tra lo Sfidante ($\mathcal{C}$) e l'Hacker ($\mathcal{A}$) in 7 passi (riassunti poi dal diagramma in basso).

1. Lo Sfidante crea il gruppo matematico con il generatore $g$.
    
2. Lo Sfidante sceglie due segreti a caso: $a$ e $b$.
    
3. Lo Sfidante calcola i "mezzi lucchetti" pubblici: **$g^a$** e **$g^b$**.
    
4. Qui arriva il trucco: lo Sfidante lancia una moneta segreta ($z \in \{0,1\}$).
    
5. In base alla moneta, crea un terzo numero $h$:
    
    - Se esce Testa ($z=0$), calcola il **vero segreto combinato** di Diffie-Hellman: $h = g^{ab}$.
        
    - Se esce Croce ($z=1$), prende un numero **totalmente a caso** (spazzatura).
        
6. Lo Sfidante dà all'hacker i tre numeri: $(g^a, g^b, h)$. L'hacker deve guardarli e dire la sua ipotesi ($z'$). In pratica deve rispondere alla domanda: _"Questo terzo numero è il vero segreto, o mi hai rifilato spazzatura casuale?"_
    
7. Se l'hacker indovina ($z = z'$), vince 1 punto. Altrimenti 0.
    

**Spiegazione e Collegamento:**

Perché il prof ce lo sta definendo proprio ora?

Perché la sicurezza totale di ElGamal (IND-CPA) si basa proprio su questo. In ElGamal, tu mascheri il tuo messaggio moltiplicandolo per il segreto di Diffie-Hellman ($m \cdot h^k$).

Se l'hacker che guarda il pacchetto non è fisicamente in grado di distinguere il vero segreto matematico da una stringa di numeri sparati a caso (che è esattamente la definizione di DDHP che vedi sulla lavagna), allora non sarà **mai** in grado di capire quale messaggio hai criptato, nemmeno se te ne ha suggeriti due tra cui scegliere!

Insomma, il professore sta preparando il terreno per l'ultimo grande teorema: dimostrare formalmente che **DDHP intoccabile = ElGamal IND-CPA sicuro**.


---
Questa lezione è un giro sulle montagne russe. Il professore prima ci fa toccare il cielo dicendo "ElGamal è perfetto!", e subito dopo ci tira una secchiata d'acqua gelata dicendo "...ma solo se scegliete il gruppo matematico giusto, altrimenti è un disastro".

Smontiamo questa rivelazione e il nuovo strumento matematico che introduce per distruggere ElGamal!
### 12. La Promessa (Theorem 4.26)

**Testo Originale:**

> **Theorem 4.26 [informal] (breaking IND-CPA ElGamal $\implies$ solving DDHP)**
> 
> From a PPT algorithm $\mathcal{A}$ which wins the IND-CPA game of the ElGamal PubKC with non-negligible probability we can construct a PPT algorithm which wins the game of the DDHP with non-negligible probability.
> 
> **Proof**
> 
> Omitted. (easy, but we don't have time) $\blacksquare$

**Cosa dice:**

È l'esatta continuazione della slide precedente. Il prof sancisce ufficialmente che la sicurezza suprema (IND-CPA) di ElGamal dipende unicamente dalla difficoltà del problema decisionale di Diffie-Hellman (DDHP). Se il DDHP è un muro di cemento armato, ElGamal è un bunker.

La dimostrazione salta ("non abbiamo tempo"), ma il concetto è fissato. E qui arriva il colpo di scena.

---

### 13. Il Tradimento del Gruppo $\mathbb{Z}_p^*$

**Testo Originale:**

> Of course DDHP is hard in $G \implies$ CDHP is hard in $G$, and $\not\Leftarrow$. We will now show that $\mathbb{Z}_p^*$ is an example of group where CDHP is hard and **DDHP is easy**.
> 
> As a consequence, ElGamal PubKC (that can potentially be IND-CPA secure) is **not IND-CPA secure in $\mathbb{Z}_p^*$**.
> 
> There are several groups where the DDHP is hard, but there is no time to describe them in this course.

**Cosa dice:**

Mettiamo in chiaro la gerarchia: se è difficile "riconoscere" il segreto (DDHP), allora è per forza difficile anche "calcolarlo" (CDHP). Ma **non vale il contrario!** Potrebbe esistere un gruppo matematico in cui è impossibile calcolare il segreto, ma è facilissimo riconoscerlo a occhio.

E indovina un po'? Il gruppo $\mathbb{Z}_p^*$ (quello con l'asterisco, che abbiamo usato finora per tutto RSA e per l'inizio di ElGamal) **è proprio uno di questi gruppi traditori**.

In $\mathbb{Z}_p^*$, calcolare i logaritmi è difficilissimo, ma il gioco DDHP (quello di capire se un numero è spazzatura o è il vero segreto combinato) è un gioco da ragazzi.

Conclusione tragica: se implementi ElGamal usando il classico gruppo $\mathbb{Z}_p^*$, perdi istantaneamente la sicurezza IND-CPA.

---

### 14. L'Arma del Delitto: Il Simbolo di Legendre

**Testo Originale:**

> **DDHP is easy in $\mathbb{Z}_p^*$**
> 
> Let us show a PPT algorithm $\mathcal{A}$ which wins the game of the DDHP with non-negligible probability.
> 
> **Definition 4.27**
> 
> Let $p \in \mathbb{P}$. We say that an element $h \in \mathbb{Z}_p^*$ is a **quadratic residue (QR)** modulo $p \iff h = x^2 \bmod p$ for some $x \in \mathbb{Z}_p^*$.
> 
> Let us define the **Legendre symbol**
> 
> $LS(h) = \begin{cases} 1 & \text{if } h \text{ is a QR} \\ -1 & \text{if } h \text{ is not a QR (NQR)} \end{cases}$
> 
> The Legendre symbol can be computed in polynomial time.

**Cosa dice:**

Come fa l'hacker a vincere sempre a DDHP in questo gruppo? Usa la teoria dei Numeri Quadrati.

Il prof introduce il concetto di **Residuo Quadratico (QR)**. In parole povere, un numero è un "QR" se esiste la sua radice quadrata perfetta all'interno del modulo $p$. Se non ha una radice quadrata perfetta, si chiama "Non-Residuo Quadratico (NQR)".

Esiste una formula velocissima, chiamata **Simbolo di Legendre (LS)**, che funziona come uno scanner da supermercato: le dai in pasto un numero, e lei "suona" 1 se è un quadrato perfetto, e suona -1 se non lo è. Questo calcolo si fa in un millisecondo (tempo polinomiale).

**Spiegazione dettagliata e Cliffhanger:**

Come fa un banale controllo sulle radici quadrate a distruggere la crittografia?

C'è una regola nascosta nelle moltiplicazioni e nelle potenze modulari che riguarda i quadrati perfetti. L'hacker userà il Simbolo di Legendre per "scansionare" i tre numeri del gioco DDHP ($g^a$, $g^b$ e il presunto segreto $h$). Guardando solo se sono quadrati perfetti o no, capirà se $h$ è il vero risultato della moltiplicazione degli esponenti o se è un numero pescato a caso.

Nella prossima pagina vedremo la matematica esatta di questa magia. Se ce l'hai, mandala pure e vediamo come l'hacker sbanca il gioco!

---

Il professore ci mostra la matematica dietro lo "scanner" di Legendre di cui parlavamo prima. Questa pagina spiega _perché_ la formula funziona così bene e così in fretta per scovare i quadrati perfetti (QR) e i non-quadrati (NQR) all'interno del nostro maledetto gruppo $\mathbb{Z}_p^*$.

Smontiamo questa dimostrazione che è pura giocoleria con gli esponenti!

### 15. La Formula Magica (Proposition 4.28)

**Testo Originale:**

> **Proposition 4.28**
> 
> Let $p \in \mathbb{P}, p \neq 2$. Then, for each $h \in \mathbb{Z}_p^*$ we have
> 
> $LS(h) = h^{\frac{p-1}{2}} \bmod p$.

**Cosa dice:**

Questa è la formula pratica per calcolare il Simbolo di Legendre (LS).

Se vuoi sapere se un numero $h$ è un quadrato perfetto, non devi cercare la sua radice (che sarebbe lunghissimo). Ti basta prendere $h$ ed elevarlo alla potenza di $\frac{p-1}{2}$.

Il risultato di questa singola operazione sarà inesorabilmente **1** (se è un quadrato perfetto) o **-1** (se non lo è). _Nota: nel mondo dei moduli, -1 corrisponde a p-1_.

---

### 16. Metà e Metà (La Struttura del Gruppo)

**Testo Originale:**

> **Proof**
> 
> [...] let us first notice that, given $g$ a generator of $\mathbb{Z}_p^*$, and being $p-1$ even, we have
> 
> $\mathbb{Z}_p^* = \{g^0, g^1, g^2, \dots, g^{\frac{p-1}{2}-1}, g^{\frac{p-1}{2}}, g^{\frac{p-1}{2}+1}, \dots, g^{p-2}\}$
> 
> Computing the square of each element gives as the set of the QR mod p:
> 
> $QR = \{g^0, g^2, g^4, \dots, g^{-2}, g^0, g^2, g^4, \dots, g^{-2}\}$
> 
> therefore $\#QR = \frac{p-1}{2} = \#NQR$.

**Cosa dice:**

Prima di dimostrare la formula, il prof fa un'osservazione fondamentale sulla "popolazione" del gruppo $\mathbb{Z}_p^*$.

Se prendiamo tutti i numeri del gruppo e li eleviamo al quadrato (le frecce verdi che puntano in basso), scopriamo che i risultati si "ripiegano" su se stessi a metà strada.

Questo significa una cosa importantissima: **esattamente metà dei numeri nel gruppo sono Quadrati Perfetti (QR), e l'altra metà sono Non-Quadrati (NQR)**. È un 50 e 50 perfetto.

**Spiegazione dettagliata:**

Pensa a un generatore $g$. Qualsiasi numero nel gruppo può essere scritto come $g$ elevato a qualcosa ($g^i$).

Se l'esponente $i$ è **pari** (es. $g^2, g^4, g^6$), quel numero è un quadrato perfetto.

Se l'esponente $i$ è **dispari** (es. $g^1, g^3, g^5$), quel numero NON è un quadrato perfetto.

---

### 17. La Prova del Nove: Elevare alla $\frac{p-1}{2}$

**Testo Originale:**

> Assume that $h \in QR$. The above argument shows that $h = g^i$ for $i$ an even integer, so $i = 2j$. Then
> 
> $h^{\frac{p-1}{2}} = (g^{2j})^{\frac{p-1}{2}} = g^{(p-1)j} = 1 = LS(h)$.

**Cosa dice (Caso 1: Se è un quadrato):**

Se il numero $h$ è un quadrato perfetto, significa che il suo esponente nascosto è pari ($i = 2j$).

Se applichiamo la formula magica (elevando alla $\frac{p-1}{2}$), il 2 si cancella. Rimane $g^{(p-1)j}$.

Per il Teorema di Fermat, sappiamo che $g^{p-1} \equiv 1 \bmod p$. Quindi otteniamo $1^j$, che fa inesorabilmente **1**. La formula funziona.

**Testo Originale:**

> On the other hand, if $h \in NQR$, then $h = g^i$, for some odd integer $i = 2j+1$. Then
> 
> $h^{\frac{p-1}{2}} = (g^{2j+1})^{\frac{p-1}{2}} = g^{(p-1)j} \cdot g^{\frac{p-1}{2}} = g^{\frac{p-1}{2}}$.
> 
> Notice that $(g^{\frac{p-1}{2}})^2 = 1$ and recall that there exists only two roots of unity mod p [...]. Therefore $g^{\frac{p-1}{2}} \in \{-1, 1\}$, but $g^{\frac{p-1}{2}} = 1$ would violate the assumption that $g$ is a generator.
> 
> So $h^{\frac{p-1}{2}} = g^{\frac{p-1}{2}} = -1 = LS(h)$. $\blacksquare$

**Cosa dice (Caso 2: Se NON è un quadrato):**

Se il numero $h$ NON è un quadrato perfetto, il suo esponente nascosto è dispari ($i = 2j+1$).

Applicando la formula magica, otteniamo un pezzo che fa 1 (esattamente come prima), ma ci avanza un pezzo: **$g^{\frac{p-1}{2}}$**.

Che numero è $g^{\frac{p-1}{2}}$? È la radice quadrata di $g^{p-1}$ (che fa 1). Come abbiamo visto nel Teorema 4.16, nel mondo dei numeri primi ci sono solo due radici quadrate di 1: $+1$ e $-1$.

Siccome $g$ è un generatore, non può fare 1 "prima del tempo" (a metà del ciclo). Quindi, per esclusione logica schiacciante, quel pezzo avanzato **deve valere per forza -1**. La formula funziona anche qui!

Ora abbiamo l'arma. Abbiamo il Simbolo di Legendre che ci permette di distinguere in un lampo i numeri con esponente pari da quelli con esponente dispari.


---

Eccoci al gran finale. Il professore unisce i pezzi del puzzle e ci mostra come l'hacker usa il "Simbolo di Legendre" per distruggere il gioco DDHP e, di conseguenza, sventrare la sicurezza IND-CPA di ElGamal se commettiamo l'errore di usare il gruppo $\mathbb{Z}_p^*$.

È una delle dimostrazioni di hacking matematico più eleganti di tutto il corso. Smontiamo questo scacco matto!

### 18. La Regola dei Segni (Proposition 4.29)

**Testo Originale:**
> **Proposition 4.29 (LS is multiplicative)**
> Let $p \in \mathbb{P}, p \neq 2$, and let $h, k \in \mathbb{Z}_p^*$. Then
> $LS(h \cdot k) = LS(h) \cdot LS(k)$.
> **Proof**
> [...] $LS(hk) = (hk)^{\frac{p-1}{2}} = h^{\frac{p-1}{2}} \cdot k^{\frac{p-1}{2}} = LS(h) \cdot LS(k)$. $\blacksquare$

**Cosa dice:**
Prima di sferrare l'attacco, il prof dimostra un'ultima regoletta facile facile: lo scanner di Legendre è moltiplicativo. Funziona esattamente come la regola dei segni che impariamo alle scuole medie (più per più fa più, meno per meno fa più).
*   Quadrato $\cdot$ Quadrato = Quadrato ($1 \cdot 1 = 1$)
*   Non-Quadrato $\cdot$ Non-Quadrato = Quadrato ($-1 \cdot -1 = 1$)
*   Quadrato $\cdot$ Non-Quadrato = Non-Quadrato ($1 \cdot -1 = -1$)

---

### 19. L'Attacco Letale al DDHP (I 4 Passaggi)

**Testo Originale:**
> Using the result from Proposition 4.28, an attacker $\mathcal{A}$ playing the DDHP game (Definition 4.24) can proceed as follows:
> 1) compute the **parity of $a$** and **$b$** from $LS(g^a)$ and $LS(g^b)$ *(Nota: $LS(g^a) = 1 \iff a \text{ even}$)*
> 2) obtains the parity of **$ab$**
> 3) from the challenge $h$ received from $\mathcal{C}$, retrieves the parity of $h$ by computing $LS(h)$.
> 4) if parity($h$) = parity($ab$) $\to$ claim $h = g^{ab}$, otherwise claim that $h$ is the random element.

**Cosa dice:**
L'hacker riceve la sfida dal banco: ecco $g^a$, ecco $g^b$, ed ecco un numero misterioso $h$. Dimmi se $h$ è il vero $g^{ab}$ o è spazzatura casuale.
L'hacker non sa calcolare i logaritmi discreti, quindi non conoscerà mai i numeri esatti $a$ e $b$. Ma tira fuori il suo scanner e fa 4 mosse micidiali:

1.  **La Scansione:** Passa lo scanner su $g^a$. Se esce 1, sa per certo che il numero segreto $a$ è pari. Se esce -1, sa che $a$ è dispari. Fa la stessa cosa su $g^b$ per scoprire se $b$ è pari o dispari.
2.  **La Deduzione:** Ora l'hacker fa un ragionamento da prima elementare. Conosco le parità di $a$ e $b$. Come sarà la loro moltiplicazione $ab$?
    *   Pari $\cdot$ Pari = Pari
    *   Pari $\cdot$ Dispari = Pari
    *   Dispari $\cdot$ Dispari = **Dispari** (Unico caso!)
    L'hacker ora sa esattamente quale dovrebbe essere la parità del vero segreto $g^{ab}$.
3.  **Il Test sul Sospettato:** Prende il numero misterioso $h$ e gli passa sopra lo scanner di Legendre per vedere la sua parità.
4.  **La Sentenza:** Se la parità di $h$ è *diversa* da quella che aveva previsto per $ab$, l'hacker ha la prova inconfutabile: *"Mi hai rifilato spazzatura casuale!"*. Se invece la parità coincide, dice *"Questo è il vero segreto combinato $g^{ab}$!"*.

---

### 20. La Condanna del Gruppo $\mathbb{Z}_p^*$

**Testo Originale:**
> $\mathcal{A}$ wins the game with constant probability **0.75**, which is of course non-negligible.
> We conclude then that in $\mathbb{Z}_p^*$ the DDHP is not hard while CDHP it is.

**Cosa dice e cosa significa:**
Perché l'hacker vince con il **75%** di probabilità (0.75) e non col 100%?
Perché se le parità non coincidono, l'hacker ha beccato la spazzatura al 100%. Ma se le parità coincidono, c'è ancora una possibilità che il banco abbia tirato fuori un numero a caso che, per pura fortuna, aveva la stessa parità di $ab$. 
Tuttavia, vincere il 75% delle volte a un gioco in cui dovresti poter vincere solo il 50% (tirando a indovinare) significa aver totalmente distrutto il sistema.

**Conclusione:**
Il professore firma la condanna a morte. Nel gruppo "classico" $\mathbb{Z}_p^*$, calcolare il segreto (CDHP) è difficilissimo, ma riconoscerlo (DDHP) è troppo facile grazie a questo trucchetto di parità. 

Visto che ElGamal basa la sua sicurezza suprema (IND-CPA) sull'impossibilità di vincere al DDHP, **usare ElGamal in $\mathbb{Z}_p^*$ è un errore madornale**. L'hacker capirebbe subito se gli stai mandando un messaggio o un altro semplicemente guardando se il file cifrato è un quadrato perfetto o no!

*Spoiler:* Come si risolve questo casino nella vita reale? Invece di usare tutto il gruppo $\mathbb{Z}_p^*$, i crittografi si nascondono in un sottogruppo in cui *tutti* i numeri sono quadrati perfetti. Così lo scanner di Legendre suona sempre su 1 per qualsiasi numero, diventando totalmente inutile per l'hacker!

