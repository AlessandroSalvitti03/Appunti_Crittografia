Questa nuova lezione è il "dietro le quinte" di quello che abbiamo visto la volta scorsa. Il professore si è fermato e ha detto: _"Vi ho detto che l'algoritmo Baby-Step Giant-Step funziona creando due liste grandi $\sqrt{n}$. Ma perché proprio la radice quadrata? Chi ce lo assicura?"_

Ecco la dimostrazione matematica del famoso **Paradosso del Compleanno**, che in crittografia prende il nome di "Algoritmi di Collisione".

---

### 1. Il Problema della Scatola (Introduzione alle Collisioni)

**Testo Originale:**

> **3.3 Collision Algorithms**
> 
> Let us consider the following problem.
> 
> We are given a box with $n$ different numbers and we build a list by choosing $m_1$ numbers from the box.
> 
> After one number is selected, it is put back in the box.
> 
> We have built a list $L_1$ with $m_1$ numbers.
> 
> Then we do the same with a list $L_2$ of $m_2$ numbers.
> 
> Have $L_1$ and $L_2$ a common element?
> 
> Very likely, if both $m_1$ and $m_2$ are slightly larger than the $\sqrt{n}$.

**Cosa dice:**

Il prof introduce un esperimento mentale. Immagina un'urna (la cassaforte) con $n$ palline numerate. Estrai una pallina, te la segni, e la ributti dentro (estrazione con reinserimento). Fai questa cosa $m_1$ volte e crei la Lista 1. Poi lo rifai $m_2$ volte e crei la Lista 2.

Qual è la probabilità che ci sia un numero in comune (una collisione) tra le due liste? La risposta intuitiva è: altissima, a patto che la lunghezza delle due liste superi di poco la radice quadrata del totale $n$.

**Spiegazione dettagliata:**

Questo è l'esatto richiamo all'algoritmo di **Shanks (Baby-Step Giant-Step)**! Ti ricordi le due liste che il computer compilava sullo schermo blu (Magma)? La $L_1$ erano i passettini, la $L_2$ erano i salti giganti. Il professore sta ponendo le basi per dimostrare scientificamente perché quel trucco di incrociare le liste spezza in due i tempi di calcolo dell'hacker, rendendo letale un attacco con complessità $\mathcal{O}(\sqrt{n})$.

---

### 2. I Confini del Caso (Theorem 3.15)

**Testo Originale:**

> **Theorem 3.15 (Collision theorem)**
> 
> Let $S$ be a set with $n$ elements and let us choose independently and at random some elements from $S$.
> 
> For $m \le n$ let $E_{n,m}$ be the event
> 
> $E_{n,m} = $ the first $m$ elements taken from $S$ are all distinct.
> 
> Then
> 
> $$1 - \frac{(m-1)m}{2n} \le P(E_{n,m}) \le \exp\left(-\frac{(m-1)m}{2n}\right)$$
> 
> Therefore, being $Coll_{n,m}$ the event
> 
> $Coll_{n,m} = $ there is a repetition in the first $m$ elements taken from $S$,
> 
> we have
> 
> $$1 - \exp\left(-\frac{(m-1)m}{2n}\right) \le P(Coll_{n,m}) \le \frac{(m-1)m}{2n}$$

**Cosa dice:**

Questo è il Teorema delle Collisioni. Definisce due eventi opposti quando peschiamo $m$ elementi da un totale di $n$:

- **$E_{n,m}$ (Nessuna collisione):** Tutti i numeri estratti sono diversi.
    
- **$Coll_{n,m}$ (Collisione):** C'è almeno un "doppione".
    
    Il teorema non ci dà la probabilità esatta (che sarebbe una formula lunghissima da calcolare), ma ci dà un **pavimento** e un **soffitto** (lower bound e upper bound). Ci dice che la probabilità di trovare un doppione è incastrata tra un valore esponenziale e una frazione molto semplice.
    

**Spiegazione dettagliata:**

Il senso di questa formula è fornire all'hacker uno strumento per calcolare velocemente le sue probabilità di successo. Guarda il numeratore in quelle frazioni: $(m-1)m$. In matematica, $m \cdot m$ fa $m^2$. Se $m$ è la radice quadrata di $n$ ($m \approx \sqrt{n}$), allora il numeratore $m^2$ diventa circa uguale a $n$. E se hai $n$ sopra e $n$ sotto nella frazione, si semplificano! Questo dimostra che quando la tua lista $m$ si avvicina a $\sqrt{n}$, l'esponente diventa una costante e la probabilità di trovare un doppione schizza subito verso l'alto (come nel paradosso dove bastano 23 persone in una stanza per avere il 50% di probabilità che due compiano gli anni lo stesso giorno, anche se i giorni sono 365).

---

### 3. La Dimostrazione - Parte 1: Il Soffitto (Upper Bound)

**Testo Originale:**

> **Proof**
> 
> Since all the extractions are independent and equally likely, we can choose the first element in $n$ ways, the second in $n-1$ ways and so on, therefore
> 
> $$P(E_{n,m}) = \left(\frac{n-1}{n}\right) \left(\frac{n-2}{n}\right) \cdots \left(\frac{n-(m-1)}{n}\right)$$
> 
> $= \left(1 - \frac{1}{n}\right) \left(1 - \frac{2}{n}\right) \cdots \left(1 - \frac{m-1}{n}\right)$
> 
> Remember that
> 
> $\forall x \in \mathbb{R} \quad 1-x \le e^{-x}$, and so
> 
> $$P(E_{n,m}) \le e^{-1/n} \cdot e^{-2/n} \cdot \cdots \cdot e^{-\frac{m-1}{n}}$$
> 
> $$= \exp\left(-\frac{1}{n} \frac{(m-1)m}{2}\right)$$
> 
> which proves the upper bound.

**Cosa dice:**

Qui il prof dimostra la parte destra della formula per l'evento "nessuna collisione" ($E_{n,m}$).

Calcola la probabilità pescando i numeri uno a uno: il primo è sicuro (probabilità 1), per il secondo hai $n-1$ numeri sicuri su $n$, per il terzo ne hai $n-2$ su $n$, eccetera. Moltiplica tutte queste frazioni.

Per semplificare questo "treno" lunghissimo di moltiplicazioni, usa un trucco magico dell'Analisi Matematica: la disuguaglianza $1-x \le e^{-x}$. Sostituisce ogni parentesi con un esponenziale ($e$), poi somma gli esponenti usando la formula di Gauss per la somma dei numeri interi da $1$ a $m-1$, ottenendo $\frac{(m-1)m}{2}$.

**Spiegazione dettagliata:**

Questo è "hacking matematico" di altissimo livello. Cercare di moltiplicare mille frazioni diverse scritte come $(1 - x)$ è un incubo anche per un computer. Ma trasformandole in $e^{-x}$, per le regole delle potenze, le moltiplicazioni diventano **addizioni** tra gli esponenti.

Questo passaggio è fondamentale perché trasforma una formula impraticabile in una formula elegante e facilissima da inserire nei calcoli della complessità algoritmica. Abbiamo appena piazzato il "soffitto".

---

### 4. La Dimostrazione - Parte 2: Il Pavimento (Lower Bound)

**Testo Originale:**

> to prove the lower bound, let us consider the complementary event
> 
> $Coll_{n,m} = E_{n,m}^c$. Notice that
> 
> $Coll_{n,m} = \bigcup_{i=1}^m Coll_{n,m,i}$, where
> 
> $Coll_{n,m,i} = $ the $i$-th element is a repetition,
> 
> and $P(Coll_{n,m,i}) \le \frac{i-1}{n}$. We can compute
> 
> $$P(E_{n,m}) = 1 - P(Coll_{n,m})$$
> 
> $$= 1 - P\left(\bigcup_{i=1}^m Coll_{n,m,i}\right)$$
> 
> $$\ge 1 - \sum_{i=1}^m P(Coll_{n,m,i})$$
> 
> $$\ge 1 - \sum_{i=1}^m \frac{i-1}{n}$$
> 
> $$= 1 - \frac{1}{n} \sum_{i=1}^m (i-1)$$
> 
> $$= 1 - \frac{(m-1)m}{2n} \qquad \blacksquare$$

**Cosa dice:**

Per trovare il "pavimento", il prof usa la logica inversa: calcola l'evento complementare (cioè la probabilità che ci _sia_ una collisione).

Spezza l'evento generale in tanti micro-eventi ($Coll_{n,m,i}$): "l'estrazione numero $i$ è la prima volta che esce un doppione". La probabilità che l'estrazione $i$ sia un doppione è al massimo $\frac{i-1}{n}$ (perché fino a quel momento hai pescato $i-1$ numeri diversi).

Poi applica una regola della probabilità (Union Bound): la probabilità di un'unione di eventi è sempre minore o uguale alla somma delle singole probabilità. Siccome sta sottraendo questo blocco da 1, il segno "minore o uguale" ($\le$) si ribalta e diventa "maggiore o uguale" ($\ge$). Somma di nuovo i numeri da $1$ a $m-1$ con la regola di Gauss e ottiene la formula finale. Il quadratino nero ($\blacksquare$) segna la fine trionfale della dimostrazione.

**Spiegazione dettagliata:**

La chiave di quest'ultimo pezzo si chiama **Disuguaglianza di Boole** (o Union Bound). L'hacker sa che ci sono mille modi diversi per avere una collisione (al 2° tentativo, al 3°, al 100°). Invece di calcolare l'esatta sovrapposizione di tutti questi scenari intricati (che richiederebbe calcoli combinatori atroci), "somma brutalmente" le probabilità massime di ogni singolo scenario.

Siccome questa somma brutale è sicuramente una sovrastima, se tu sottrai questa sovrastima dal 100%, quello che ti rimane è garantito essere il **minimo assoluto** (il pavimento) della tua probabilità.

Con queste pagine, il professore ha blindato le fondamenta matematiche. Ora sappiamo con assoluta certezza che quando un hacker crea due liste di grandezza $\sqrt{n}$, la probabilità di fare "Bingo" è vicinissima a 1.

Agli ordini! Questa è in assoluto la mia parte preferita. Il professore sta per svelare l'**Algoritmo $\rho$ (Rho) di Pollard**, il vero capolavoro crittografico che distrugge la cassaforte senza usare un solo Megabyte di RAM.

Smontiamo queste quattro immagini seguendo il nostro metodo implacabile.

---

### 5. La Ricetta dell'Hacker (Lemma 3.18)

**Testo Originale:**

> **Pollard's $\rho$ Algorithm**
> 
> **Lemma 3.18**
> 
> Let $(G, \cdot)$ be a cyclic group of order $n$, $g$ a generator and $h \in G$. If there exist $a,b,c,d \in \mathbb{Z}$ such that
> 
> 1. $g^a h^b = g^c h^d$,
>     
> 2. $b-d \neq 0$,
>     
> 3. $\gcd(b-d, n) = 1$.
>     
>     Then $dlog_g(h)$ is easy to compute (linear time).
>     
>     **Proof**
>     
>     Let $a,b,c,d \in \mathbb{Z}$ such that $g^a h^b = g^c h^d$, then $g^{a-c} = h^{d-b}$.
>     
>     Since $\gcd(b-d, n) = 1$, we have $(d-b)\alpha + \beta n = 1$ for some $\alpha, \beta \in \mathbb{Z}$. Then
>     
>     $g^{(a-c)\alpha} = h^{(d-b)\alpha} = h^{1-\beta n} = h$,
>     
>     therefore $(a-c)\alpha = dlog_g(h)$.
>     

**Cosa dice:**

Il professore ci dà la "formula d'oro". Se l'hacker riesce, in qualche modo magico, a trovare quattro numeri ($a, b, c, d$) che creano un'uguaglianza incrociando $g$ e $h$, ha vinto.

La dimostrazione algebrica fa spavento ma il concetto è semplice: sposta le $g$ da una parte e le $h$ dall'altra ($g^{a-c} = h^{d-b}$). Visto che il Massimo Comune Divisore è 1, l'hacker calcola l'inverso modulare $\alpha$ (usando l'algoritmo di Euclide), sbuccia l'esponente di $h$, e si ritrova l'esponente segreto servito su un piatto d'argento: $x = (a-c)\alpha$.

**Spiegazione dettagliata:**

Questo Lemma ci dice una cosa fondamentale: l'hacker non ha più bisogno di calcolare due liste mastodontiche e incrociarle come nel Baby-Step Giant-Step. Gli basta vagare per il gruppo matematico combinando $g$ e $h$ a caso, finché non inciampa in **una singola equazione** che "sbatte" contro se stessa (una collisione interna). Se trova quella, l'algebra fa il resto in una frazione di secondo.

---

### 6. La Trappola Inevitabile (La forma a $\rho$)

**Testo Originale:**

> **Abstract formulation**
> 
> Let $S$ be a set and $f: S \to S$ be a random function.
> 
> Let us consider the following sequence of elements
> 
> $x_0 \in S$, $x_1 = f(x_0)$, $x_2 = f(x_1) \dots x_{i+1} = f(x_i) \dots$
> 
> forming a so-called **pseudo-random walk** in $S$.
> 
> Since $S$ is finite, $\mathcal{O}$ must contain a repetition and therefore a cycle. **Goal: finding $i \neq j$ such that $x_i = x_j$.**
> 
> _(Segue un diagramma geniale intitolato "$\rho$-shaped graph": una linea dritta chiamata TAIL che finisce in un cerchio chiuso chiamato LOOP)_.
> 
> Let us define:
> 
> $T-1 :=$ the index of the last element which appears only once.
> 
> $M :=$ the smallest index such that $x_T = x_{T+M}$.
> 
> We will soon show that if $\#S = n$, then $T+M \sim \sqrt{n}$, therefore after computing $\mathcal{O}(\sqrt{n})$ elements we must expect a collision.

**Cosa dice:**

Il professore abbandona i numeri per un attimo e fa un disegno. Immagina di lanciare un esploratore in una foresta chiusa ($S$ finito) usando una mappa che lo fa muovere a caso ($f$). L'esploratore inizia a camminare: passo 0, passo 1, passo 2...

Siccome la foresta è finita, a furia di camminare a caso, l'esploratore finirà **inevitabilmente** per rimettere il piede su un'orma che aveva già lasciato in precedenza. Da quel momento in poi, sarà bloccato in un cerchio infinito (Loop).

Il percorso disegnato assomiglia alla lettera greca **$\rho$ (Rho)**. La matematica del Paradosso del Compleanno ci garantisce che l'esploratore cadrà in questo loop dopo appena $\sqrt{n}$ passi.

**Spiegazione dettagliata:**

Questo è il motivo per cui l'algoritmo si chiama "Pollard's Rho". La forma del cammino dell'hacker è letteralmente una coda che finisce in un ciclo. L'hacker sa che la collisione ($x_i = x_j$) avverrà in fretta.

Ma c'è un problema enorme: per accorgersi di aver pestato una vecchia orma, l'esploratore dovrebbe essersi **annotato su un taccuino** ogni singolo passo fatto fin dall'inizio! E questo richiederebbe di nuovo una memoria RAM gigantesca ($\sqrt{n}$). Come si fa a capire di essere in un cerchio senza usare la memoria?

---

### 7. La Lepre e la Tartaruga (Floyd's Cycle-Finding Algorithm)

**Testo Originale:**

> As it is, the methods requires the effort of memorizing $\sim \sqrt{n}$ elements to find a collision. This can be done without relying on a memory storage, using the idea of **Floyd's cycle finding algorithm**.
> 
> _(...Diagramma del loop con indici e matematica modulare...)_
> 
> Notice that two colliding elements $x_i$ and $x_j$ satisfy $x_i = x_j \iff i = j \bmod M$.
> 
> Now, if $i$ is such that $x_i$ is the first element in the loop... Indeed $x_i = x_{2i} \iff i = 2i \bmod M \iff M | i$.
> 
> The problem is now reduced to the one of finding the element $x_i$ (which satisfies $x_i = x_{2i}$).
> 
> We can determine the index $i$ (and so also $x_i$) **without memory**, by computing:
> 
> $f(x_j): \quad x_0 \quad x_1 \quad x_2 \dots \quad \mathbf{x_i}$
> 
> $f(f(x_j)): \quad x_0 \quad x_2 \quad x_4 \dots \quad \mathbf{x_{2i}}$

**Cosa dice:**

La magia finale. Per scovare la collisione senza memoria RAM, si usa l'intuizione di Floyd.

Invece di mandare un solo esploratore col taccuino, l'hacker ne manda **due contemporaneamente** sulla stessa strada, a mani vuote.

- Il primo esploratore calcola la funzione una volta sola: $f(x_j)$. Fa un passo alla volta ($x_1, x_2, x_3\dots$). È la Tartaruga.
    
- Il secondo esploratore calcola la funzione due volte: $f(f(x_j))$. Fa due passi alla volta ($x_2, x_4, x_6\dots$). È la Lepre.
    

Poiché sono bloccati nella forma a $\rho$, prima o poi entreranno nel Loop circolare. Essendo la Lepre più veloce, a un certo punto **doppierà** la Tartaruga da dietro. Nel momento in cui i due esploratori si trovano sull'identica casella (l'equazione magica $x_i = x_{2i}$), la collisione è trovata.

**Spiegazione dettagliata:**

Questo è puro genio algoritmico. L'hacker non ha dovuto salvare un milione di vecchi passi nell'hard disk. Il suo computer tiene in memoria solo **due numeri in croce**: la posizione attuale della Tartaruga e la posizione attuale della Lepre. Ad ogni ciclo di clock, aggiorna queste due posizioni. Li fa correre. Appena i due numeri sono identici, il programma si ferma: ha trovato la collisione.

Li prende, li butta dentro l'equazione del **Lemma 3.18** visto nella prima pagina, calcola l'inverso modulare e si prende la tua password segreta di Diffie-Hellman. Tutto in tempo $\sqrt{n}$ e con consumo di memoria zero. Boom.

Agli ordini! Procediamo con l'analisi implacabile. Il professore in queste pagine fa una deviazione verso la statistica pura e l'analisi matematica per rispondere a una domanda fondamentale dell'hacker: _"Okay, so che la Lepre doppierà la Tartaruga, ma in media, quanti minuti devo aspettare davanti allo schermo del computer prima che succeda?"_

Smontiamo la dimostrazione.

---

### 8. Il Calcolo dell'Attesa (Theorem 3.19)

**Testo Originale:**

> **Theorem 3.19**
> 
> If $f$ is sufficiently random, then the expected value of $T+M$ is $E(T+M) \sim 1.25 \sqrt{n}$, ($n = \#S$)
> 
> **Proof (sketch)**
> 
> What is the probability that the first $k$ values $x_0, x_1, \dots, x_{k-1}$ of $\mathcal{O}_{x_0}$ are all distinct?
> 
> By the collision theorem, it is
> 
> $\prod_{i=1}^{k-1} (1 - \frac{i}{n}) \le \prod_{i=1}^{k-1} e^{-i/n} = e^{-\frac{(k-1)k}{2n}} \sim e^{-\frac{k^2}{2n}}$.
> 
> Now, assuming that they are distinct, what is the probability that the $(k+1)$-th $x_k$ is a match?
> 
> $P(x_k \text{ is a match} \mid x_i \ 0 \le i < k \text{ are distinct}) = k/n$
> 
> and so
> 
> $P(x_k \text{ is the first match}) = P(x_k \text{ is a match} \land x_i \ 0 \le i < k \text{ are distinct})$
> 
> $= P(x_k \text{ is a match} \mid \dots \text{distinct}) \cdot P(\dots \text{distinct}) = \frac{k}{n} e^{-\frac{k^2}{2n}}$.
> 
> Then the expected value
> 
> $E(\text{first match}) = \sum_{k=1}^\infty k \cdot P(x_k \text{ is the first match}) = \sum_{k=1}^\infty \frac{k^2}{n} e^{-\frac{k^2}{2n}}$

**Cosa dice:**

Il Teorema 3.19 stabilisce che la somma della "Coda" ($T$) e del "Loop" ($M$), ovvero il numero di passi totali prima di trovare la collisione, ha un Valore Atteso ($E$) pari a circa $1.25 \sqrt{n}$.

La dimostrazione calcola prima la probabilità di sopravvivere per $k$ passi senza collisioni (usando la formula con l'esponenziale $e$ vista nella lezione precedente). Poi la moltiplica per la probabilità "fatale" di schiantarsi esattamente al passo $k+1$ (che è $k/n$).

Per trovare il Valore Atteso medio, somma tutti i possibili scenari ($k$ moltiplicato per la sua probabilità) da 1 a infinito.

**Spiegazione dettagliata:**

Questo è il calcolo del "tempo di cracking stimato". L'hacker non si accontenta di sapere che il suo programma finirà "prima o poi". Vuole una stima temporale. Il Valore Atteso è la media matematica di infiniti tentativi.

La formula $\frac{k}{n} e^{-\frac{k^2}{2n}}$ è bellissima perché descrive una curva a campana asimmetrica: all'inizio la probabilità di scontrarsi è bassa, poi cresce vertiginosamente attorno a $\sqrt{n}$, e poi crolla di nuovo a zero (perché è quasi impossibile sopravvivere così a lungo senza sbattere). Il picco di questa curva ci dice esattamente quando il computer farà "BIP" e sputerà fuori la collisione.

---

### 9. Il Trucco dell'Integrale (Il "Salto" nell'Analisi Matematica)

**Testo Originale:**

> _(Continuazione della formula)_
> 
> $\sim \sqrt{n} \int_0^\infty t^2 e^{-t^2/2} dt \qquad \text{(see later for details)}$
> 
> $= \sqrt{\frac{\pi}{2}} \cdot \sqrt{n} \sim 1.25 \sqrt{n}$.
> 
> Here are the details of equation $(*)$ in the previous theorem. **Skip them if not interested.**
> 
> _(Segue un grafico a barre di un integrale di Riemann)_
> 
> $\sum_{k=1}^\infty F(k/\sqrt{n}) \simeq n \int_0^\infty F(t) dt \qquad (**)$
> 
> where the last "equality" follows from the definition of Riemann's integral.
> 
> Now, writing the previous formula for $F(t) := t^2 e^{-t^2/2}$ we have that
> 
> $\sum_{k=1}^\infty \frac{k^2}{n} e^{-\frac{k^2}{2n}} = \dots \sim \sqrt{n} \int_0^\infty t^2 e^{-t^2/2} dt \swarrow \text{(is a famous equality)}$
> 
> $= \sqrt{\frac{\pi}{2}} \cdot \sqrt{n} \sim 1.25 \cdot \sqrt{n}$.

**Cosa dice:**

Il prof avverte i non appassionati di matematica che possono saltare questa parte, ma poi svela il trucco. Calcolare una sommatoria infinita ($\sum$) di una formula così brutta è impossibile. Allora usa l'Integrale di Riemann: trasforma i "gradini" discreti della sommatoria nell'area continua sotto una curva ($\int$).

Inserendo i dati nella formula dell'integrale, si ritrova davanti a un integrale celeberrimo nella statistica ($\int_0^\infty t^2 e^{-t^2/2} dt$), il cui risultato esatto è noto da secoli: $\sqrt{\pi/2}$.

Moltiplicando questo numero per $\sqrt{n}$ ottiene il coefficiente magico: **1.25**.

**Spiegazione dettagliata:**

Anche se il prof scrive "saltate se non vi interessa", per un informatico questa è pura eleganza. Abbiamo dimostrato in modo rigoroso, inconfutabile e assoluto che la costante davanti all'algoritmo di Pollard è 1.25.

Se la grandezza della tua cassaforte $n$ è 10.000, sai per certo che, in media, l'algoritmo impiegherà $1.25 \cdot \sqrt{10000} = 1.25 \cdot 100 = 125$ passi per distruggerla. Non è più teoria, è un orologio svizzero.

---

### 10. L'Innesco Finale (Floyd incontra DLOG)

**Testo Originale:**

> Let us now see how to apply this argument to the problem of finding dlogs using Lemma 3.18.
> 
> **Floyd's cycle-finding algorithm to DLOGS**
> 
> The idea is to use Floyd's cycle-finding algorithm to find a collision as in Lemma 3.18 and then to compute dlogs easily.

**Cosa dice:**

Dopo aver chiuso la parentesi statistica, il professore unisce finalmente tutti i puntini. Annuncia il titolo del prossimo scontro: applicheremo l'algoritmo della Tartaruga e della Lepre (Floyd) per trovare i 4 numeri magici ($a,b,c,d$) richiesti dal Lemma 3.18, al fine di calcolare il logaritmo discreto.

**Spiegazione dettagliata:**

Questo è il cliffhanger perfetto. Abbiamo costruito il motore matematico (Lemma 3.18), abbiamo scelto il telaio (la forma a $\rho$), abbiamo installato il software per non consumare memoria (Floyd) e abbiamo appena calcolato esattamente quanti chilometri farà con un pieno (1.25 $\sqrt{n}$).

Nelle prossime pagine, il professore accenderà finalmente la macchina e ci farà vedere il codice esatto per scassinare Diffie-Hellman.

Eccoci al gran finale della trilogia di Pollard! Nelle pagine precedenti abbiamo preparato il telaio (Floyd) e il motore teorico (Lemma 3.18). Ora il professore ci fa vedere il vero e proprio "codice sorgente" della funzione che l'hacker usa per muoversi nel gruppo e sbucciare il logaritmo.

Smontiamo questo capolavoro pezzo per pezzo.

---

### 11. La Mappa Pseudo-Casuale (La funzione $f$)

**Testo Originale:**

> **Prime-order group case**
> 
> For now let us assume we are given a cyclic group of order $\#G = n = p \in \mathbb{P}$. Let $\langle g \rangle = G$, $h \in G$.
> 
> If we want to use Floyd's cycle-finding algorithm we must find a function $f: G \to G$ which behaves as a random function but it is easy enough to track its orbits.
> 
> Pollard's idea is to divide $G$ into three disjoint sets $S_1, S_2, S_3$ such that $1 \notin S_2$ and define $f: G \to G$
> 
> $$f(x) := \begin{cases} h \cdot x & x \in S_1 \\ x^2 & x \in S_2 \\ g \cdot x & x \in S_3 \end{cases}$$

**Cosa dice:**

Il professore imposta le regole del gioco in un gruppo di grandezza prima ($p$). Per far muovere i nostri esploratori (Tartaruga e Lepre) in modo caotico per fargli trovare una collisione, abbiamo bisogno di una mappa (la funzione $f$). Ma non può essere una mappa _totalmente_ casuale, altrimenti ci perderemmo.

L'idea geniale di Pollard è dividere l'intero gruppo in tre fette ($S_1, S_2, S_3$). A seconda della fetta in cui atterra l'esploratore, si applica una regola diversa per fare il passo successivo:

- Se cadi in $S_1$, moltiplichi per il bersaglio $h$.
    
- Se cadi in $S_2$, fai il quadrato del numero che hai.
    
- Se cadi in $S_3$, moltiplichi per il generatore $g$.
    

**Spiegazione dettagliata:**

Questa è l'essenza dell'hackeraggio elegante. Mescolando queste tre operazioni in base a dove "rimbalza" il numero, il percorso diventa talmente imprevedibile da sembrare caotico (creando la famosa forma a $\rho$), ma in realtà è strettamente matematico. Questa funzione è il "motore" che fa muovere la Tartaruga (applica $f$ una volta) e la Lepre (applica $f$ due volte).

---

### 12. Il Taccuino degli Esponenti (Il tracciamento)

**Testo Originale:**

> Now, starting from $1$, at each iteration we will multiply the previous element for $g$ or for a power of $g$ ($h$). Therefore, at the $i$-th iteration, the element $x_i$ will be of the type $g^{a_i} h^{b_i}$.
> 
> The exponents $a_i$ and $b_i$ can be iteratively (and efficiently) computed as follows
> 
> $$a_{i+1} = \begin{cases} a_i & x \in S_1 \\ 2a_i & x \in S_2 \\ a_i + 1 & x \in S_3 \end{cases}$$
> 
> $$b_{i+1} = \begin{cases} b_i + 1 & x \in S_1 \\ 2b_i & x \in S_2 \\ b_i & x \in S_3 \end{cases}$$
> 
> We can use $f$ to perform a pseudo-random walk in $G$ keeping track of the following piece of information $(x_i, a_i, b_i) \in G \times \mathbb{Z}_n \times \mathbb{Z}_n$ starting from $(x_0 = 1, 0, 0)$.

**Cosa dice:**

Mentre l'esploratore viaggia, il computer deve prendere nota di _come_ è arrivato su quella casella. Ogni singolo passo $x_i$ può sempre essere scritto come una combinazione di $g$ e $h$ (ovvero $g^{a_i} h^{b_i}$).

Il prof ci dà le regole per aggiornare gli esponenti $a$ e $b$ ad ogni passo:

- In $S_1$ (moltiplichi per $h$): l'esponente di $g$ resta uguale ($a_i$), l'esponente di $h$ sale di uno ($b_i+1$).
    
- In $S_2$ (fai il quadrato): raddoppiano entrambi gli esponenti ($2a_i$ e $2b_i$).
    
- In $S_3$ (moltiplichi per $g$): l'esponente di $g$ sale di uno ($a_i+1$), l'esponente di $h$ resta uguale ($b_i$).
    

**Spiegazione dettagliata:**

Questo è il vero capolavoro di Pollard. L'hacker non salva la _cronologia_ dei passi (che esaurirebbe la RAM), ma salva solo **la ricetta** del passo attuale. In ogni momento, il computer ha in pancia solo 3 numeri per la Tartaruga e 3 per la Lepre: il valore in cui si trova ($x$), quante $g$ ha usato ($a$) e quante $h$ ha usato ($b$). L'impiego di memoria è praticamente zero.

---

### 13. Lo Scacco Matto (L'estrazione della password)

**Testo Originale:**

> Applying Floyd's cycle-finding algorithm, at some point we obtain $x_{2m} = x_m$, which means
> 
> $$g^{a_{2m}} h^{b_{2m}} = g^{a_m} h^{b_m} \implies \text{(Lemma 3.18)}$$
> 
> $$g^{a_{2m} - a_m} = h^{b_m - b_{2m}}$$
> 
> $$g^{(a_{2m} - a_m)(b_m - b_{2m})^{-1}} = h \pmod p \quad \text{(look at the proof of Lemma 3.18)}$$
> 
> and the discrete logarithm is found.

**Cosa dice:**

La Lepre e la Tartaruga corrono finché non finiscono sulla stessa casella ($x_{2m} = x_m$). A questo punto, il programma si ferma.

Ma siccome noi abbiamo tenuto traccia delle "ricette", non sappiamo solo che i due valori sono uguali, sappiamo che $g^{a_{2m}} h^{b_{2m}}$ (la ricetta della Lepre) è uguale a $g^{a_m} h^{b_m}$ (la ricetta della Tartaruga).

Questa è l'equazione a 4 variabili perfetta che richiedeva il Lemma 3.18!

Il prof fa i passaggi algebrici finali: sposta le $g$ a sinistra, le $h$ a destra, e calcola l'inverso modulare dell'esponente di $h$. Il risultato finale (l'esponente gigante a sinistra) è la nostra agognata password $x$.

**Spiegazione dettagliata:**

Ecco svelato il trucco di magia. Abbiamo iniziato con un logaritmo impossibile da calcolare. L'abbiamo trasformato in una gara di corsa tra una Lepre e una Tartaruga dentro un videogioco a tre zone ($S_1, S_2, S_3$). Non appena i due personaggi si sono scontrati, abbiamo preso i loro contapassi ($a$ e $b$), abbiamo fatto una banale divisione modulare, e la cassaforte crittografica si è aperta.

Tutto in tempo $\sim 1.25 \sqrt{n}$ e con un consumo di RAM pari a una manciata di byte. Questo è il motivo per cui Pollard's Rho è l'algoritmo standard insegnato in crittografia per distruggere i gruppi ciclici.

Come avevamo previsto, ecco la resa dei conti! Il professore prende tutto l'impianto teorico costruito nelle lezioni precedenti e lo cala in un vero e proprio attacco su strada.

Questa è l'esecuzione numerica dell'algoritmo $\rho$ (Rho) di Pollard. Prepariamoci a craccare una password in diretta.

---

### 14. Il Campo di Battaglia (Example 3.20)

**Testo Originale:**

> **Example 3.20 ($n \in \mathbb{P}$)**
> 
> Let $G$ be the subgroup of $(\mathbb{Z}_{607}^*, \cdot)$ generated by $g = 54$. We have $606 = 2 \cdot 3 \cdot 101$ and $ord(g) = 101$, so $\langle g \rangle$ is a (cyclic) subgroup of prime order.
> 
> Let us compute $dlog_g h$, where $h = 122 \in \langle g \rangle$.
> 
> As above
> 
> $f(x) = \begin{cases} hx & x \in S_1 \\ x^2 & x \in S_2 \\ gx & x \in S_3 \end{cases} \quad a_{i+1} = \begin{cases} a_i \\ 2a_i \\ a_i+1 \end{cases} \quad b_{i+1} = \begin{cases} b_i+1 \\ 2b_i \\ b_i \end{cases}$
> 
> $S_1 := \{1 \le i \le 201\}$
> 
> $S_2 := \{202 \le i \le 403\}$
> 
> $S_3 := \{404 \le i \le 606\}$

**Cosa dice:**

Il prof imposta i parametri della cassaforte. Il modulo principale è $p=607$, ma attenzione: stiamo lavorando in un **sottogruppo** generato dal numero $g=54$. L'ordine (la grandezza) di questo sottogruppo non è 606, ma è **101**. Questo numero 101 è fondamentale, sarà il "modulo" che useremo per gli esponenti alla fine!

Il bersaglio pubblico da scassinare è $h=122$.

Poi divide l'intero spazio da 1 a 606 in tre fette uguali ($S_1, S_2, S_3$) e riscrive la "ricetta" di Pollard per muovere gli esploratori e aggiornare i loro contapassi ($a$ e $b$).

**Spiegazione dettagliata:**

Questo è il setup classico di un attacco crittografico. L'hacker analizza il terreno, scopre che la chiave si nasconde in un ciclo lungo 101 passi, e prepara i tre "teletrasporti" ($S_1, S_2, S_3$). Da questo momento in poi, non si deve più pensare, bisogna solo far girare il "ciclo for" del computer e lasciare che la Tartaruga e la Lepre inizino a correre.

---

### 15. La Corsa e la Collisione Invisibile (La Tabella)

**Testo Originale:**

> _(Tabella con colonne per la Tartaruga: $i, x_i, a_i, b_i$ e per la Lepre: $x_{2i}, a_{2i}, b_{2i}$)_
> 
> riga $i=12$: Tartaruga $\to x_{12} = \mathbf{352}$
> 
> riga $i=13$: Lepre $\to x_{26} = \mathbf{352}$
> 
> _(Nota laterale)_: The red pair is also a match but it gets unnoticed
> 
> riga $i=14$: Tartaruga $\to \mathbf{167}, 0, 12$ | Lepre $\to \mathbf{167}, 64, 6$
> 
> $\to$ match
> 
> $x_{14} = x_{28} \implies g^0 h^{12} = g^{64} h^6$

**Cosa dice:**

La tabella mostra i valori calcolati passo dopo passo.

Il prof ci fa notare un dettaglio affascinante (i cerchi rossi): al passo 12, la Tartaruga atterra sul numero 352. Al passo 13, la Lepre (che è al suo 26° salto) atterra anch'essa sul 352. **È una collisione!** Ma il computer _non se ne accorge_. Perché? Perché l'algoritmo controlla solo la stessa riga (confronta $x_{12}$ con $x_{24}$, o $x_{13}$ con $x_{26}$).

Poco male: l'algoritmo continua a girare imperterrito e alla riga 14 trova il match perfetto sulla stessa linea: $x_{14} = x_{28} = \mathbf{167}$. A quel punto, preleva i contapassi ($a$ e $b$) e compone l'equazione.

**Spiegazione dettagliata:**

Questa è la bellezza spietata dell'algoritmo di Floyd. È vero, a volte si perde delle collisioni "di sbieco" (come quella rossa) perché non ha memoria del passato. Ma non gli interessa! Risparmiare memoria è talmente vantaggioso che l'hacker è ben felice di far fare alla Lepre un giro in più nel loop. Tanto, prima o poi, i due esploratori si schianteranno _nello stesso esatto momento_, ed è lì che scatta la trappola. Dalla tabella tiriamo fuori i 4 numeri magici: la Tartaruga ha $a=0, b=12$; la Lepre ha $a=64, b=6$.

---

### 16. L'Estrazione della Password (Il Calcolo Finale)

**Testo Originale:**

> $g^{64} = h^6$ and $\gcd(6, 101) = 1$ and by EEA we find $6 \cdot 17 = 1 \pmod{101} \implies$
> 
> $g^{64 \cdot 17} = h^{6 \cdot 17} = h$
> 
> $g^{1088} = g^{78} = h \implies dlog_g(h) = 78$.
> 
> (remember to work the exponents modulo the order of the generator $g$ of the subgroup!)

**Cosa dice:**

Partiamo dall'equazione del match: $g^0 h^{12} = g^{64} h^6$.

Spostando le $h$ a destra (dividendo), otteniamo: $g^{64} = h^6$.

Ora l'hacker deve liberare la $h$ dal fastidioso esponente 6. Visto che stiamo lavorando negli esponenti, usiamo come modulo l'ordine del gruppo, che è **101**. L'Algoritmo di Euclide Esteso (EEA) ci dice che l'inverso di 6 modulo 101 è **17** (infatti $6 \times 17 = 102$, che diviso 101 dà resto 1).

Quindi, eleviamo entrambe le parti alla 17:

$g^{64 \cdot 17} = h^1 \implies g^{1088} = h$.

Facciamo l'ultima riduzione modulo 101: $1088 \pmod{101}$ fa esattamente **78**.

Il logaritmo discreto è 78. La password è craccata.

**Spiegazione dettagliata:**

Quest'ultima riga verde in fondo agli appunti ("remember to work the exponents modulo the order...") è l'errore in cui cascano tutti all'esame.

I numeri grandi ($x = 167$, $h = 122$) vivono nel modulo della cassaforte grande ($p = 607$).

Ma gli **esponenti** ($a, b$) vivono nel mondo a parte della grandezza del sottogruppo ($n = 101$). Se tu cercassi di fare l'inverso di 6 usando il modulo 607, otterresti un disastro e la password non funzionerebbe. In crittografia, bisogna sempre avere ben chiaro in quale "livello" (base o esponente) ci si sta muovendo!

Abbiamo appena smontato con successo, riga per riga, l'algoritmo più spaventoso di questo capitolo. Se hai altre foto o nuovi argomenti, lanciali pure: il formato è ormai blindato!

### <p align="right"><font color="#2DC26B">Alcuni chiarimenti</font></p>

#### 1. Il Problema: Il Logaritmo Discreto

In crittografia abbiamo un gruppo ciclico di ordine $n$. Conosciamo il generatore $g$ e il risultato $h$, ma non conosciamo l'esponente segreto $x$ tale che:

$$g^x = h \pmod n$$

Trovare $x$ è difficilissimo se procediamo a caso, perché il gruppo è enorme.

---

#### 2. La Soluzione: Trovare una "Collisione"

Il Lemma 3.18 dice che se riusciamo a trovare (in qualsiasi modo) un'uguaglianza tra potenze diverse di $g$ e $h$, il segreto $x$ è praticamente scoperto.

**L'uguaglianza magica:**

$$g^a h^b = g^c h^d$$

Se abbiamo questa relazione, possiamo sostituire $h$ con $g^x$:

$$g^a (g^x)^b = g^c (g^x)^d \implies g^{a+xb} = g^{c+xd}$$

Uguagliando gli esponenti modulo $n$:

$$a + xb \equiv c + xd \pmod n$$

---

#### 3. Perché serve l'Algoritmo di Euclide?

Dall'equazione sopra, vogliamo isolare $x$:

$$x(b - d) \equiv c - a \pmod n$$

Per liberare $x$, dobbiamo "dividere" per $(b - d)$. Ma in aritmetica modulare **la divisione non esiste**.

Usiamo quindi l'**Algoritmo di Euclide Esteso** per trovare l'**inverso modulare** $\alpha$ di $(b - d)$.

- **A cosa serve $\alpha$?** A moltiplicare entrambi i lati per annullare l'esponente di $h$.
    
- **A cosa serve $\beta n$?** Nell'identità di Bézout $(b-d)\alpha + \beta n = 1$, il termine $\beta n$ rappresenta i "giri completi" dell'orologio. Nel gruppo, $h^n = 1$, quindi quel termine svanisce e ci permette di dire che $(b-d)\alpha$ si comporta esattamente come l'esponente $1$.
##### La "Scomparsa" Matematica

L'identità di Bézout è un'uguaglianza tra numeri interi:

$$(b-d)\alpha + \beta n = 1$$

Ora, applichiamo questa uguaglianza come **esponente** alla nostra base $h$:

$$h^{(b-d)\alpha + \beta n} = h^1$$

Usando le proprietà delle potenze, possiamo spezzare il termine a sinistra:

$$h^{(b-d)\alpha} \cdot h^{\beta n} = h^1$$

Sappiamo che nel nostro gruppo l'ordine è $n$, quindi **$h^n = 1$** (l'elemento neutro, che moltiplicato non cambia nulla). Di conseguenza:

$$h^{\beta n} = (h^n)^\beta = (1)^\beta = 1$$

Quindi l'equazione diventa:

$$h^{(b-d)\alpha} \cdot 1 = h^1$$

$$\mathbf{h^{(b-d)\alpha} = h^1}$$
    

---

#### 4. Come trovare la collisione: Pollard's Rho

Visto che non possiamo tirare a indovinare $a, b, c, d$, usiamo una **traiettoria deterministica**.

##### La funzione di cammino $f(x)$

Dividiamo il gruppo in tre zone ($S_1, S_2, S_3$). In base a dove si trova l'elemento corrente $x_i$, decidiamo il passo successivo:

- **Se $x_i \in S_1$:** $x_{i+1} = x_i \cdot h \implies$ (aggiorniamo $b \to b+1$)
    
- **Se $x_i \in S_2$:** $x_{i+1} = x_i^2 \implies$ (raddoppiamo $a \to 2a, b \to 2b$)
    
- **Se $x_i \in S_3$:** $x_{i+1} = x_i \cdot g \implies$ (aggiorniamo $a \to a+1$)
    

**Importante:** Gli esponenti $a$ e $b$ sono strettamente legati a $x$. Se "muovi" la $x$, aggiorni i contatori $a$ e $b$ seguendo la matematica, così l'uguaglianza $x = g^a h^b$ resta sempre vera senza dover fare calcoli pesanti.

##### La Tartaruga e la Lepre (Algoritmo di Floyd)

Per non memorizzare tutto (che occuperebbe troppa memoria), usiamo due esploratori:

1. **Tartaruga ($T$):** fa un passo alla volta ($x_i$).
    
2. **Lepre ($L$):** fa due passi alla volta ($x_{2i}$).
    

Essendo in un gruppo finito, la traiettoria prima o poi entra in un ciclo (forma a $\rho$). La Lepre, essendo più veloce, "doppierà" la Tartaruga nel ciclo.

Quando succede che **$x_i = x_{2i}$**, abbiamo trovato la nostra collisione!

---

#### 5. Esempio di riepilogo (Tabella)

Immagina la riga della collisione nel tuo esercizio:

- **Tartaruga:** si ferma a $167$ con esponenti $(a_T, b_T)$.
    
- **Lepre:** si ferma a $167$ con esponenti $(a_L, b_L)$.
    

Anche se il valore è lo stesso ($167$), la strada fatta è diversa, quindi gli esponenti saranno diversi. Ora hai:

$$g^{a_T} h^{b_T} = g^{a_L} h^{b_L}$$

Applichi Euclide, trovi l'inverso, isoli la $x$ e il logaritmo è risolto.

---
#### 6. Il dubbio sulla Lepre: "Perché $[0]_M$ e cosa è?"

Immagina una pista ciclabile circolare lunga **$M = 5$** metri.

- La **Tartaruga** fa 1 metro al secondo.
    
- La **Lepre** fa 2 metri al secondo.
    

**Cosa succede al secondo 5 ($i=5$)?**

- La Tartaruga ha fatto 5 metri. È tornata al punto di partenza (perché $5 \pmod 5 = 0$).
    
- La Lepre ha fatto 10 metri. Ha fatto due giri ed è tornata anche lei al punto di partenza ($10 \pmod 5 = 0$).
    

In quel momento si toccano! Quel "secondo 5" è il famoso **$[0]_M$**. È l'istante in cui il tempo trascorso è un multiplo esatto della lunghezza del cerchio. In quel momento, anche se hanno fatto distanze diverse, si trovano nello stesso punto $x$.

**Ma attenzione:** hanno accumulato esponenti $a$ e $b$ diversi perché la Lepre ha corso di più e ha attivato più volte le regole delle zone $S_1, S_2, S_3$.


#### 7. Che cos'è $[i]_M$? (La posizione nel Ciclo)

In matematica, questa notazione indica la **classe di resto modulo $M$**.

Immagina che il ciclo dell'algoritmo sia una pista circolare lunga **$M$** metri (dove $M$ è il periodo del ciclo).

- **$M$**: è la lunghezza totale del cerchio (quanti passi servono per tornare allo stesso punto).
    
- **$[i]_M$**: indica la "posizione relativa" di un elemento all'interno di quel cerchio, indipendentemente da quanti giri hai già fatto.
    

##### Esempio pratico:

Se il ciclo è lungo **$M=5$**:

- Il passo 5 è nella posizione $[5]_5 = \mathbf{[0]_5}$ (fine del primo giro).
    
- Il passo 6 è nella posizione $[6]_5 = \mathbf{[1]_5}$ (un passo dopo l'inizio del secondo giro).
    
- Il passo 10 è nella posizione $[10]_5 = \mathbf{[0]_5}$ (fine del secondo giro).
    

---

#####  Perché proprio $[0]_M$? (Il punto dell'incontro)

Nell'algoritmo di Floyd (Lepre e Tartaruga), cerchiamo il momento in cui $x_i = x_{2i}$.

Questo accade solo quando la **differenza tra i passi** è un multiplo esatto della lunghezza del ciclo $M$.

$$2i - i = k \cdot M \implies i = k \cdot M$$

In quel preciso istante, l'indice **$i$** (i passi fatti dalla Tartaruga) è divisibile per **$M$**.

Quindi, la sua posizione nel ciclo è:

$$[i]_M = \mathbf{[0]_M}$$

> **Per i tuoi appunti:** > "Il punto $[0]_M$ è il **punto di aggancio** dove la Tartaruga e la Lepre si sovrappongono. Anche se la Lepre ha corso molto di più, in quel punto entrambi hanno 'completato i loro giri' e si ritrovano nello stesso posto modulo $M$."

##### Perché i valori possono essere Positivi o Negativi?

Questa è una domanda molto acuta. Quando parliamo di esponenti negli appunti, potresti trovare segni meno. Ecco perché:

##### I "Spostamenti" (Somme e Sottrazioni)

Quando cerchi di isolare la $x$ nell'equazione $a + xb \equiv c + xd \pmod n$, devi spostare i termini da una parte all'altra dell'uguale:

$$(b - d)x \equiv c - a \pmod n$$

- **Valori Negativi:** Se $d$ è più grande di $b$, la differenza $(b-d)$ sarà un numero negativo (es. $3 - 8 = -5$).
    
- **In Aritmetica Modulare:** Un numero negativo non è un problema! Si "riporta" nel mondo dei positivi aggiungendo $n$. Se siamo modulo 10, allora $-5$ è la stessa cosa di $+5$.
    

##### Il significato degli esponenti

- **Esponente Positivo ($+1$):** Significa che hai fatto un'operazione "in avanti" (es. hai moltiplicato per $g$ o per $h$).
    
- **Esponente Negativo (L'Inverso):** Quando scrivi $\alpha = (b-d)^{-1}$, quel $-1$ non è un esponente negativo nel senso di "sotto lo zero", ma indica l'**Inverso Modulare**.
    
    - È il valore che "annulla" l'effetto di un altro numero per riportarti a 1.

---

#### Riassunto per lo schema grafico:

Se disegni la **$\rho$ (Rho)**:

1. **La coda:** sono i passi da $x_0$ a $x_{T-1}$. Qui non sei ancora nel ciclo.
    
2. **L'ingresso ($x_T$):** è il primo punto del ciclo. La sua posizione è $[T]_M$.
    
3. **Il punto di match ($[0]_M$):** è un punto qualunque dentro il cerchio dove l'indice del passo è un multiplo di $M$. È lì che i due esploratori "si scontrano" e tu estrai i dati per risolvere il logaritmo.
    

**In breve:** $[0]_M$ non è il "numero zero", è la **linea del traguardo** del ciclo dove i due esploratori si incontrano matematicamente.