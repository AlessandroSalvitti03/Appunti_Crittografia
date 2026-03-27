## 1. Il Protocollo di Scambio (Protocol 3.1)

**Testo Originale:**

> **3. DIFFIE-HELLMAN KEY EXCHANGE**
> 
> Let $(G, \cdot)$ be a cyclic group of order $n$, let $g$ be a generator of $G$. Let us consider the following 2-party protocol between $cA$ and $cB$.
> 
> **Protocol 3.1 (Diffie Hellman key exchange protocol)**
> 
> _Public information:_ $G, n, g$
> 
> _Generation phase:_
> 
> - $cA$ choses randomly $a \in \{1, 2, \dots, n-1\}$
>     
> - $cB$ choses randomly $b \in \{1, 2, \dots, n-1\}$
>     
>     _Public phase:_
>     
> - $cA$ computes $A := g^a$ and sends it publicly to $cB$
>     
> - $cB$ computes $B := g^b$ and sends it publicly to $cA$
>     

**Cosa dice:**

Il professore definisce il campo di gioco e le prime mosse. Due computer, $cA$ (Alice) e $cB$ (Bob), si mettono d'accordo alla luce del sole su un gruppo matematico $G$ e un punto di partenza $g$. Poi, ognuno pensa a un numero segreto ($a$ per Alice, $b$ per Bob). Invece di inviarsi i segreti, li usano come esponenti per calcolare due nuovi valori "pubblici" ($A$ e $B$) che si scambiano su internet.

**Spiegazione dettagliata:**

Qui vediamo la nascita della crittografia asimmetrica. L'obiettivo è creare una cassaforte a distanza senza essersi mai incontrati prima.

La genialità sta nella _Public Phase_: Alice non manda $a$, manda $g^a$. Se un hacker è in ascolto sulla rete, intercetta i pezzi $G$, $g$, $g^a$ e $g^b$. L'hacker ha in mano i risultati delle potenze, ma per tornare indietro e scoprire i segreti $a$ e $b$ si scontra con il problema del Logaritmo Discreto, che per gruppi ben scelti è impossibile da risolvere in tempi ragionevoli.

---

## 2. L'Accordo Finale (Theorem 3.2)

**Testo Originale:**

> _Agreement phase:_
> 
> - $cA$ computes $B^a$
>     
> - $cB$ computes $A^b$
>     
>     _(Diagramma dell'incrocio con scritto "matches!")_
>     
>     **Theorem 3.2 (of DH key exchange)**
>     
>     At the end of protocol 3.1 $cA$ and $cB$ share the knowledge of $s := A^b = B^a$.
>     

**Cosa dice:**

Descrive l'ultimo passaggio per generare la password comune. Alice prende il pezzo ricevuto da Bob e lo eleva al suo segreto. Bob fa la stessa cosa con il pezzo di Alice. Il Teorema 3.2 garantisce matematicamente che, facendo così, otterranno entrambi lo stesso identico numero $s$.

**Spiegazione dettagliata:**

Questa è la vera e propria "magia" dell'algoritmo. Sfrutta una proprietà basilare delle potenze: la commutatività degli esponenti.

- Alice calcola $B^a$. Ma sappiamo che $B$ è in realtà $g^b$. Quindi Alice sta calcolando $(g^b)^a$, che fa **$g^{ba}$**.
    
- Bob calcola $A^b$. Ma sappiamo che $A$ è in realtà $g^a$. Quindi Bob sta calcolando $(g^a)^b$, che fa **$g^{ab}$**.
    
    Poiché $g^{ba}$ e $g^{ab}$ sono esattamente lo stesso numero, Alice e Bob hanno ora in mano la stessa chiave condivisa $s$. L'hacker, che ha solo spiato la rete, ha in mano $g^a$ e $g^b$, ma in algebra non c'è modo di moltiplicare quelle due basi per ottenere $g^{ab}$.
    

---

## 3. La Velocità del Sistema (Proposition 3.3)

**Testo Originale:**

> **Proposition 3.3 (Efficiency of DH key exchange)**
> 
> Let $(G, \cdot)$ a cyclic group of order $n$. For each $1 \leq a \leq n-1$, $g^a$ can be computed in $2 \log_2 a$ operations.
> 
> _Proof:_
> 
> Let $1 \leq a \leq n-1$, $a = \sum_{i=0}^t a_i 2^i$ for $a_i \in \{0,1\}$ is the binary representation of $a$. Then
> 
> $\prod_{i=0}^t (g^{2^i})^{a_i} = g^{\sum_{i=0}^t a_i 2^i} = g^a$.
> 
> So computing $g^a$ requires computing $t$ group operations, where $t$ is binary size of an integer $a$ less than $n$, so $t \leq \log_2 a$.

**Cosa dice:**

Il professore si chiede: "Ok, l'hacker non può craccare la potenza, ma per Alice e Bob calcolare queste potenze giganti è veloce?". La risposta è sì. Dimostra che il calcolo richiede al massimo un numero di operazioni pari al doppio della lunghezza in bit dell'esponente.

**Spiegazione dettagliata:**

Questo è l'algoritmo _Square and Multiply_ (Esponenziazione Veloce).

Se Alice sceglie un segreto $a$ pari a 1000, non deve fare $g \cdot g \cdot g \dots$ per 1000 volte. Il computer trasforma il numero 1000 in codice binario.

Il trucco è calcolare i "quadrati successivi" ($g^2, g^4, g^8, g^{16}\dots$) e moltiplicare tra loro solo quelli che corrispondono ai bit accesi ("1") nel numero binario.

Come si vede nell'ultima foto, il numero di moltiplicazioni necessarie crolla: dipende solo da $t$ (il numero di bit), che è microscopico rispetto al numero reale (si calcola col logaritmo in base 2). Questo assicura che il protocollo DH sia istantaneo per chi lo usa legittimamente, ma rimanga un muro impenetrabile per chi deve fare il percorso inverso.


Ripartiamo con il metodo rigoroso: trascrizione esatta, riassunto chiaro e analisi da hacker.

---

## 1. L'Esempio Pratico (Example 3.4)

**Testo Originale:**

> **Example 3.4**
> 
> Let us compute by hand $2^{257}$ in $\mathbb{Z}_{719}^*$.
> 
> We have $257 = 2^8 + 2^0 = 256 + 1$. Find $2^{256}$:
> 
> $2^0 = 1$
> 
> $2^1 = 2$
> 
> $2^2 = 4$
> 
> $2^4 = 16$
> 
> $2^8 = 16 \cdot 16 = 256$
> 
> _(...calcoli intermedi omessi/tagliati...)_
> 
> $2^{64} = \dots = 17$
> 
> $2^{128} = 17 \cdot 17 = 289$ _(nota: il professore ha scritto un numero simile)_
> 
> $2^{256} = \dots = 16$
> 
> $\implies 2^{257} = 2^{256} \cdot 2^1 = 16 \cdot 2 = 32$

**Cosa dice:**

Il professore ci fa vedere "a mano" come funziona l'algoritmo _Square and Multiply_ (Esponenziazione Veloce) che aveva teorizzato nella pagina precedente. Per calcolare una potenza apparentemente enorme come $2^{257}$, la scompone in potenze del due ($256 + 1$). Calcola i quadrati successivi ($2^2, 2^4, 2^8\dots$) calcolando sempre il resto della divisione per 719, e alla fine moltiplica tra loro solo i pezzi che gli servono ($2^{256}$ moltiplicato per $2^1$). Il risultato finale è 32.

**Spiegazione dettagliata:**

Questo è il "motore" che permette ad Alice e Bob di comunicare istantaneamente. Se tu provassi a moltiplicare il numero 2 per se stesso 257 volte, faresti 256 moltiplicazioni. Il prof, invece, calcolando "il quadrato del quadrato del quadrato", fa solo 8 elevamenti a potenza e una moltiplicazione finale. In totale **9 operazioni invece di 256**. Su numeri a 256 bit, questo trucco trasforma miliardi di anni di calcolo in una frazione di secondo. Questo dimostra che per gli utenti legittimi il protocollo è facilissimo da usare.

---

## 2. Il Problema dell'Hacker (La connessione con il DLOG)

**Testo Originale:**

> **Question:** Who else can obtain information on $s$ just accessing public information and the public interaction between $cA$ and $cB$?
> 
> It is clear that the hardness of Protocol 3.1 is related to the ability of computing discrete logarithms in the group $G$.
> 
> Before addressing the connection between the computational problem of Protocol 3.1 and the one of computing dlogs, let us give a more precise definition.

**Cosa dice:**

Il prof lancia la domanda fatidica: "Chi altro può scoprire il segreto $s$ spiando le informazioni pubbliche?". La risposta ovvia è un avversario. Sottolinea poi che la difficoltà di scassinare questo protocollo (il 3.1) è intimamente legata alla capacità di risolvere i Logaritmi Discreti (dlogs) in quel gruppo matematico. Prima di dimostrare questa connessione, annuncia che serve una definizione precisa del problema.

**Spiegazione dettagliata:**

Qui si passa dalla pratica alla teoria della sicurezza. L'hacker vede passare su internet $g^a$ e $g^b$. L'unico modo "intuitivo" che ha per trovare la chiave finale $s$ (che ricordiamo essere $g^{ab}$) è prendere una delle due metà (es. $g^a$), risolvere il logaritmo discreto per "scoperchiare" l'esponente segreto $a$, e poi usare quell'esponente segreto per elevare l'altra metà pubblica ($g^b$). Ma quanto è formalmente difficile questo problema per l'hacker? La definizione successiva serve proprio a dare un nome a questo ostacolo.

---

## 3. La Definizione Ufficiale della Sfida (CDHP)

**Testo Originale:**

> We will call **computational Diffie Hellman Problem** (CDHP) the problem where we are given a cyclic group $G$ of order $n$, a generator $g$ of $G$ and $g^a, g^b \in G$ for randomly generated $1 \leq a, b \leq n-1$ and we are asked to compute $g^{ab}$.

**Cosa dice:**

Viene battezzato ufficialmente il "Problema Computazionale di Diffie-Hellman" (CDHP). È un gioco in cui ti vengono forniti il gruppo $G$, la sua grandezza $n$, il punto di partenza $g$, e due metà pubbliche casuali $g^a$ e $g^b$. Il tuo unico obiettivo per vincere è riuscire a calcolare il valore esatto di $g^{ab}$.

**Spiegazione dettagliata:**

Questa è la formulazione matematica del lavoro di un hacker che intercetta una comunicazione. È fondamentale notare cosa **NON** viene chiesto in questo problema: la definizione non ti chiede di scoprire i segreti $a$ o $b$. Ti chiede solo di trovare il risultato finale $g^{ab}$.

Questa è una distinzione cruciale nella crittografia: l'hacker non è interessato a sapere quali numeri hanno pensato Alice e Bob; gli interessa solo trovare la chiave della cassaforte per leggere i messaggi.


## 1. La Definizione di "Impossibile" (Definition 3.5)

**Testo Originale:**

> **Definition 3.5**
> 
> A function $\varepsilon: \mathbb{N} \to \mathbb{R}$ is a _negligible function_ if $\forall c > 0 \quad \exists n_0 \in \mathbb{N} \quad \forall n > n_0$
> 
> $|\varepsilon(n)| < \frac{1}{n^c}$
> 
> We are asking that $\varepsilon(n)$ goes to zero faster than the inverse of any polynomial function.
> 
> _(Seguono un grafico con curve nere etichettate $1/x, 1/x^2, 1/x^3$ e una curva blu etichettata $\varepsilon$ che va sotto di esse)._

**Cosa dice:**

Il professore definisce matematicamente cosa significa che una probabilità è "trascurabile" (negligibile). Una probabilità $\varepsilon$ (epsilon) è considerata trascurabile se si rimpicciolisce verso lo zero molto più velocemente di qualsiasi frazione polinomiale ($1/n, 1/n^2$, ecc.) man mano che il numero $n$ diventa più grande. Il grafico mostra visivamente questa curva blu ($\varepsilon$) che si tuffa al di sotto di tutte le altre curve nere.

**Spiegazione dettagliata:**

Questo è il fondamento della sicurezza moderna. In crittografia, non diciamo mai che un sistema è "inviolabile al 100%". Diciamo che la probabilità che un hacker ci riesca è _trascurabile_.

Le curve nere ($1/x^2$, ecc.) rappresentano il tempo o le probabilità calcolabili dai computer normali. La curva blu è la probabilità di vittoria dell'hacker. Il grafico ci dice una cosa bellissima: all'inizio (sulla sinistra) l'hacker potrebbe avere qualche chance, ma se noi facciamo una password grande abbastanza (spostandoci verso destra sull'asse orizzontale), la sua probabilità di vincere precipita al di sotto delle capacità di qualsiasi computer esistente. È il modo in cui i matematici dicono "teoricamente possibile, ma praticamente impossibile prima della fine dell'universo".

---

## 2. Le Regole del Gioco per l'Hacker (Definition 3.6 - CDHP)

**Testo Originale:**

> We can now define formally the security of the CDH problem, modeled as a game between a challenger $\mathcal{C}$ and an adversary $\mathcal{A}$.
> 
> **Definition 3.6 (CDHP)**
> 
> For each a probabilistic polynomial-time algorithm $\mathcal{A}$ and for each cyclic group $G$ of order $n$ let us define the following 2-party game $\text{DH}_{G, \mathcal{A}}(n)$ between $\mathcal{A}$ and the challenger $\mathcal{C}$:
> 
> 1. $\mathcal{C}$ obtains a description of the cyclic group $G$, let $n$ be the order of $G$ and $g$ a generator;
>     
> 2. $\mathcal{C}$ chooses $1 \leq a, b \leq n-1$ uniformly at random
>     
> 3. $\mathcal{C}$ computes $g^a$ and $g^b$ in $G$
>     
> 4. $\mathcal{A}$ is given $(G, n, g, g^a, g^b)$
>     
> 5. $\mathcal{A}$ outputs $1 \leq s \leq n-1$
>     
> 6. $\mathcal{C}$ returns
>     
>     $\begin{cases} 1 & \text{if } s = g^{ab} \\ 0 & \text{otherwise} \end{cases}$
>     

**Cosa dice:**

Il professore imposta un "gioco di ruolo" rigoroso per testare se il protocollo di Diffie-Hellman è sicuro. Da una parte c'è lo Sfidante ($\mathcal{C}$, che fa la parte del sistema sicuro), dall'altra l'Avversario ($\mathcal{A}$, l'hacker con un computer normale).

Lo Sfidante prepara il tavolo (fase 1), sceglie due segreti a caso $a$ e $b$ (fase 2) e calcola le metà pubbliche $g^a$ e $g^b$ (fase 3). All'Avversario vengono consegnate solo le informazioni pubbliche (fase 4) e gli viene chiesto di produrre un'ipotesi $s$ (fase 5). Se l'ipotesi è esattamente uguale alla chiave segreta finale $g^{ab}$, l'Avversario vince (valore 1), altrimenti perde (valore 0).

**Spiegazione dettagliata:**

Questo elenco in 6 punti è la simulazione esatta di un hacker che intercetta i dati su internet. Il passaggio chiave è il **Punto 4**: guarda bene cosa riceve l'hacker. Ha la scatola ($G$), le istruzioni ($g, n$), e i lucchetti chiusi di Alice e Bob ($g^a, g^b$). **Non ha $a$ e non ha $b$.**

Il **Punto 6** è la condizione di vittoria (ovvero il disastro per la nostra privacy): l'hacker vince solo se riesce a combinare le informazioni pubbliche per trovare $g^{ab}$. Nota bene: il gioco _non_ gli chiede di scoprire i segreti originali $a$ e $b$ (quello sarebbe il problema del Logaritmo Discreto puro). Gli chiede "solo" di trovare il risultato finale dell'incastro. Se per l'Avversario vincere questo gioco ha una probabilità _trascurabile_ (come definito dal grafico precedente), allora il nostro sistema Diffie-Hellman è certificato come sicuro!


## 1. Il Sigillo di Sicurezza Ufficiale (Definition 3.7)

**Testo Originale:**

> **Definition 3.7**
> 
> We say that the CDHP is **hard** in $(G, \cdot)$, where $G$ is cyclic of order $n$, if for all probabilistic polynomial-time algorithm $\mathcal{A}$ playing the game of Definition 3.6 there exists a negligible function $\varepsilon(n)$ such that
> 
> $$P(\text{DH}_{G, \mathcal{A}}(n) = 1) < \varepsilon(n)$$
> 
> _(Segue uno schema del gioco tra Challenger $\mathcal{C}$ e Adversary $\mathcal{A}$)_
> 
> $\mathcal{C} \xrightarrow{\text{group } G, g, n} \mathcal{A}$
> 
> $a, b \in_R \{1, 2, \dots, n-1\}$ _(operazione interna di $\mathcal{C}$)_
> 
> $\mathcal{C} \xrightarrow{g^a, g^b} \mathcal{A}$
> 
> $\mathcal{C} \xleftarrow{s} \mathcal{A}$
> 
> with $s$ being the adversary $\mathcal{A}$'s guess for $g^{ab}$.

**Cosa dice:**

Questa è la fusione delle due definizioni precedenti. Il professore dichiara ufficialmente che il problema di Diffie-Hellman (CDHP) è considerato "difficile" (e quindi sicuro) in un certo gruppo matematico, se e solo se succede questo: **qualsiasi hacker ($\mathcal{A}$) che usa un computer normale ha una probabilità di vincere il gioco strettamente minore di una funzione trascurabile ($\varepsilon$)**. Sotto, lo schema riassume le mosse: il Challenger invia i parametri e i lucchetti aperti ($g^a, g^b$), l'Adversary risponde con il suo tentativo $s$.

**Spiegazione dettagliata:**

Questo è il "timbro di garanzia" della crittografia. Se un sistema matematico soddisfa questa precisa equazione, le banche e i governi lo possono usare su internet. L'hacker $\mathcal{A}$ può inventarsi gli algoritmi più creativi del mondo, può usare tutta la potenza di calcolo polinomiale che vuole, ma se il problema è "Hard", la matematica ci garantisce che la sua probabilità di azzeccare la chiave condivisa $g^{ab}$ colerà a picco verso lo zero. Lo schemino ci ricorda visivamente l'asimmetria letale dell'attacco: l'hacker riceve tantissime informazioni verso destra, ma deve sputare fuori un singolo numero esatto verso sinistra.

---

## 2. Il Ponte verso il Logaritmo Discreto

**Testo Originale:**

> to discuss the security of Protocol 3.1 (<=> the hardness of the problem in Definition 3.6) we must formally introduce the problem of computing discrete logarithms.

**Cosa dice:**

Il professore tira le somme e annuncia il prossimo capitolo. Ci sta dicendo: "Ragazzi, per poter stabilire se il protocollo di Diffie-Hellman (Protocol 3.1) è davvero sicuro, dobbiamo per forza iniziare a studiare matematicamente cos'è e come funziona il calcolo dei Logaritmi Discreti".

**Spiegazione dettagliata:**

Questa è la "chiamata alle armi" crittografica. Finora il professore ha solo costruito il castello (Diffie-Hellman) e ha stabilito le regole per dire se un castello è sicuro (il gioco CDHP). Ma come fa un hacker a provare a buttarlo giù?

Nella realtà, l'unico vero "ariete da sfondamento" che l'hacker possiede per trovare $g^{ab}$ partendo da $g^a$ è cercare di "sbucciare" l'esponente. E l'operazione che sbuccia l'esponente si chiama proprio Logaritmo Discreto. Quindi, se vogliamo capire se la nostra porta reggerà, dobbiamo studiare le armi dell'avversario.

___

## 1. Il Traduttore Imprevedibile (L'isomorfismo)

**Testo Originale:**

> **3.1 Discrete logarithms**
> 
> Our knowledge of discrete logarithms comes from the proof of theorem 2.29, where we proved that every cyclic group of order $n$ is isomorphic to $(\mathbb{Z}_n, +)$.
> 
> In particular, given $g$ a generator of $G$
> 
> $dlog : (G, \cdot) \to (\mathbb{Z}_n, +)$
> 
> $h = g^a \mapsto a$
> 
> is an isomorphism.
> 
> So far we have seen that dlog is a **natural translation** of an abstract cyclic group of order $n$ to the concrete group of integers modulo $n$ with modular addition. This translation behaves **quite randomly**, and from this it comes the interest to cryptography.
> 
> _(Segue un grafico a dispersione intitolato "First powers of 627 in $\mathbb{Z}_{941}^*$")*

**Cosa dice:**

Il professore definisce la funzione "logaritmo discreto" ($dlog$) come un traduttore perfetto (isomorfismo) che prende un'equazione difficile nel mondo delle moltiplicazioni ($h = g^a$) e ti restituisce l'esponente segreto ($a$) che vive nel mondo più semplice delle addizioni. Tuttavia, avverte che questa traduzione si comporta in modo apparentemente "casuale". Il grafico sottostante ne è la prova: tracciando le potenze, i risultati rimbalzano su e giù senza formare alcuna linea o curva leggibile. È proprio questo caos che rende la funzione utile per la crittografia.

**Spiegazione dettagliata:**

Questo foglio ci spiega visivamente _perché_ il logaritmo discreto è un'arma di difesa formidabile. Se disegni una normale funzione $y = 2^x$ su un grafico, vedi una bella curva dolce che sale. Se conosci $y$, ti basta guardare il grafico e trovare subito la $x$ corrispondente.

Con il modulo, invece, la curva si spezza. Il grafico mostrato dal professore assomiglia a una manciata di sabbia gettata su un foglio: non c'è schema, non c'è pendenza da calcolare. Se l'hacker intercetta un valore sull'asse Y, guardando questo grafico "rumoroso" non ha la minima idea di quale esponente sull'asse X l'abbia generato. L'unico modo per trovarlo è testare i punti a caso finché non inciampa in quello giusto.

---

## 2. Il Gioco del Logaritmo Discreto (Definition 3.8 - CDLP)

**Testo Originale:**

> Let us define the **computational discrete logarithm problem**: given a cyclic group $(G, \cdot)$ of order $n$, a generator $g$ of $G$, $h \in G$, determine $1 \leq a \leq n-1$ such that $g^a = h$.
> 
> As we did for CDHP, we must formalize the problem of computing dlogs and what we mean by its hardness.
> 
> **Definition 3.8 (CDLP)**
> 
> For each probabilistic polynomial-time algorithm $\mathcal{A}$ and for each cyclic group $G$ of order $n$ let us define the following 2-party game $\text{DLOG}_{G, \mathcal{A}}(n)$ between a challenger $\mathcal{C}$ and $\mathcal{A}$:
> 
> 1. $\mathcal{C}$ obtains a description of the cyclic group $G$, let $n$ be the order of $G$ and $g$ a generator;
>     
> 2. $\mathcal{C}$ chooses $1 \leq a \leq n-1$ uniformly at random
>     
> 3. $\mathcal{C}$ computes $g^a$ in $G$
>     
> 4. $\mathcal{A}$ is given $(G, n, g, g^a)$
>     
> 5. $\mathcal{A}$ output $1 \leq s \leq n-1$
>     
> 6. $\mathcal{C}$ returns
>     
>     $\begin{cases} 1 & \text{if } s = a \\ 0 & \text{otherwise} \end{cases}$
>     
>     _(Segue lo schema: $\mathcal{C}$ invia $g^a$ ad $\mathcal{A}$, e $\mathcal{A}$ risponde con $s$, "with $s$ being $\mathcal{A}$'s guess for $a$")_
>     

**Cosa dice:**

Il professore definisce ufficialmente il Problema Computazionale del Logaritmo Discreto (CDLP) come la ricerca dell'esponente $a$ partendo da $g^a$.

Poi, esattamente come aveva fatto per Diffie-Hellman, crea un "gioco" rigoroso (Definizione 3.8) per testare l'hacker. Lo Sfidante ($\mathcal{C}$) sceglie un _singolo_ segreto $a$, calcola $g^a$ e lo lancia all'Avversario ($\mathcal{A}$). L'Avversario deve tirare a indovinare e restituire un valore $s$. Vince (valore 1) solo se $s$ è esattamente l'esponente segreto originale $a$.

**Spiegazione dettagliata:**

Mettiamo a confronto questo gioco (CDLP) con quello di prima (CDHP).

- Nel gioco **CDHP** (Diffie-Hellman), l'hacker riceveva $g^a$ e $g^b$, e doveva sputare fuori la chiave condivisa **$g^{ab}$**. Non gli veniva chiesto di scoprire i segreti.
    
- In questo gioco **CDLP** (Logaritmo Discreto), l'hacker riceve un solo lucchetto $g^a$ e la condizione di vittoria è brutale: deve sputare fuori l'esatto **esponente segreto $a$**.
    

Questa è la sfida matematica per eccellenza: costringere l'hacker a percorrere l'isomorfismo all'indietro. E dal grafico caotico visto nella pagina precedente, sappiamo che non sarà una passeggiata.

___
Eccoci arrivati al momento in cui le due metà del ponte si uniscono. Il professore lega indissolubilmente il destino di Diffie-Hellman a quello del Logaritmo Discreto, attraverso un teorema fondamentale e una dimostrazione elegantissima.

---

## 1. La Definizione Parallela (Definition 3.9 e Theorem 3.10)

**Testo Originale:**

> **Definition 3.9**
> 
> We say that the computational discrete logarithm problem is **hard** in $(G, \cdot)$ if for each probabilistic polynomial-time algorithm $\mathcal{A}$ then exists a negligible function $\varepsilon(n)$ s.t.
> 
> $$P(\text{DLOG}_{G, \mathcal{A}}(n) = 1) < \varepsilon(n)$$
> 
> _(...domande e risposta informale...)_
> 
> **Theorem 3.10**
> 
> In every cyclic group $(G, \cdot)$ of order $n$, CDHP is **no harder** than CDLP. In particular, for each PPT $\mathcal{A}$ for $\text{DLOG}_G$, there exists an algorithm $\mathcal{B}$ such that
> 
> $$P(\text{DLOG}_{G, \mathcal{A}}(n) = 1) = P(\text{DH}_{G, \mathcal{B}}(n) = 1)$$

**Cosa dice:**

Prima (Def. 3.9) applica la stessa etichetta di "Sicurezza (Hard)" che aveva usato per DH anche al gioco del Logaritmo Discreto: è sicuro se nessun hacker può vincere.

Poi, lancia la bomba col Teorema 3.10: ti dimostro che Diffie-Hellman non può MAI essere più difficile del Logaritmo Discreto. Se tu possiedi un programma hacker ($\mathcal{A}$) che sa risolvere i logaritmi, io ti posso costruire un secondo programma hacker ($\mathcal{B}$) che scassina Diffie-Hellman con l'esatta, identica probabilità di successo.

**Spiegazione dettagliata:**

Questo teorema fissa una gerarchia di difficoltà tra i due problemi. Sta dicendo: "Se cade la fortezza del Logaritmo Discreto, si porta giù con sé anche la fortezza di Diffie-Hellman". È un concetto potentissimo in crittografia, che si chiama **riduzione**. Invece di dover studiare da zero come craccare DH, i matematici si possono concentrare solo sullo studiare DLOG, perché sanno che se risolvono quello, hanno automaticamente risolto anche l'altro.

---

## 2. L'Hacker "Pigro" (La Dimostrazione)

**Testo Originale:**

> **Proof**
> 
> Let $\mathcal{A}$ be a p.p.t. algorithm which solves $\text{DLOG}_G$, and let $(G, g, n, g^a, g^b)$ be the challenge of Definition 3.6 for CDHP. Let the algorithm $\mathcal{B}$ be defined as follows:
> 
> 1. Run $\mathcal{A}$ on $(G, g, n, g^a)$ and obtain $s$
>     
> 2. compute $c := (g^b)^s$
>     
> 3. return $c$
>     
>     Steps 1-2-3 are executed in polynomial-time, so also $\mathcal{B}$ is a p.p.t. algorithm, and
>     
>     $$P(\text{DH}_{G, \mathcal{B}}(n) = 1) = P(\text{DLOG}_{G, \mathcal{A}}(n) = 1)$$
>     
>     _(...spiegazione testuale del perché i due giochi restituiscono 1 contemporaneamente...)_
>     

**Cosa dice:**

Questa è la dimostrazione matematica del teorema precedente. Il prof immagina di essere l'hacker $\mathcal{B}$ che deve vincere a Diffie-Hellman, e mostra il suo piano infallibile in 3 mosse:

1. Prendo il pezzo pubblico $g^a$ e lo do in pasto al programma $\mathcal{A}$ (quello bravo a fare i logaritmi). Lui mi "sputa fuori" il numero $s$ (che dovrebbe essere l'esponente $a$).
    
2. Prendo l'altro pezzo pubblico $g^b$ e lo elevo a questo numero $s$.
    
3. Consegno il risultato allo Sfidante e vinco la partita.
    
    Poiché l'elevamento a potenza (fase 2) è velocissimo, tutto dipende dall'efficacia del programma $\mathcal{A}$.
    

**Spiegazione dettagliata:**

La bellezza di questa dimostrazione sta nella sua semplicità brutale. L'hacker $\mathcal{B}$ si comporta come uno sviluppatore moderno: non reinventa la ruota, ma usa il codice scritto da qualcun altro come una libreria esterna.

Fa finta di "non sapere" come si scassina Diffie-Hellman. Semplicemente, affida il lavoro sporco all'algoritmo $\mathcal{A}$, si fa restituire l'esponente sbucciato, e fa una banale potenza per trovare $g^{ab}$.

Se l'algoritmo $\mathcal{A}$ funziona bene (alta probabilità di successo per DLOG), allora anche $\mathcal{B}$ funzionerà benissimo (alta probabilità di successo per DH). Le due probabilità sono matematicamente incatenate.

---

## 3. Il Mistero Irrisolto e i Gruppi Bucati

**Testo Originale:**

> The two problems of computing discrete logarithms and of solving the computational Diffie-Hellman look quite different. The problem of understanding if the two problems are equivalent, in the sense of proving an equivalent of theorem 3.10 in the other direction, is **open**.
> 
> _(...)_
> 
> As far as Question 2 is concerned, we will partially address it in next section.
> 
> We can immediately spot a group where both problems are **easy**.

**Cosa dice:**

Il prof svela un grande mistero matematico: sappiamo che se DLOG è facile allora DH è facile. Ma vale il contrario? Se un giorno qualcuno trovasse un modo geniale per craccare DH senza passare per i logaritmi discreti, questo significherebbe che anche DLOG è stato craccato? La risposta è: **Nessuno lo sa. È un problema aperto.** Infine, chiude la lezione con un _cliffhanger_: ci annuncia che sta per mostrarci un gruppo matematico in cui entrambi i problemi sono una passeggiata, e quindi non andrebbe mai usato per la crittografia.

**Spiegazione dettagliata:**

Questo paragrafo è affascinante perché ti mostra i confini attuali della ricerca crittografica. I due problemi non sono "perfettamente uguali". Potrebbe esistere (forse) una scorciatoia per fondere $g^a$ e $g^b$ per ottenere $g^{ab}$ senza dover per forza scoprire i segreti $a$ e $b$. Fino a quando i matematici non risolveranno questo "Open Problem", dovremo vivere col dubbio.

L'ultima frase, invece, prepara il terreno per la rivelazione del "gruppo colabrodo" (il gruppo additivo degli interi) che smonterà nella lezione successiva.

___

Ecco che riemerge la nemesi della crittografia! Come promesso alla fine della sezione precedente, il professore analizza il "gruppo colabrodo", applicando rigorosamente la teoria che ha appena spiegato.

---

## Il Gruppo da Evitare a Tutti i Costi (Example 3.11)

**Testo Originale:**

> **Example 3.11 (an unsuitable group)**
> 
> We noticed that computing discrete logarithms given $(G, n, g)$ is equivalent to computing an isomorphism between $(G, \cdot)$ and $(\mathbb{Z}_n, +)$.
> 
> Computing this appears in general to be a challenging task, but this is not always true. Remember, for example, that the isomorphism of the CRT is efficiently computed (and inverted).
> 
> there is **for sure** for which computing the isomorphism with $(\mathbb{Z}_n, +)$ (the "translation" of the group to $(\mathbb{Z}_n, +)$) is very easy: it is $(\mathbb{Z}_n, +)$!
> 
> Computing discrete logarithms there is indeed easy.
> 
> Let $g$ be a generator of $(\mathbb{Z}_n, +)$. Then
> 
> $$g^a = h \xLeftrightarrow{\text{def}} \underbrace{g \cdot g \cdot \dots \cdot g}_{a \text{ times}} = h \xLeftrightarrow{\text{in } (\mathbb{Z}_n, +)} \underbrace{g + g + \dots + g}_{a \text{ times}} = h$$
> 
> $$\implies a \cdot g = h \pmod n$$
> 
> Now, $g$ is invertible modulo $n$ since it is a generator $\implies a = g^{-1} \cdot h$ is the discrete logarithm of $h$ computed in the time needed for computing the inverse of $g \pmod n$, that is **linear time** (Theorem 1.13).

**Cosa dice:**

Il professore ricorda che calcolare un logaritmo significa trovare un traduttore (isomorfismo) verso il mondo delle addizioni $(\mathbb{Z}_n, +)$. Ma qual è il gruppo matematico per cui questa "traduzione" verso $(\mathbb{Z}_n, +)$ è del tutto immediata? È il gruppo $(\mathbb{Z}_n, +)$ stesso!

Se tu provi a impostare l'equazione del logaritmo $g^a = h$ in questo mondo puramente additivo, il simbolo della moltiplicazione collassa e diventa una banale addizione. "Moltiplicare un numero per se stesso $a$ volte" diventa "sommare un numero a se stesso $a$ volte", che in algebra elementare si scrive $a \cdot g \pmod n$.

A questo punto, per trovare l'esponente segreto $a$, l'hacker deve solo isolarlo calcolando l'inverso di $g$ e moltiplicandolo per $h$ ($a = g^{-1} \cdot h$). Questa è un'operazione banalissima e lineare.

**Spiegazione dettagliata:**

Questo esempio distrugge l'illusione che la formula $g^a$ sia magica a prescindere. La formula è robusta _solo_ se l'ingranaggio sottostante gira con le regole giuste (moltiplicazione complessa).

Se un programmatore sprovveduto decidesse di implementare Diffie-Hellman usando un gruppo additivo base (come le ore su un orologio), regalerebbe la chiave all'hacker.

Guardiamo la formula finale: **$a = g^{-1} \cdot h$**. L'hacker conosce $g$ e conosce $h$ (sono dati pubblici). Per trovare l'inverso $g^{-1}$ deve solo lanciare l'Algoritmo di Euclide Esteso, che i computer risolvono in una manciata di nanosecondi (è di complessità lineare, non esponenziale).

___

## 1. Il Metodo dell'Uomo delle Caverne (Proposition 3.12)

**Testo Originale:**

> **3.1 First bounds on the hardness of DLOG** The most trivial algorithm for computing discrete logarithms comes from the brute-force approach and has exponential complexity. **Proposition 3.12 (brute force complexity)** Let (G,⋅) be a cyclic group of order n. Then it takes no more than n group operations to compute any discrete logarithm in G. **Proof** Let g be a generator of G. Computing discrete logarithms in G is equivalent to determine the isomorphism of theorem 2.29 with (Zn​,+). That requires computing g,g2,…,gn−1,gn=1, which takes n group operations. A solution to the problem ga=h will be obtained looking through the list of powers of g.

**Cosa dice:** Il professore inizia a definire i confini della difficoltà del Logaritmo Discreto. L'approccio più banale in assoluto è la forza bruta (brute-force). La Proposizione 3.12 ci garantisce che, in un gruppo grande n, serviranno al massimo n moltiplicazioni per trovare l'esponente segreto. La dimostrazione è disarmante nella sua ovvietà: l'hacker prende il generatore g e calcola tutta la lista delle potenze (g1,g2,g3…) una per una. Poiché il gruppo ha grandezza n, prima di arrivare alla fine del ciclo troverà sicuramente la potenza che corrisponde al bersaglio h.

**Spiegazione dettagliata:** Questa è la tattica di chi non conosce la matematica. Se hai un lucchetto a combinazione di 1000 numeri (n=1000), la forza bruta ti dice di provare le combinazioni una dopo l'altra: 000, 001, 002... e ti assicura che in massimo 1000 tentativi lo aprirai. In crittografia, significa mettere il computer a macinare potenze all'infinito. Sembra una cosa fattibile (fare n passi per scassinare n elementi sembra una proporzione 1:1, detta "lineare"), ma nasconde un trabocchetto concettuale enorme che il prof svela subito dopo.

---

## 2. La Trappola dell'Esponenziale (Remark 3.13)

**Testo Originale:**

> **Remark 3.13** If N is the size of the input, so if n=2N, then the algorithm of Proposition 3.12 has **exponential** complexity in the size of the input and requires, by Proposition 3.3, linear storage.

**Cosa dice:** Attenzione all'illusione! Il professore chiarisce che se N è la "dimensione dell'input" (il numero di bit della password), la grandezza reale della cassaforte n diventa 2N. Di conseguenza, questo algoritmo di forza bruta non è lineare, ma ha una complessità **esponenziale** rispetto all'input. Tuttavia, ha il vantaggio di usare pochissima memoria RAM (linear storage).

**Spiegazione dettagliata:** Qui è dove un sacco di programmatori si confondono. L'hacker pensa: _"Il prof ha scritto che ci metto n passi, quindi è una passeggiata!"_ Sbagliato. In informatica, la difficoltà si misura in base alla dimensione dell'input in bit (la N). Se ti do una password di soli 256 bit (N=256, un file piccolissimo, una stringa di testo insignificante), la grandezza totale delle combinazioni possibili n diventa 2256. Questo significa che il ciclo della forza bruta dovrà fare **due-alla-duecentocinquantaseiesima** operazioni. È un numero che ha più zeri degli atomi nell'universo visibile. È per questo che la curva dei tempi di calcolo schizza verso l'alto fuori dal grafico (diventa esponenziale). La forza bruta, da sola, è completamente inutile contro la crittografia moderna.

___

## 1. Il Crollo delle Certezze (La minaccia della radice quadrata)

**Testo Originale:**

> _(Grafico delle complessità: logarithmic, linear, polynomial, exponential. Sotto, le barre per PROP 3.12: TIME COMPLEXITY lunga, SPACE COMPLEXITY corta)_
> 
> By virtue of Proposition 3.12 if we want $\text{Dlog}_G$ to be hard we must at least require that $N$ is sufficiently large s.t. performing $2^N$ group operations is infeasible.
> 
> We will see now that the method of Proposition 3.12 is not the best we can do. Consequently, the request that $N$ is such that performing $2^N$ operations is infeasible would not be sufficient. We will prove that $\sqrt{2^N}$ group multiplications will be enough to compute discrete logarithms with different methods.

**Cosa dice:**

Il prof parte dal grafico per mostrarci che la forza bruta (Prop 3.12) richiede un tempo infinito (barra azzurra lunghissima) ma pochissima memoria RAM (barra rossa corta). Sembrerebbe bastare una password a $N$ bit abbastanza lunga (così che $2^N$ calcoli siano impossibili) per stare tranquilli.

Ma poi sgancia la bomba: la forza bruta _non è_ il metodo migliore. Ci sono algoritmi più furbi a cui basta fare un numero di operazioni pari alla **radice quadrata** del totale ($\sqrt{2^N}$). Quindi, la lunghezza originale della password non è più sufficiente per garantirci la sicurezza.

**Spiegazione dettagliata:**

Questo è l'incubo di ogni ingegnere della sicurezza informatica. Immagina di aver costruito una cassaforte con 256 bit di sicurezza ($2^{256}$ combinazioni). Ti senti al sicuro perché nessun computer al mondo può fare $2^{256}$ calcoli.

Ma l'hacker non usa la forza bruta. Usa un algoritmo che richiede solo la radice quadrata dei tentativi. E qual è la radice quadrata di $2^{256}$? Per le regole delle potenze, basta dividere l'esponente a metà: diventa **$2^{128}$**.

L'hacker ha appena dimezzato la tua armatura. Un attacco da $2^{128}$ operazioni è difficile, ma per governi o supercomputer inizia a diventare pericolosamente fattibile. È per questo motivo che oggi si usano chiavi a 256 bit: per sperare di avere almeno 128 bit di sicurezza _reale_ contro attacchi matematici intelligenti.

---

## 2. La Tattica dei Due Esploratori (Baby-Step / Giant-Step)

**Testo Originale:**

> One of these is the so called **baby-step / giant-step** method, which exploits the idea that a solution of $g^x = h$ can be found by finding two integers $i, j$ such that $g^i = h g^{-j}$. If $i$ and $j$ are known, then $i+j \pmod n$ is the $dlog_g(h)$.
> 
> How to find them?
> 
> the idea is to let two explorers move through the elements of the group: one moves by baby-steps exploring the cyclic groups by consecutive powers $g^i$; the other moves by giant-steps like $h g^{-j}$.
> 
> When they meet, the pair $(i, j)$ (and so the dlog) is found.
> 
> _(Segue un diagramma con una linea da 1 a $g^{n-1}$. Un esploratore blu fa passettini in avanti da 1. Un esploratore rosso fa salti lunghi all'indietro partendo da un punto. Si incontrano nel cerchio verde $g^i = h g^{-j}$)_

**Cosa dice:**

Il professore spiega la logica del BSGS con una brillante metafora. Immagina di spezzare l'equazione $g^x = h$ in due metà che si vengono incontro: $g^i = h \cdot g^{-j}$.

Per trovare dove si incrociano, mandi due "esploratori" lungo la mappa del gruppo:

1. Il primo esploratore parte dall'inizio (1) e fa passi piccoli (baby-steps), calcolando $g^1, g^2, g^3\dots$
    
2. Il secondo esploratore parte dal bersaglio ($h$) e fa salti giganti all'indietro (giant-steps), calcolando $h \cdot g^{-j}$.
    
    Quando atterrano sulla stessa identica casella ($g^i = h g^{-j}$), hai vinto. Il tuo esponente segreto $x$ è semplicemente la somma dei loro passi ($i + j$).
    

**Spiegazione dettagliata:**

Questa tecnica è nota in crittografia come attacco _Meet-in-the-Middle_ (incontrarsi a metà strada). L'hacker, invece di fare tutta la strada a piedi dal punto A al punto B (che richiederebbe troppo tempo), fa partire una ricerca simultanea da entrambi i lati.

Il trucco matematico sta nell'aver trasformato una singola equazione difficile ($g^x = h$) in una collisione tra due liste più piccole. Il passo avanti $g^i$ deve coincidere con il salto all'indietro $h \cdot g^{-j}$.

---

## 3. Il Patto col Diavolo (Time-Memory Tradeoff)

**Testo Originale:**

> This simple argument can be made more precise and it can be proven that, proceeding similarly, a solution is found in $O(\sqrt{n})$ steps, **provided that** we can memorize $O(\sqrt{n})$ data.
> 
> In particular, this is accomplished by noticing that if we divide $x$ by an integer $m$ close to $\sqrt{n}$, then $x = q m + r$ with both $q, r \leq \sqrt{n}$, so $x$ can be reconstructed by finding $q$ and $r$.

**Cosa dice:**

Il prof svela il "prezzo" da pagare per questa magia. Puoi davvero trovare la soluzione in pochissimo tempo (radice quadrata di $n$), **A PATTO CHE** tu abbia un computer con una memoria RAM grande abbastanza da memorizzare $\sqrt{n}$ dati.

Per farlo rigorosamente, l'algoritmo fa una divisione elementare: prende l'esponente incognito $x$ e lo divide per un numero $m$ grande circa come la radice quadrata di $n$. Ottiene un quoziente $q$ (che saranno i salti giganti) e un resto $r$ (che saranno i passettini piccoli). L'equazione diventa $x = qm + r$.

**Spiegazione dettagliata:**

Ecco il cuore informatico dell'attacco: il **Time-Memory Tradeoff** (Compromesso Tempo-Memoria).

Per fare in modo che i due esploratori capiscano di essersi incontrati, il primo esploratore deve _disegnare una mappa_ (salvare tutti i suoi passi nella memoria RAM del computer). Quando il secondo esploratore atterra, consulta la mappa: "Ehi, questo numero c'è già nel database?".

L'hacker scambia il tempo (di cui non ha a disposizione abbastanza per la forza bruta) con lo spazio fisico sull'hard disk/RAM.

___
## 1. Il Teorema e l'Equazione Magica (Theorem 3.14)

**Testo Originale:**

> **Theorem 3.14 (Baby-step / Giant-step, Shanks)**
> 
> Let $(G, \cdot)$ be a cyclic group of order $n$, $g \in G$ a generator of $G$. Every discrete logarithm to the base $g$ can be computed in $\mathcal{O}(\sqrt{n} \log n)$ steps using $\mathcal{O}(\sqrt{n})$ storage.
> 
> **Proof**
> 
> Let $h \in G$ and let $x = dlog_g(h)$ the value that we want to find.
> 
> Fix $m := \lfloor\sqrt{n}\rfloor + 1$ (so $m > \sqrt{n}$) and let us divide $x$ by $m$. Then
> 
> $x = q \cdot m + r$ with $0 \leq r < m$ and $q = \frac{x-r}{m} < \frac{n}{m} < \sqrt{n} < m$, so both $q$ and $r$ are less than $m$.
> 
> Now $g^x = h \implies g^{qm+r} = h \implies \mathbf{g^r = h \cdot (g^{-m})^q}$
> 
> With the goal of finding $x$, let us build two lists.
> 
> $L_1 := [g^i \mid 0 \leq i < m]$ _(list of BABY STEPS)_
> 
> $L_2 := [h \cdot (g^{-m})^i \mid 0 \leq i \leq m]$ _(list of GIANT STEPS)_
> 
> $L_1$ and $L_2$ have a common element, so a match between the two list will allow to recover $q$ and $r$, which means recovering $dlog_g(h)$ as $x = mq + r$.
> 
> Storage: $\mathcal{O}(m) = \mathcal{O}(\sqrt{n})$
> 
> Sorting $L_1$ and $L_2 = \mathcal{O}(m \log m) = \mathcal{O}(\sqrt{n} \log n)$.

**Cosa dice:**

Il Teorema 3.14 dimostra formalmente che si può craccare il logaritmo discreto in un tempo proporzionale alla radice quadrata di $n$ (con un piccolo extra dovuto all'ordinamento, il $\log n$), a patto di usare una RAM proporzionale a $\sqrt{n}$.

La prova è geniale: il computer divide l'esponente segreto $x$ per un "numero magico" $m$ (che è la radice di $n$). Ottiene un'equazione da scuole elementari: $x = qm + r$ (quoziente per $m$ più resto).

Sostituendo questa $x$ nell'equazione $g^x = h$ e spostando i pezzi, ottiene due metà separate dall'uguale: la parte sinistra dipende solo dai resti $r$ (Baby Steps), la parte destra dipende solo dai quozienti $q$ (Giant Steps). Crea due liste separate e cerca dove i numeri combaciano.

**Spiegazione dettagliata:**

Questa è l'algebra che spezza in due il lucchetto. Invece di far provare al computer le combinazioni da $1$ a $n$ in un'unica linea retta, l'hacker crea una **griglia**. I passettini $r$ sono le colonne, i salti giganti $q$ sono le righe.

Il passaggio chiave è in fondo alla pagina: il **Sorting** (ordinamento). Se tu hai due liste di un milione di numeri buttati a caso, trovare un doppione tra le due ti porta via una vita. Ma se usi un algoritmo per _ordinarle_ dal più piccolo al più grande (cosa che il computer fa in tempo $\mathcal{O}(\sqrt{n} \log n)$), trovare il match diventa istantaneo. L'hacker ha trasformato un problema di forza bruta in un problema di ricerca nei database!

---

## 2. Il Patto col Diavolo e il Codice Finale

**Testo Originale:**

> _(Grafico delle complessità con le barre B.FORCE vs BS/GS)_
> 
> Now we know that, whatever the group $(G, \cdot)$ is, in order the discrete logarithm to be hard we must at least require that $\#G$ is such that performing $2^{\#G/2}$ operations is computationally infeasible.
> 
> From the proof of theorem 3.14 we can derive the following algorithm which, given an input $h$ of $N$ bits, requires $\mathcal{O}(2^{N/2} \cdot N)$ time and $\mathcal{O}(2^{N/2})$ space to find $dlog_g(h)$.
> 
> 1. Set $m := \lfloor\sqrt{n}\rfloor + 1$
>     
> 2. Create the baby-step list $[(0, g^0), (1, g^1), (2, g^2), \dots, (m, g^m)]$
>     
> 3. Create the giant-step list $[(0, h(g^{-m})^0), (1, h(g^{-m})^1), (2, h(g^{-m})^2), \dots, (m, h(g^{-m})^m)]$
>     
> 4. Find a match $(i, g^i), (j, h \cdot (g^{-m})^j)$
>     

**Cosa dice:**

Il grafico visivo riassume il Compromesso Tempo-Memoria: la forza bruta ha una barra del Tempo (Time Complexity) azzurra enorme e una barra dello Spazio (Space Complexity) rossa microscopica. Il BS/GS accorcia drasticamente il tempo, ma allunga lo spazio richiesto.

La conseguenza per noi che ci difendiamo è severa: per essere sicuri, dobbiamo fare in modo che **la metà** della lunghezza della nostra password in bit ($N/2$) sia comunque un numero impossibile da calcolare per i computer moderni.

Infine, fornisce la ricetta in 4 step esatti che l'hacker programma sul suo terminale per eseguire l'attacco.

**Spiegazione dettagliata:**

Questo foglio ti spiega perché quando navighi su internet o usi WhatsApp, le chiavi crittografiche sono lunghe **256 bit** e non 128.

Se tu usassi una chiave da 128 bit, saresti al sicuro dalla forza bruta (perché $2^{128}$ operazioni sono impossibili). Ma l'hacker userebbe questo script di 4 righe e, grazie alla radice quadrata introdotta dall'algoritmo BS/GS, i suoi tempi di calcolo diventerebbero $\sqrt{2^{128}} = 2^{64}$.

E indovina un po'? Fare $2^{64}$ operazioni per un cluster di server moderni è questione di pochi giorni, se non ore!

Ecco perché raddoppiamo tutto a 256 bit: la radice quadrata di $2^{256}$ è $2^{128}$. Solo così l'algoritmo BS/GS dell'hacker va a sbattere contro un muro invalicabile.

Gli step numerati in basso sono letteralmente lo pseudocodice. Manca solo lo step 5 (che avrai sicuramente nella pagina successiva o tagliato fuori dalla foto), che sarà il trionfo finale dell'hacker: una volta trovati gli indici $i$ e $j$ che combaciano, calcolerà la password finale facendo semplicemente $x = i + j \cdot m$.

___
## 1. Il Bersaglio: La Cassaforte da Aprire

Sulla lavagna il prof imposta i dati del problema:

- **$p = 137$**: Il numero primo che definisce il recinto (il modulo).
    
- **$G = (\mathbb{Z}_{137}^*, \cdot)$**: Il gruppo in cui lavoriamo.
    
- **$n = \#G = 136$**: La grandezza totale dell'ingranaggio (il numero di elementi).
    
- **$g = 3$**: La nostra base (il generatore).
    
- **$h = 99$**: Il bersaglio pubblico.
    

**L'obiettivo:** Trovare l'esponente segreto $x$ tale che $3^x \equiv 99 \pmod{137}$.

---

## 2. La Strategia: Spezzare il problema (Il riferimento teorico)

Ti ricordi la formula del "Patto col Diavolo" che abbiamo visto negli appunti?

$$x = q \cdot m + r$$

Per rendere l'attacco efficiente, il prof calcola il salto magico $m$ facendo la radice quadrata di $n$:

$$m = \lfloor\sqrt{136}\rfloor + 1 = 11 + 1 = 12$$

Questo numero **12** è fondamentale: ci dice che invece di provare 136 combinazioni (Forza Bruta), ne proveremo solo **12 + 12 = 24**.

---

## 3. L'Esecuzione sullo Schermo Blu (Magma)

Lo schermo blu mostra il computer che calcola le due liste $L_1$ e $L_2$ contemporaneamente.

- **I "Baby Steps" (Lista $L_1$):** Il computer calcola $g^i \pmod{137}$ per $i$ che va da 0 a 12.
    
    - _Esempio dallo schermo:_ Iterazione 4, baby 81. Significa che $3^4 = 81$.
        
- **I "Giant Steps" (Lista $L_2$):** Il computer parte dal bersaglio $99$ e fa salti giganti all'indietro di 12 passi alla volta, calcolando $h \cdot (g^{-12})^j \pmod{137}$.
    

**Il momento del "Match":**

L'hacker (il professore) scorre le due colonne finché non vede lo stesso numero comparire in entrambe.

- Guarda l'**Iterazione 4** nella colonna "baby": il valore è **81**.
    
- Guarda l'**Iterazione 10** nella colonna "giant": il valore è **81**.
    

**Abbiamo una collisione!** I due esploratori si sono incontrati sulla casella 81.

---

## 4. La Soluzione Finale

Ora usiamo i riferimenti della lavagna per ricostruire la password segreta $x$. Abbiamo trovato che:

- $i = 4$ (il resto)
    
- $j = 10$ (il quoziente)
    
- $m = 12$ (il modulo del salto)
    

Applichiamo la formula $x = m \cdot j + i$:

$$x = 12 \cdot 10 + 4 = 120 + 4 = \mathbf{124}$$

Il professore lo scrive chiaramente in alto a destra sulla lavagna: **$x = 124$**.

Infatti, se verifichi con la calcolatrice, $3^{124} \pmod{137}$ fa esattamente **99**.