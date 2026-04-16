
### 1. Il Grimaldello Incastrato (Composite-order group case)

**Testo Originale:**

> **Composite-order group case**
> 
> Let $G$ be a cyclic group of composite order $n$ and assume that we are executing Pollard's $\rho$ Algorithm for computing $x = dlog_g(h)$, where $\langle g \rangle = G$ and $h \in G$.
> 
> When we get to the collision, we find two elements $a,b \in \mathbb{Z}_n$ such that $g^a = h^b$, where $a$ has been computed from $a_{2m}$ and $a_m$ and $b$ from $b_{2m}$ and $b_m$.
> 
> If $\gcd(b, n) = 1$ we know how to proceed.
> 
> Assume then that $d := \gcd(b, n) > 1$.

**Cosa dice:**

Il professore imposta il nuovo scenario: la grandezza del gruppo $n$ non è più un numero primo intoccabile, ma è un numero "composto" (es. 15, 20, 100...).

L'hacker lancia Pollard's Rho, la Lepre e la Tartaruga si scontrano, e ottiene la famosa equazione $g^a = h^b$.

Fino a ieri, per isolare $h$, calcolavamo l'inverso di $b$ modulo $n$. Questo si può fare solo se il Massimo Comune Divisore (GCD) tra $b$ e $n$ è 1.

Ma oggi il prof dice: e se la sfortuna vuole che il Massimo Comune Divisore sia un numero $d$ più grande di 1? In algebra modulare, questo significa che **$b$ non ha un inverso**. Non puoi dividere. L'algoritmo si ferma.

**Spiegazione dettagliata:**

Questo è il classico imprevisto sul campo. L'hacker ha fatto tutto giusto, ha trovato la collisione, ma quando preme "Invio" per calcolare la password, il computer restituisce "Errore: Inverso Modulare Inesistente". I due numeri ($b$ e $n$) condividono un divisore comune $d$.

Il lucchetto è stato forzato, ma la porta non si apre. Serve un "piede di porco" matematico per scardinare questo intoppo. Ed è qui che entra in gioco l'Identità di Bézout.

---

### 2. Il Piede di Porco Matematico (L'Identità di Bézout)

**Testo Originale:**

> By Bezout's identity $\exists s,t \in \mathbb{Z}$ such that $d = sb + tn$.
> 
> Now
> 
> $g^a = h^b \implies g^{as} = h^{sb} = h^{d-tn} = h^d \qquad (1)$
> 
> and $g^x = h \implies g^{xd} = h^d \qquad (2)$.
> 
> From (1) and (2)
> 
> $g^{as} = g^{xd}$, therefore the value of $x = dlog_g(h)$ is found if we solve the equation (in the unknown $x$)
> 
> $(*) \quad dx = as \pmod n$

**Cosa dice:**

Il prof usa l'Identità di Bézout, una regola aurea dell'algebra: se $d$ è il Massimo Comune Divisore tra $b$ e $n$, allora esisteranno sicuramente due numeri interi ($s$ e $t$) tali che $sb + tn = d$.

Con questa equazione, manipola in modo geniale le potenze:

1. Prende la collisione $g^a = h^b$ e la eleva alla $s$. Tramite Bézout, la parte di $h$ si trasforma miracolosamente in $h^d$.
    
2. Prende l'obiettivo originale dell'hacker ($g^x = h$) e lo eleva alla $d$, ottenendo $g^{xd} = h^d$.
    
    A questo punto ha due equazioni che finiscono entrambe per $h^d$. Le unisce ($g^{as} = g^{xd}$) e passa agli esponenti, ottenendo la nuova equazione da risolvere: $dx \equiv as \pmod n$.
    

**Spiegazione dettagliata:**

Guarda l'eleganza di questo passaggio. L'hacker non poteva isolare la $x$ perché $b$ non era invertibile. Allora ha usato Bézout per "aggirare" il blocco. Invece di cercare direttamente $x$, ha creato una nuova equazione dove la $x$ è moltiplicata proprio per quel fastidioso divisore $d$.

Ora abbiamo l'equazione $(*)$: $dx = as$. Conosciamo $d$ (il GCD), conosciamo $a$ (dalla collisione) e conosciamo $s$ (calcolato da Bézout). L'unica incognita è la nostra password $x$.

Ma attenzione, non abbiamo ancora finito!

---

### 3. La Scelta Multipla (La risoluzione finale)

**Testo Originale:**

> Notice that $d$ is not invertible mod $n$ but $d$ is a divisor of $a \cdot s$. Indeed $d|n \mid as - dx \implies d | as$.
> 
> therefore we have:
> 
> $y_0 := \frac{as}{d}$ is a solution of $(*)$
> 
> $y_1 := \frac{as}{d} + \frac{n}{d}$ is a solution of $(*)$
> 
> $y_2 := \frac{as}{d} + 2 \cdot \frac{n}{d}$ is a solution of $(*)$
> 
> $\vdots$
> 
> $y_{d-1} := \frac{as}{d} + (d-1) \frac{n}{d}$ is a solution of $(*)$
> 
> The solution $x$ we are looking for is the **only** element in $\{y_0, y_1, \dots, y_{d-1}\}$ such that $g^{y_i} = h$.

**Cosa dice:**

L'ultimo foglio 

risolve il problema. Poiché stiamo dividendo l'equazione $dx \equiv as \pmod n$ per un numero $d$ che divide anche il modulo, la matematica modulare ci avverte: non otterrai una sola soluzione, ne otterrai esattamente **$d$ diverse**.

Il prof elenca tutte le possibili soluzioni ($y_0, y_1 \dots y_{d-1}$). Si parte dalla divisione base $\frac{as}{d}$ e poi si aggiungono "pezzi" grandi $\frac{n}{d}$.

L'esponente segreto $x$ è nascosto in mezzo a questa piccola lista. Per capire qual è quello vero, l'hacker prende la lista, fa una rapida prova ($g^{y_i}$) e vede quale di questi produce esattamente il bersaglio $h$.

**Spiegazione dettagliata:**

Ecco il colpo di scena finale. Quando il gruppo non è un numero primo, il grimaldello matematico scardina la porta, ma lascia l'hacker davanti a un bivio con $d$ strade diverse (che di solito è un numero piccolissimo, tipo 2, 3 o 4).

Per un computer, provare 3 o 4 chiavi candidate richiede una frazione di millisecondo. È un attacco "brute-force" microscopico alla fine di un attacco super-ottimizzato.

L'hacker calcola i pochi candidati, li testa sul lucchetto pubblico $h$, scarta quelli sbagliati, e intasca la password corretta.

Con questo stratagemma, Pollard's Rho diventa letale non solo contro i gruppi di ordine primo, ma anche contro le difese peggio configurate che usano ordini composti.

Agli ordini! Il professore qui ci fa sporcare le mani. Dopo aver spiegato la teoria del "grimaldello incastrato" (quando il modulo $n$ è un numero composto e il Massimo Comune Divisore è maggiore di 1), ci fa fare due simulazioni pratiche per vedere come l'hacker risolve il problema dal vivo. E poi chiude il capitolo con un grafico riassuntivo che è la Bibbia della crittografia.

Procediamo con l'analisi implacabile!

---

### 4. Il Primo Inciampo (Example 3.21)

**Testo Originale:**

> **Example 3.21 ($n \notin \mathbb{P}$)**
> 
> Let $p = 47$ and let us consider $(\mathbb{Z}_{47}^*, \cdot)$ of order $46 \notin \mathbb{P}$. Let $g = 5$ be a generator of $\mathbb{Z}_{47}^*$ and $h = 15$. Let us find $dlog_5(15)$, i.e. $x$ such that $5^x = 15 \pmod{47}$.
> 
> _(...Saltiamo le liste $S_1, S_2, S_3$ e passiamo alla tabella...)_
> 
> riga $i=4$: Tartaruga $\to \mathbf{32}, 2, 2$ | Lepre $\to \mathbf{32}, 14, 8$ $\implies$ match
> 
> $x_4 = x_8 \implies g^2 \cdot h^2 = g^{14} \cdot h^8 \implies g^{-12} = h^6 \implies g^{34} = h^6$
> 
> and 6 is not invertible mod $\#\mathbb{Z}_{47}^* = 46$.
> 
> From one side we have $g^a = h^b$ with $a=34$ and $b=6$.
> 
> Then $d = \gcd(6, 46) = 2$ and $2 = 8 \cdot (6) + (-1) \cdot 46 \quad (s=8, t=-1)$.
> 
> _(...)_
> 
> $\implies dx = as \pmod{46}$.
> 
> We solve $2x = 34 \cdot 8 \pmod{46} \implies 2x = 42 \pmod{46}$.
> 
> $y_0 = \frac{42}{2} = 21 \quad (1^{\text{st}} \text{ candidate})$
> 
> $y_1 = \frac{42}{2} + \frac{46}{2} = 21 + 23 = 44 \quad (2^{\text{nd}} \text{ candidate})$
> 
> Filter wrong candidate: $g^{y_0} = h \implies y_0 = 21 = dlog_5(15)$. ($g^{y_1} \neq h$)

**Cosa dice:**

Il professore imposta una cassaforte con modulo primo $p=47$. Questo significa che la grandezza dell'ingranaggio degli esponenti è $n = 46$. Poiché 46 è un numero pari (non primo), siamo a rischio di intoppo.

L'hacker lancia la Lepre e la Tartaruga, che si scontrano al passo 4 sulla casella 32. Compone l'equazione e ottiene $g^{34} = h^6$.

L'esponente di $h$ è $b=6$. Ma 6 e 46 sono entrambi pari, quindi il loro Massimo Comune Divisore ($d$) è 2. Non si può fare la divisione normale!

Il professore applica l'algoritmo di Euclide Esteso per trovare i coefficienti di Bézout ($s=8$) e costruisce l'equazione di bypass: $2x = 42 \pmod{46}$.

Poiché $d=2$, la formula genera esattamente 2 candidati per la password: 21 e 44. Li testa uno alla volta: $5^{21} \pmod{47}$ fa 15 (Bingo!), mentre $5^{44}$ fa un altro numero (Scartato). La password è 21.

**Spiegazione dettagliata:**

Guarda come il computer dell'hacker ha gestito l'imprevisto in frazioni di secondo. Il fatto che l'inverso di 6 non esistesse avrebbe mandato in crash un programma scritto male. Ma con l'aggiunta dell'identità di Bézout, il programma capisce di trovarsi di fronte a un "bivio" a 2 strade. Svolge un mini-attacco di forza bruta sulle uniche due opzioni possibili (21 e 44), testa la serratura, e ti restituisce la chiave corretta senza battere ciglio. Notare il passaggio da $g^{-12}$ a $g^{34}$: l'esponente negativo viene "raddrizzato" aggiungendo il modulo 46 ($-12 + 46 = 34$), perché negli esponenti l'orologio gira a 46 tacche!

---

### 5. Il Bivio a 3 Vie (Esercizio aggiuntivo)

**Testo Originale:**

> Let us make another example: let $p=457 \in \mathbb{P}$, $g=13$ a generator of $\mathbb{Z}_p^*$, $h=196$. Let us find $x = dlog_g(h)$.
> 
> _(...Saltiamo le liste e passiamo alla tabella...)_
> 
> riga $i=24$: Tartaruga $\to \mathbf{84}, 18, 66$ | Lepre $\to \mathbf{84}, 318, 249$
> 
> $g^{18} h^{66} = g^{318} h^{249} \implies g^{-300} = h^{183} \implies g^{156} = h^{183} \implies a=156, b=183$
> 
> and $3 = d = \gcd(183, 456) = 5 \cdot (183) + (-2) \cdot (456) \quad (s=5, t=-2)$
> 
> $dx = as \pmod n \iff 3x = 156 \cdot 5 = 324 \pmod{456}$.
> 
> We solve
> 
> $y_0 = \frac{324}{3} = 108 \implies g^{y_0} \neq h$ (X)
> 
> $y_1 = \frac{324}{3} + \frac{456}{3} = 260 \implies g^{y_1} \neq h$ (X)
> 
> $y_2 = \frac{324}{3} + 2 \cdot \frac{456}{3} = 412 \implies g^{y_2} = h$ (V)
> 
> $\implies y_2 = 412 = dlog_{13}(196)$.

**Cosa dice:**

Un esempio più grande. Cassaforte $p=457$, quindi grandezza ingranaggio $n=456$.

Match trovato al passo 24. L'equazione collassa in $g^{156} = h^{183}$.

Qui l'esponente $b=183$ e il modulo $n=456$ hanno in comune il numero 3. (Infatti $183/3 = 61$ e $456/3 = 152$).

Di nuovo Bézout ci salva, fornendo $s=5$. L'equazione di bypass diventa $3x = 324 \pmod{456}$.

Poiché $d=3$, avremo 3 candidati. Il computer li calcola (108, 260, 412) e li testa sul lucchetto. Il primo fallisce. Il secondo fallisce. Il terzo apre la cassaforte.

**Spiegazione dettagliata:**

Questo dimostra la scalabilità del metodo dell'hacker. Che il GCD sia 2, 3 o 10, il computer genera istantaneamente una piccola lista di sospettati e la filtra alla velocità della luce. Risolvere questo problema a mano avrebbe richiesto giorni; il computer, con Pollard's Rho, ci ha messo solo 24 passi principali e 3 test finali, aggirando con eleganza il problema matematico della non-invertibilità.

---

### 6. La Bibbia della Complessità e il Gancio Finale

**Testo Originale:**

> So far we have studied the following algorithms for computing discrete logarithms in cyclic groups of order $n \sim 2^N$ ($N$ is the size of the input):
> 
> _(Grafico visivo)_
> 
> **B.FORCE:** [TIME: lunghissima (esponenziale)] | [SPACE: cortissima (minima)]
> 
> **BS/GS:** [TIME: media (radice quadrata)] | [SPACE: media (radice quadrata)]
> 
> **$\rho$ (Pollard):** [TIME: media (radice quadrata)] | [SPACE: cortissima (minimal, see proposition 3.3)]
> 
> Let us now introduce the last algorithm of the chapter, which will dramatically change the situation in $(\mathbb{Z}_p^*, \cdot)$.

**Cosa dice:**

Il professore tira le somme su tutto quello che abbiamo imparato finora sui logaritmi discreti, disegnando il grafico definitivo che ogni crittografo ha stampato in testa:

1. **Forza Bruta:** Troppo lenta, ma non consuma memoria.
    
2. **Baby-Step Giant-Step:** Molto più veloce, ma fa esplodere i banchi di memoria RAM.
    
3. **Pollard's Rho ($\rho$):** La perfezione assoluta. Ha la stessa velocità bruciante del BS/GS, ma un consumo di memoria nullo, proprio come la forza bruta.
    

E poi piazza il colpo di scena finale: tutto quello che abbiamo visto finora tratta il gruppo matematico come una "scatola nera" (non ci importa di come sono fatti dentro i numeri). Ma se noi sfruttiamo i punti deboli strutturali del gruppo $(\mathbb{Z}_p^*, \cdot)$, esiste un ultimo algoritmo capace di **cambiare drammaticamente la situazione**, abbassando ulteriormente i tempi di calcolo.

**Spiegazione dettagliata:**

Questo foglio sancisce che Pollard's Rho è il "Re" incontrastato degli algoritmi generici. Se tu crei una crittografia basata su Curve Ellittiche, Pollard's Rho è il limite massimo a cui un hacker può arrivare, e noi dimensioniamo le password (es. 256 bit) proprio per resistere a questo specifico grafico.

Tuttavia, l'ultima frase è un monito: la crittografia classica (come RSA o il Diffie-Hellman standard su numeri primi) non è una scatola nera perfetta. Ha delle fessure matematiche. E l'ultimo algoritmo che sta per introdurre (che quasi certamente sarà l'_Index Calculus_) andrà a infilare un coltello proprio in quelle fessure, obbligandoci a usare chiavi crittografiche gigantesche (es. 2048 o 4096 bit) per mantenere la stessa sicurezza che le Curve Ellittiche garantiscono con soli 256 bit.



![[Pasted image 20260410205428.png|323]]![[Pasted image 20260410205523.png|322]]
Queste foto sono fantastiche perché ci mostrano il "dietro le quinte" dal vivo. Quello che abbiamo appena letto sugli appunti digitali (l'Esempio con $p=457$), il professore lo ha scritto fisicamente sulla lavagna durante la lezione, pezzo per pezzo.

È la prova del nove del nostro attacco. Smontiamo la lavagna!

---

### 5. Il Duello sulla Lavagna (L'Esecuzione dal Vivo di Pollard's Rho)

**Testo Originale (Trascrizione unita delle due foto della lavagna):**

> **[Pannello Sinistro - Il Setup e la Corsa]**
> 
> $\mathbb{Z}_p, \quad p=457 \mid g=\langle 13 \rangle$
> 
> $h=196$
> 
> $13^x = 196 \bmod 457$
> 
> $1 \le S_1 \le 152$
> 
> $153 \le S_2 \le 305$
> 
> $306 \le S_3 \le 456$
> 
> _(Tabella con collisione al passo 24)_
> 
> $i=24 \implies x_i=84, a_i=18, b_i=66 \mid x_{2i}=84, a_{2i}=318, b_{2i}=249$
> 
> $g^{18} h^{66} = g^{318} h^{249}$
> 
> **[Pannello Centrale - L'Algebra e Bézout]**
> 
> $g^{-300} = h^{183} \implies g^{156} = h^{183}$
> 
> $\gcd(b, n) = 3 \quad 5, -2$
> 
> $dx = as \bmod n$
> 
> $S=5; a=156; b=183$
> 
> $3x = 156 \cdot 5 \bmod 456 \implies 3x = 324 \bmod 456$
> 
> $y_0 = \frac{as}{d} = 108$
> 
> $y_1 = \frac{as}{d} + \frac{n}{d} = 108 + 152 = 260$
> 
> $y_2 = \frac{as}{d} + \frac{2n}{d} = 108 + 304 = 412$
> 
> **[Pannello Destro - Il Filtro dei Candidati]**
> 
> $13^{108} \stackrel{?}{=} 196 \bmod 457 \quad F$
> 
> $13^{260} \stackrel{?}{=} 196 \bmod 457 \quad F$
> 
> $13^{412} \stackrel{?}{=} 196 \bmod 457 \quad T$
> 
> $y_2 \text{ is a solution}$
> 
> _(All'estrema destra, la regola di movimento $f(x)$)_

**Cosa dice:**

Il professore ha diviso la lavagna esattamente come un computer divide i processi in memoria.

A sinistra c'è l'input e il "motore di ricerca" (la tabella di Floyd) che gira fino al passo 24, dove trova la collisione sul numero 84.

Al centro c'è il "risolutore di emergenza". Visto che il Massimo Comune Divisore tra 183 e 456 è 3, calcola i coefficienti di Bézout (5 e -2), imposta l'equazione $3x = 324$ e genera la lista dei 3 sospettati: 108, 260 e 412.

A destra (nella foto `image_298214.jpg`) c'è il collaudo finale. Il prof prende i 3 sospettati e fa fare al computer la prova del nove, segnando una **F (Falso)** per i primi due che falliscono l'uguaglianza, e una **T (Vero)** per l'ultimo che apre la serratura.

**Spiegazione dettagliata:**

Vedere questo calcolo scritto col gesso ti fa capire davvero quanto sia meccanico e inesorabile l'algoritmo di Pollard. Non c'è intuizione, non c'è magia: è una catena di montaggio.

1. Corri finché non sbatti contro un muro (pannello sinistro).
    
2. Estrai i 4 numeri dell'urto e passali al tritatutto algebrico (pannello centrale).
    
3. Se l'ingranaggio si inceppa (GCD > 1), usa Bézout per creare delle "chiavi di scorta".
    
4. Prova le chiavi di scorta una alla volta (pannello destro).
    

Quella colonna di **F, F, T** sulla destra è la gratificazione finale dell'hacker. Hai forzato un sistema crittografico che sulla carta aveva 456 combinazioni, eseguendo solo una ventina di calcoli in croce.

![[Pasted image 20260410205635.png|318]]![[Pasted image 20260410205713.png|317]]

Che chiusura di sipario! Il professore ha posato gli appunti, ha preso il gesso e ha fatto il "punto della situazione" riassumendo tutto il corso in due lavagnate. Questa è la mappa mentale definitiva di un crittografo.

Smontiamo la lavagna, traducendo la "lista della spesa" dell'hacker.

---

### 6.1. La Mappa Concettuale e il Muro Invalicabile (Foto 1)

**Cosa c'è scritto sulla lavagna:**

> **[Centro]**
> 
> 1. share secret $\Leftrightarrow$ DH
>     
>     CDHP $\swarrow$
>     
>     DLOG
>     
>     math: $ax=b \bmod n$, $G$, $G \times H$, Bez [Bézout], Birthday [Paradox]
>     
> 
> **[Destra]**
> 
> $2^{128} \max \text{time complexity}$
> 
> - BF [Brute Force]
>     
> - BSGS [Baby-Step Giant-Step]
>     
> - PH 1 [Pohlig-Hellman]
>     
> - PH 2
>     
> - Pollard's
>     
> - PH I+II + P$\rho$
>     
> - Index calculus
>     

**Cosa dice:**

Il professore (indicando in alto) ci ricorda perché siamo qui: vogliamo condividere un segreto in modo sicuro (Diffie-Hellman). Per farlo, ci basiamo sulla difficoltà del problema CDHP, che a sua volta si rompe se sappiamo risolvere il DLOG (Logaritmo Discreto). Sotto, elenca tutti gli "strumenti matematici" che abbiamo imparato a usare: equazioni modulari, teoria dei gruppi, identità di Bézout e il Paradosso del Compleanno.

A destra, traccia il confine del mondo reale: **$2^{128}$ Max Time Complexity (MTC)**. Questo è il limite invalicabile dell'informatica moderna. Se un attacco richiede più di $2^{128}$ operazioni, l'hacker fallisce perché il sole si spegnerà prima che il suo computer abbia finito.

Sotto, fa l'elenco gerarchico di tutte le armi a nostra disposizione, aggiungendo due nomi nuovi: **PH (Pohlig-Hellman)** e **Index calculus**.

---

### 6.2 Il Diagramma di Flusso dell'Hacker (Foto 2)

**Cosa c'è scritto sulla lavagna:**

> $G = \langle g \rangle \qquad n = p_1^{\alpha_1} \cdot p_2^{\alpha_2} \dots p_k^{\alpha_k}$
> 
> $g^x = h$
> 
> if $n < MTC \implies \text{could } BF$
> 
> if $\sqrt{n} < MTC \implies \text{could } P\rho$
> 
> $p_k^{\alpha_k}$
> 
> $\sqrt{p_k}$
> 
> _(A destra, il limite MTC viene abbassato a $2^{50}$ per un esempio didattico)_

**Cosa dice:**

Questa è la logica decisionale cruda e spietata. L'hacker si trova davanti al problema $g^x = h$ in un gruppo di grandezza $n$. Come sceglie l'arma giusta?

1. **Guarda $n$:** Se il numero totale di combinazioni è minore del limite fisico del suo computer ($n < MTC$), non si sbatte nemmeno: lancia la **Forza Bruta (BF)** e aspetta.
    
2. **Guarda $\sqrt{n}$:** Se $n$ è troppo grande, calcola la radice quadrata. Se $\sqrt{n} < MTC$, tira fuori l'arma pesante: **Pollard's Rho ($P\rho$)**. Sa che consumerà zero RAM e ci metterà un tempo accettabile.
    

Ma cosa succede se anche $\sqrt{n}$ è incredibilmente più grande di $2^{128}$? L'hacker si arrende? Assolutamente no. Guarda la primissima riga in alto: **$n = p_1^{\alpha_1} \cdot p_2^{\alpha_2} \dots p_k^{\alpha_k}$**.

**Spiegazione dettagliata (L'anticipazione di Pohlig-Hellman):**

Quella formula è la scomposizione in fattori primi. Il prof ci sta dicendo che l'hacker prende la grandezza della cassaforte ($n$) e la fa a fette.

Se la cassaforte è stata costruita male, e la sua grandezza $n$ è il prodotto di tanti numeri primi piccoli (i famosi $p_k$), l'hacker usa il nuovo algoritmo **Pohlig-Hellman (PH)**.

Questo algoritmo sfrutta il Teorema Cinese del Resto per spezzare il mega-problema del logaritmo discreto in tanti mini-problemi, uno per ogni fattore primo.

E qual è la cosa più terrificante? Che l'hacker applicherà Pollard's Rho non sull'intero $n$, ma solo sul pezzettino più grande! Ecco perché in basso a sinistra c'è scritto **$\sqrt{p_k}$**.

**In sintesi:** Se il fattore primo più grande del tuo sistema ($\sqrt{p_k}$) è minore di $MTC$, sei spacciato, non importa quanto sia grande $n$ in totale. Questo è il motivo per cui, in crittografia moderna, esigiamo che la grandezza del gruppo $n$ sia un numero primo puro, o che abbia almeno un fattore primo gigantesco.