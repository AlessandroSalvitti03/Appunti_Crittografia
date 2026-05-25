Agli ordini! Lezione 21: ci lasciamo alle spalle la Guerra Fredda, le reti sovietiche e i vecchi dinosauri.

Entriamo nell'era moderna. Il professore ci presenta il Re incontrastato della crittografia simmetrica, l'algoritmo che oggi protegge letteralmente tutto il mondo digitale (dal tuo Wi-Fi alle tue transazioni bancarie): l'**AES (Advanced Encryption Standard)**.

Smontiamo le basi di questa fortezza e capiamo che lingua "parla" questo algoritmo!

### 1. La Carta d'Identità di AES

**Testo Originale:**

> **AES (Advanced Encryption Standard) LECTURE #21**
> 
> [...] standardized by the NIST in 2001 and today is a worldwide spread standard for non-military symmetric encryption.
> 
> It operates on $V = \{0,1\}^{128} = \mathbb{F}_2^{128}$ (Magma GF(2))
> 
> and on a key space $K = \mathbb{F}_2^l$, $l \in \{128, 192, 256\}$

**Cosa dice:**

Dopo un concorso pubblico indetto dal governo americano (NIST) durato anni, nel 2001 ha vinto questo algoritmo (il suo vero nome era _Rijndael_, creato da due crittografi belgi).

Le sue specifiche sono uno standard granitico:

- **Il Blocco (Messaggio):** AES lavora _sempre e solo_ su blocchi da **128 bit**. Né più, né meno. Prende l'universo di tutti i possibili messaggi e lo taglia a fette da 128.
    
- **La Chiave:** Puoi scegliere tre "livelli di paranoia" per la chiave master: **128, 192, o 256 bit**.
    

### 2. Il "Campo dei Byte" e l'Esadecimale

**Testo Originale:**

> The arithmetic of AES is built on the **field of bytes** $\mathbb{F}_{256}$.
> 
> A byte is a sequence of 8 bits.
> 
> Notation: we represent each sequence of 4 bits using the classical hexadecimal encoding.
> 
> [...] sometimes we will write $0x73$ to mean the byte $01110011$ in order to distinguish it from the integer $73 = 70+3$.

**Cosa dice e cosa significa:**

Lavorare scrivendo stringhe di 128 zeri e uni farebbe impazzire chiunque. Quindi i crittografi impacchettano i bit in gruppi più comodi: i **Byte** (8 bit). Un blocco AES di 128 bit è composto esattamente da 16 Byte.

Per scrivere un Byte in modo ancora più compatto, si usa l'**Esadecimale** (base 16).

- 4 bit = 1 cifra esadecimale (da 0 a F).
    
- 8 bit (1 Byte) = 2 cifre esadecimali.
    
    Il prof usa il classico prefisso informatico **$0x$** per avvisarci: _"Ehi, questo non è il numero settantatré, è l'esadecimale 73!"_. Che tradotto con la tabellina diventa: $7 \to 0111$, $3 \to 0011 \implies 01110011$.
    

### 3. Sommare i Byte: Il Ritorno dello XOR

**Testo Originale:**

> We already know how to sum two bytes. For example
> 
> $0x73 = 01110011 \oplus 0xFC = 11111100 = 10001111 = 0x8F$
> 
> So $(Bytes, \oplus)$ is a group with neutral element $0x00$ where every element is the inverse of itself.

**Cosa dice:**

In questa speciale "matematica aliena" su cui si basa AES (chiamata Campo di Galois $\mathbb{F}_{256}$), **l'addizione non esiste nel senso tradizionale**. Non si fanno i riporti.

La somma tra due byte è semplicemente l'operazione **XOR ($\oplus$)** bit a bit.

Questa matematica ha delle proprietà bellissime:

1. Lo zero ($0x00$) non fa nulla.
    
2. Ogni numero è l'inverso di se stesso. Se sommi (XOR) un numero per se stesso, si annulla tornando a zero ($A \oplus A = 0$).
    

Per farti prendere confidenza con questa "matematica dei computer", ho preparato un calcolatore AES. Prova a inserire i valori del professore ($73$ e $FC$) o qualsiasi altro esadecimale, e guarda come la somma XOR lavora sotto il cofano!

### 4. Il Problema da Risolvere (Il Cliffhanger)

**Testo Originale:**

> We want to find a way to **multiply bytes**. The reason for this is that we want another operation to use when manipulating messages that introduces **non-linearity**.

L'ultima frase della slide è il gancio per la prossima lezione.

Ricordi l'epitaffio del One-Time Pad? Abbiamo imparato che lo XOR è comodissimo, ma è un'operazione _lineare_. Se AES fosse fatto solo di XOR, un hacker potrebbe distruggerlo usando la semplice algebra.

Il professore ci avvisa: ci serve disperatamente un modo per introdurre il Caos (la non-linearità). Ci serve inventare un modo per **moltiplicare** questi byte tra loro, in modo che l'output sia matematicamente scollegato in modo lineare dagli input.

Scommetto che la prossima lezione sarà un tuffo pazzesco nell'algebra dei polinomi per scoprire come l'AES esegue le moltiplicazioni. Manda pure quando ci sei!

---

Avevamo lasciato in sospeso una questione fondamentale: come facciamo a **moltiplicare** due Byte per creare il caos matematico necessario all'AES?

In queste tre pagine il professore ci svela il trucco. Dimentica i numeri normali. Entriamo nel mondo dell'Algebra Astratta e trasformiamo i bit in **Polinomi**!

Smontiamo questa magia matematica passo dopo passo.

### 5. Il Miracolo di $p^m$ (Perché proprio 256?)

**Testo Originale:**

> **5.3 The field of bytes**
> 
> [...] $(\mathbb{Z}_n, +, \cdot) =: \mathbb{F}_n$ is a field $\iff n$ is prime.
> 
> It is possible to prove that $\forall p \in \mathbb{P} \ \forall m \in \mathbb{N}$ there exists a finite field of size $p^m$.
> 
> From the above mentioned result, since $\#\text{bytes} = 256 = 2^8$, there exists a finite-field structure on the set of bytes.

**Cosa dice:**

Finora sapevamo che per avere un "Campo" perfetto (un posto dove puoi sommare, sottrarre, moltiplicare e **dividere** senza ottenere resti strani), il numero di elementi doveva essere un numero primo (es. $\mathbb{Z}_7$, $\mathbb{Z}_{11}$).

Ma 256 (il numero totale di valori di un Byte) non è un numero primo! È pari. Quindi $\mathbb{Z}_{256}$ fa schifo, non possiamo farci le divisioni.

Qui interviene un teorema miracoloso dell'algebra: i campi perfetti esistono non solo per i numeri primi ($p$), ma anche per le loro potenze (**$p^m$**)!

Siccome $256 = 2^8$ (un numero primo elevato a qualcosa), la matematica ci garantisce che esiste un modo per far funzionare le divisioni. Lo chiamiamo **$\mathbb{F}_{256}$ (Il Campo dei Byte)**.

### 6. I Bit diventano Polinomi (Definition 5.8)

**Testo Originale:**

> In the construction that we will show, **polynomials** will play the same role that integers played in Chapters 1-2.
> 
> A polynomial on $\mathbb{F}_2$ in the variable $x$ is an expression of the type
> 
> $p(x) = a_0 + a_1x + a_2x^2 + \dots + a_nx^n$
> 
> where $a_i \in \mathbb{F}_2$.

**Cosa dice e cosa significa:**

Come costruiamo questo Campo dei Byte? Il trucco è smettere di guardare al Byte come a un numero intero (es. 73) e iniziare a guardarlo come a un **Polinomio**.

In un polinomio su $\mathbb{F}_2$, i coefficienti ($a_i$) possono essere solo **0** oppure **1**.

_Esempio pratico:_ Prendi il byte in binario `1011`.

Leggendolo da destra verso sinistra:

- Il primo bit (1) è il termine noto: $1$
    
- Il secondo bit (1) è la $x$: $1x$
    
- Il terzo bit (0) è la $x^2$: $0x^2$
    
- Il quarto bit (1) è la $x^3$: $1x^3$
    
    Quindi il byte `1011` è in realtà l'equazione: **$x^3 + x + 1$**.
    
    Boom! Abbiamo trasformato l'informatica in algebra.
    

### 7. L'Algebra Aliena (Dove $1+1 = 0$)

**Testo Originale:**

> we can sum and multiply polynomials as usual, **except** that we must remember that coefficients are taken modulo 2. For example:
> 
> $(x^2 + 1) + (x^3 + x + 1) = x^3 + x^2 + x$
> 
> $(x^2 + 1)(x^3 + x) = x^5 + x^3 + x^3 + x = x^5 + x$

**Cosa dice:**

Ora possiamo sommare e moltiplicare questi polinomi usando le regole che abbiamo imparato alle scuole medie, ma con una singola, enorme eccezione (la stessa che abbiamo visto con lo XOR): **i coefficienti si calcolano Modulo 2**.

Questo significa che i numeri negativi non esistono e, soprattutto, **$1+1 = 0$**.

Guarda i calcoli del professore:

- **La Somma:** Se sommi $(x^2 + 1)$ e $(x^3 + x + 1)$, metti insieme i termini simili. Hai un $+1$ dal primo e un $+1$ dal secondo. Fa $2$. Ma Modulo 2, il $2$ diventa $0$! Quindi gli $1$ si annullano e spariscono. Il risultato è puro XOR.
    
- **La Moltiplicazione:** Moltiplichi tutto per tutto. Ottieni due volte il termine $x^3$ ($x^5 + x^3 + x^3 + x$). Di nuovo, hai due $x^3$. Modulo 2, due cose uguali si annullano. Si disintegrano. Rimane solo **$x^5 + x$**.
    

### 8. Il Ritorno al Passato (Divisioni, MCD e Bezout)

**Testo Originale:**

> treating polynomials over $\mathbb{F}_2$ as we treated integers in Chapter 1 we obtain
> 
> 1. [...] $a(x) = b(x) \cdot q(x) + r(x)$ [...]
>     
> 2. [...] there exists a unique greatest common divisor $g(x)$
>     
> 3. (Bezout's identity) [...] $a(x) \cdot s(x) + b(x) \cdot t(x) = g(x)$.
>     

**Cosa dice:**

L'ultima slide è una chiusura del cerchio epica.

Ti ricordi le primissime lezioni del corso? L'algoritmo di Euclide per le divisioni e l'identità di Bezout? Il professore ci sta dicendo che **tutto quello che abbiamo fatto con i numeri interi, ora possiamo farlo identico con i polinomi.**

Possiamo fare la divisione col resto tra polinomi. Possiamo calcolare il Massimo Comune Divisore. E, grazie a Bezout, potremo calcolare **l'inverso moltiplicativo** di un polinomio! Che è esattamente l'operazione che ci serve disperatamente per far funzionare l'AES in modo non-lineare.

Per farti toccare con mano quanto sia bizzarra ma perfetta questa algebra, ti ho preparato un calcolatore che simula esattamente la moltiplicazione dei polinomi di AES. Prova a inserire i polinomi dell'esempio del prof e guarda i termini "gemelli" annullarsi a vicenda!

Scommetto che nella prossima lezione il professore ci mostrerà qual è il "modulo" massimo per questa divisione, perché se moltiplichiamo all'infinito superiamo gli 8 bit del Byte! Manda pure quando sei pronto.

---

Avevamo intuito perfettamente. Per sconfiggere la linearità e creare la famosa scatola magica (S-Box) dell'AES, i crittografi sono dovuti tornare sui banchi di scuola, prendere le divisioni in colonna e l'algoritmo di Euclide, e trasportarli nel bizzarro mondo alieno dei polinomi a coefficienti binari.

Smontiamo queste tre pagine di pura algebra crittografica!

### 9. Le Divisioni in Colonna "Aliene" (Example 5.9, pt. 1 e 2)

**Cosa dicono i calcoli:**

Il professore ci mostra che dividere due polinomi in $\mathbb{F}_2$ funziona esattamente come le divisioni in colonna delle elementari, con una sola regola speciale: **quando devi sottrarre, fai lo XOR!** (E ricordati che i segni negativi non esistono).

Guarda il primo esempio:

Vogliamo dividere $a(x) = x^5 + x^2 + 1$ per $b(x) = x^3 + x^2$.

1. Quante volte il termine più grande del divisore ($x^3$) sta nel termine più grande del dividendo ($x^5$)? Ci sta **$x^2$** volte. Questo è il primo pezzo del Quoziente.
    
2. Moltiplichiamo indietro: $x^2 \cdot (x^3 + x^2) = x^5 + x^4$.
    
3. **La Sottrazione (XOR):** Incolonniamo e sommiamo i termini uguali. Le due $x^5$ si annullano (1+1=0). L'$x^4$ scende giù. Otteniamo $x^4 + x^2 + 1$.
    
4. Ripetiamo finché il grado del resto non è più piccolo del divisore.
    
    Alla fine, il prof ottiene un resto $r(x) = 1$. Visto che c'è un resto, deduce che $b(x)$ non divide perfettamente $a(x)$.
    

**Il "Sogno della Matricola" (pt. 2):**

Nel secondo esempio (che dà resto 0), il prof fa notare una proprietà assurda di questo campo matematico:

$$(x^2 + x + 1)^2 = x^4 + x^2 + 1$$

Dove sono finiti i doppi prodotti ($2x^3$, $2x$) dello sviluppo del quadrato? Semplice: siccome i coefficienti sono modulo 2, tutti i "due" diventano "zero" e si disintegrano! In $\mathbb{F}_2$, il quadrato di una somma è semplicemente la somma dei quadrati.

### 10. Euclide non passa mai di moda (Example 5.9, pt. 3)

**Testo Originale:**

> let us compute by EA the gcd $(a(x), b(x))$.
> 
> $gcd(a, b) = gcd(a \bmod b, b)$

**Cosa dice:**

Ti ricordi il primissimo capitolo del corso? Per trovare il Massimo Comune Divisore (MCD / GCD) usavamo l'Algoritmo di Euclide (EA), facendo le divisioni col resto a catena.

Il professore applica la stessa identica procedura ai polinomi. Prende due polinomi giganti, fa la divisione col resto, prende il resto e lo divide per il divisore precedente, e così via, finché non ottiene un resto in comune. In questo caso, scopre che $g(x) = x + 1$.

Inoltre, menziona l'**Algoritmo di Euclide Esteso (EEA)** per trovare i coefficienti di **Bezout** ($s(x)$ e $t(x)$). Questo è il meccanismo che ci permette di riavvolgere l'algoritmo al contrario.

### 11. L'Arma Segreta di AES: Il Teorema dell'Inverso (Theorem 5.10)

**Testo Originale:**

> **Theorem 5.10**
> 
> Let $a(x), g(x) \in \mathbb{F}_2[x]$. Then there exists $b \in \mathbb{F}_2[x]$ s.t.
> 
> $a(x) \cdot b(x) = 1 \bmod g(x) \iff \gcd(a(x), g(x)) = 1$.
> 
> **Proof:** Identical to that of Proposition 1.28.

**Cosa dice e perché è il Sacro Graal:**

Questo teorema è il motivo per cui abbiamo fatto tutta questa faticaccia con i polinomi.

Ci dice che un polinomio $a(x)$ possiede un **Inverso Moltiplicativo** $b(x)$ (ovvero un numero che moltiplicato per lui dà 1) _se e solo se_ è coprimo con il modulo $g(x)$ (cioè non hanno divisori in comune, $\gcd=1$).

**Perché ci interessa per l'AES?**

Se noi, come progettisti dell'AES, scegliamo come modulo un polinomio $g(x)$ "Irriducibile" (l'equivalente di un numero primo per i polinomi), allora **TUTTI** i polinomi più piccoli di lui avranno un Massimo Comune Divisore pari a 1.

Questo significa che nell'universo a 8 bit dei Byte, **ogni singolo Byte (tranne lo zero) avrà un suo Inverso Moltiplicativo esatto!**

Calcolare l'inverso di un Byte usando l'Algoritmo Esteso di Euclide è un'operazione matematica incredibilmente complessa, frammentata e **brutalmente NON-lineare**.

I creatori di AES hanno preso proprio questa operazione di "Inversione Moltiplicativa dei Polinomi", l'hanno cablata dentro una S-Box, e l'hanno usata per respingere qualsiasi attacco basato sull'algebra lineare!

Visto che fare queste divisioni polinomiali a mano con gli XOR fa venire il mal di testa, ti ho costruito uno strumento per fare le prove in automatico!

---

Hai chiesto un'analisi super dettagliata e completa, e ti porto dritto nel cuore dell'algebra astratta.

Queste tre pagine sono il "momento della verità" per l'AES. Finora abbiamo scherzato con i polinomi, ma qui il professore ci fa costruire l'intero universo matematico in cui l'AES vive e respira: il **Campo di Galois**.

Mettiti comodo, perché stiamo per dimostrare matematicamente come sia possibile dividere e trovare gli inversi in un mondo fatto solo di 0 e 1. Smontiamo questo capolavoro!

### 12. La Prova su Strada (Example 5.11)

**Cosa c'è scritto:**

Il professore apre con un esempio pratico per dimostrarci che l'inverso moltiplicativo esiste davvero.

Prende un polinomio $a(x) = x^2 + x$ e un modulo $g(x) = x^4 + x + 1$.

Usa l'Algoritmo Esteso di Euclide (XGCD) e scopre che i coefficienti di Bezout sono $s(x) = x^2 + x + 1$ e $t(x) = 1$.

**La verifica:** Moltiplica il nostro polinomio $a(x)$ per il suo presunto inverso $s(x)$:

$$(x^2 + x)(x^2 + x + 1) = x^4 + x^3 + x^2 + x^3 + x^2 + x = x^4 + x$$

_(Ricorda: i termini doppi come $x^3$ e $x^2$ si sono annullati perché $1+1=0$!)_

Alla fine calcola $x^4 + x \pmod{g(x)}$. Siccome $g(x) = x^4 + x + 1$, il resto di questa divisione è esattamente **$1$**.

Abbiamo appena dimostrato che $s(x)$ è l'inverso perfetto di $a(x)$!

### 13. La Stele di Rosetta (Il Parallelismo)

**Testo Originale:**

> Let us now define the polynomial equivalent of a prime number. [...]
> 
> Let $g(x) \in \mathbb{F}_2[x]$. We say that $g$ is **irreducible** if $\forall f(x) \in \mathbb{F}_2[x]$ s.t. $0 \le \deg(f) < \deg(g)$ we have $\gcd(f(x), g(x)) = 1$.

**Cosa dice:**

Per far funzionare i campi finiti, avevamo bisogno dei Numeri Primi. Come facciamo con i polinomi?

Inventiamo il concetto di **Polinomio Irriducibile**. È l'esatto equivalente di un numero primo: un polinomio che non può essere diviso (fattorizzato) da nessun polinomio più piccolo di lui, se non da $1$ e da se stesso. Il suo Massimo Comune Divisore ($\gcd$) con qualsiasi polinomio più piccolo è sempre $1$.

Il professore disegna una tabella (La Stele di Rosetta) geniale che mappa il vecchio mondo degli interi (Capitoli 1 e 2) nel nuovo mondo dei polinomi (Capitolo 5):

|**Mondo degli Interi (Cap. 1/2)**|**Mondo dei Polinomi (Cap. 5)**|
|---|---|
|$\mathbb{Z}$ (Tutti i numeri interi)|$\mathbb{F}_2[x]$ (Tutti i polinomi binari)|
|$p$ (Numero Primo)|$g(x)$ (Polinomio Irriducibile)|
|$\forall 1 \le a < p \implies \gcd(a, p) = 1$|$\forall a(x)$ con grado minore di $g \implies \gcd(a(x), g(x)) = 1$|
|Campo $\mathbb{Z}_p$ (di dimensione $p$)|**Il nuovo campo mistico... di dimensione $2^{\deg(g)}$**|

L'ultima riga è la bomba: ci anticipa che se scegliamo un polinomio irriducibile di **grado 8**, otterremo un campo grande esattamente **$2^8 = 256$** elementi. I nostri Byte!

### 14. La Creazione dell'Universo (Theorem 5.13)

**Testo Originale:**

> **Theorem 5.13**
> 
> Let $g(x) \in \mathbb{F}_2[x]$ be an irreducible polynomial of degree $n$.
> 
> Then $\mathbb{F}_2[x]/g := \{ f(x) \bmod g(x) \mid f \in \mathbb{F}_2[x] \}$ [...] is a finite field with $2^n$ elements.

**Cosa dice:**

Questo è il Teorema Fondamentale dell'AES.

Se tu prendi un polinomio irriducibile $g(x)$ di grado $n$, e prendi l'insieme di **tutti i possibili resti** delle divisioni per $g(x)$, ottieni un Campo Finito perfetto.

Lo chiamiamo **$\mathbb{F}_2[x]/g$**.

In questo universo puoi fare somme (XOR) e moltiplicazioni (con modulo $g$), e le regole dell'algebra non si rompono mai.

**Perché ci sono esattamente $2^n$ elementi? (L'inizio della Proof)**

Il prof lo dimostra in modo elegantissimo.

I membri di questo campo sono i _resti_ della divisione per $g(x)$. Se $g(x)$ ha grado $n$, il resto deve per forza avere al massimo **grado $n-1$**.

La forma generale di un elemento è quindi:

$$p(x) = a_0 + a_1x + a_2x^2 + \dots + a_{n-1}x^{n-1}$$

Quanti coefficienti ci sono da $a_0$ ad $a_{n-1}$? Sono esattamente $n$ slot.

Ogni slot può ospitare solo due valori: uno `0` o un `1` (siamo in $\mathbb{F}_2$).

Quindi le combinazioni totali possibili sono $2 \times 2 \times \dots$ per $n$ volte, ovvero **$2^n$**.

_Nota per AES:_ Scegliendo $n=8$, avremo polinomi fino a $x^7$, che corrispondono perfettamente agli 8 bit di un singolo Byte (da $2^0$ a $2^7$). Il numero totale di Byte possibili è $2^8 = 256$. Tutto combacia alla perfezione.

### 15. Il Colpo di Grazia Matematico (L'Esistenza dell'Inverso)

**Cosa dice l'ultima pagina:**

Il prof ha dimostrato che le somme funzionano e che il gruppo ha la dimensione giusta. Ora deve dimostrare la cosa più difficile in assoluto: **la moltiplicazione forma un gruppo chiuso, ovvero ogni elemento ha un Inverso Moltiplicativo.**

Se manca anche solo un inverso, il Campo crolla e l'AES non funziona. Ecco la dimostrazione formale finale:

1. Prendiamo un elemento qualsiasi $a(x) \neq 0$ dentro il nostro campo (il suo grado è minore di $g$).
    
2. Siccome per definizione $g(x)$ è **irriducibile**, non ha divisori in comune con $a(x)$.
    
    $\implies \gcd(a(x), g(x)) = 1$.
    
3. Qui invoca la magia dell'**Identità di Bezout**. Siccome il $\gcd$ è 1, sappiamo per certo che esistono due polinomi magici $s(x)$ e $t(x)$ tali che:
    
    $$1 = a(x) \cdot s(x) + g(x) \cdot t(x)$$
    
4. Ora, calcoliamo tutta l'equazione Modulo $g(x)$ (cioè ci spostiamo dentro il nostro campo finito).
    
    Qualsiasi multiplo di $g(x)$, come $g(x) \cdot t(x)$, diviso per $g(x)$ dà resto zero. Si disintegra.
    
    L'equazione si riduce istantaneamente a:
    
    $$1 \equiv a(x) \cdot s(x) \pmod{g(x)}$$
    
5. **Scacco Matto:** Che cos'è $s(x)$? È quel polinomio che, moltiplicato per $a(x)$, fa tornare $1$. È letteralmente la definizione di **Inverso Moltiplicativo**. E l'abbiamo appena trovato, dimostrando che esiste sempre per ogni $a(x)$!
    

Con un bel quadratino nero $\blacksquare$ alla fine della pagina, il professore ha appena dimostrato che l'ingegneria dietro l'AES è matematicamente inattaccabile.

Tutto quello che l'AES farà d'ora in poi (nella famosa S-Box) sarà prendere un Byte, convertirlo in polinomio, e **sostituirlo con il suo Inverso Moltiplicativo** calcolato in questo modo assurdo.

Un hacker che prova a scrivere un'equazione lineare per craccare AES si schianta contro il muro di queste divisioni polinomiali!

---

Hai chiesto il livello massimo di dettaglio, e questa lezione lo merita tutto.

Il professore fa una mossa didattica geniale. Lavorare direttamente con il campo dell'AES ($\mathbb{F}_{256}$) significherebbe fare calcoli infiniti con polinomi di grado 7.

Quindi, per farci capire esattamente cosa succede sotto il cofano, crea un **"Modello Giocattolo" (Toy Model)**: un campo finito in miniatura con soli 8 elementi (**$\mathbb{F}_8$**), che usa stringhe da **3 bit**.

Le regole che governano questo mini-universo sono _esattamente_ le stesse dell'AES. Smontiamo questo motore di prova pezzo per pezzo!

### 16. La Ricerca del "Numero Primo" (Example 5.14)

**Cosa c'è scritto:**

Per creare un campo perfetto di 8 elementi ($2^3$), ci serve un "Numero Primo" del mondo polinomiale: un polinomio irriducibile $g(x)$ di grado esatto 3.

Il professore sceglie: **$g(x) = x^3 + x + 1$**.

**Perché è irriducibile?**

Il prof fa un ragionamento logico elegante. Un polinomio di grado 3 può essere fattorizzato (spezzato) solo se ha almeno un pezzetto di grado 1 (cioè $x$ oppure $x+1$).

Avere un divisore di grado 1 significa che il polinomio ha delle "radici" (cioè se al posto della $x$ metti `0` o `1`, il risultato fa zero).

Facciamo la prova in $\mathbb{F}_2$:

- Se $x=0 \implies 0^3 + 0 + 1 = 1 \neq 0$
    
- Se $x=1 \implies 1^3 + 1 + 1 = 1 + 1 + 1 = 3 \implies \text{Modulo } 2 = 1 \neq 0$
    

Non fa mai zero! Nessuna radice $\implies$ Nessun divisore $\implies$ **Irriducibile**. Il nostro "modulo" perfetto è validato.

### 17. Il Dizionario: Dai Bit ai Polinomi

Ora che abbiamo il modulo, il professore costruisce la "Stele di Rosetta" di questo campo. Crea una tabella che mappa tutte le $2^3 = 8$ possibili stringhe a 3 bit nei corrispondenti esadecimali e polinomi.

|**Hex**|**Bit**|**Polinomio in F2​[x]/g**|
|---|---|---|
|**0x0**|`000`|$0$|
|**0x1**|`001`|$1$|
|**0x2**|`010`|$x$|
|**0x3**|`011`|$x + 1$|
|**0x4**|`100`|$x^2$|
|**0x5**|`101`|$x^2 + 1$|
|**0x6**|`110`|$x^2 + x$|
|**0x7**|`111`|$x^2 + x + 1$|

Questa è la rubrica che il computer userà per saltare dal mondo informatico (i bit) al mondo dell'algebra (i polinomi).

### 18. La Tavola Pitagorica Aliena (Le Moltiplicazioni)

Nelle slide 2 e 3, il prof ci fa sudare costruendo a mano un pezzo della tavola pitagorica di questo campo, concentrandosi sulla **riga del $0x4$** (ovvero la tabellina del polinomio **$x^2$**).

Per fare questi calcoli a mente, i crittografi usano un **Trucco di Riduzione Magico**.

Sappiamo che tutto è calcolato Modulo $g(x)$. Questo significa che $x^3 + x + 1 \equiv 0$.

Se spostiamo i termini (ricordando che i segni meno non esistono perché $+1 = -1$ in modulo 2), otteniamo:

$$x^3 \equiv x + 1$$

Questa è la regola d'oro! Ogni volta che nei calcoli compare un $x^3$, il computer lo "schiaccia" sostituendolo istantaneamente con $x+1$. (E di conseguenza, $x^4 \equiv x \cdot x^3 \equiv x(x+1) \equiv x^2+x$).

**Smontiamo il calcolo più difficile del prof ($0x4 \;\hat{\cdot}\; 0x7$):**

Vogliamo moltiplicare $x^2$ per $(x^2 + x + 1)$.

1. **Moltiplicazione base:** $x^2(x^2 + x + 1) = x^4 + x^3 + x^2$
    
2. **Riduzione (Applichiamo la regola d'oro):**
    
    Sostituiamo $x^4$ con $(x^2+x)$
    
    Sostituiamo $x^3$ con $(x+1)$
    
3. **L'Equazione diventa:** $(x^2+x) + (x+1) + x^2$
    
4. **XOR finale (1+1=0):** Raggruppiamo i termini simili.
    
    Abbiamo due $x^2 \implies$ si annullano ($0$)
    
    Abbiamo due $x \implies$ si annullano ($0$)
    
    Rimane solo l'**$1$**.
    
5. **Risultato:** $1$, che guardando il dizionario corrisponde all'esadecimale **$0x1$**.
    
    Il prof mostra che il risultato è esattamente $0x1$. La matematica è spietatamente perfetta! E soprattutto, moltiplicare $0x4$ per $0x7$ ha generato $0x1$, un valore del tutto imprevedibile tramite la normale logica binaria (pura non-linearità).
    

### 19. Il Traduttore Universale (L'ultima slide)

**Testo Originale:**

> $t = (a_2, a_1, a_0) \longrightarrow a_2x^2 + a_1x + a_0 = f(x)$
> 
> $y = (b_2, b_1, b_0) \longrightarrow b_2x^2 + b_1x + b_0 = d(x)$
> 
> $\longrightarrow f(x) \cdot d(x) \bmod g \longrightarrow c_2x^2 + c_1x + c_0 \longrightarrow (c_2, c_1, c_0) = t \;\hat{\cdot}\; y$

**Cosa dice:**

Il prof schematizza l'intero motore crittografico. Ecco il ciclo di vita dei dati:

1. Prendi due sequenze di bit in entrata.
    
2. Traducile in polinomi.
    
3. Moltiplicale tra loro.
    
4. Schiaccia il risultato usando la divisione (modulo) col polinomio irriducibile.
    
5. Prendi i coefficienti del resto e ritraducili in bit.
    

**Il Cliffhanger Definitivo:**

> "We are now ready to introduce the field of bytes where the arithmetic of AES is performed. We just need to specify an irreducible polynomial over $\mathbb{F}_2[x]$ of degree 8."

Il modello giocattolo ha funzionato. Ora sappiamo esattamente come si muoverà l'AES. L'unica differenza è che l'AES prenderà le vere stringhe da 8 bit e le "schiaccerà" usando un mostruoso polinomio irriducibile di grado 8!

Per farti dominare questa matematica esoterica e farti toccare con mano l'eleganza del calcolo $0x4 \;\hat{\cdot}\; 0x7$, ti ho costruito il simulatore del nostro Toy Model $\mathbb{F}_8$. Prova a giocare con le moltiplicazioni e guarda la "regola d'oro" ($x^3 \to x+1$) in azione!

---

Preparati, perché qui finiscono le simulazioni e i modelli giocattolo. Quella che hai davanti è la carta d'identità ufficiale del motore dell'AES.

Il professore ci svela finalmente il "Numero Primo" (il polinomio irriducibile) scelto dal governo americano e dai crittografi belgi per creare il Campo di Galois a 256 elementi ($\mathbb{F}_{256}$).

Smontiamo questa pagina e, soprattutto, **calcoliamo a mano tutti i passaggi matematici che il professore ha saltato nell'esempio finale!**

### 20. La Nascita del Campo AES (Definition 5.15)

**Testo Originale:**

> Let $g(x) := x^8 + x^4 + x^3 + x + 1$. Then $g$ is an irreducible polynomial over $\mathbb{F}_2[x]$, so $\mathbb{F}_2[x]/g$ is a finite field with $2^8 = 256$ elements.

**Cosa dice:**

Questo è il Sacro Graal dell'AES. Tra tutti i polinomi irriducibili possibili, lo standard definisce esattamente questo: **$g(x) = x^8 + x^4 + x^3 + x + 1$**.

Siccome è di grado 8 ed è irriducibile, ci garantisce matematicamente che l'universo dei resti generato dalle divisioni per $g(x)$ conterrà esattamente 256 elementi.

Ogni singolo Byte (da `00000000` a `11111111`, ovvero da `0x00` a `0xFF`) viene "canonico identificato" come un elemento di questo campo. Le regole dell'algebra astratta sono state incatenate ai circuiti del computer.

### 20. Il Calcolo Esploso: $0x11 \;\hat{\cdot}\; 0x5F$

Il prof ci lancia un esempio di moltiplicazione tra due Byte. Ma fa un salto enorme tra l'impostazione e il risultato. Facciamo noi il lavoro sporco per vedere come funziona davvero il motore.

#### Fase A: Traduzione da Esadecimale a Polinomio

- **$0x11$:** In binario è `00010001`.
    
    Leggendolo da destra (termine noto) a sinistra: c'è un `1` nella posizione $x^0$ e un `1` nella posizione $x^4$.
    
    $\implies a(x) = \mathbf{x^4 + 1}$
    
- **$0x5F$:** In binario è `01011111`.
    
    Posizioni attive: $x^6, x^4, x^3, x^2, x^1, x^0$.
    
    $\implies b(x) = \mathbf{x^6 + x^4 + x^3 + x^2 + x + 1}$
    

#### Fase B: La Moltiplicazione Estesa

Dobbiamo calcolare $(x^4 + 1) \cdot (x^6 + x^4 + x^3 + x^2 + x + 1)$. Moltiplichiamo tutto:

1. Moltiplichiamo per $x^4$: $x^{10} + x^8 + x^7 + x^6 + x^5 + x^4$
    
2. Moltiplichiamo per $1$: $x^6 + x^4 + x^3 + x^2 + x + 1$
    

Uniamo i risultati e **annulliamo i doppioni (regola XOR $1+1=0$)**:

Abbiamo due $x^6 \implies$ Cancellati!

Abbiamo due $x^4 \implies$ Cancellati!

Il polinomio espanso risultante è:

**$x^{10} + x^8 + x^7 + x^5 + x^3 + x^2 + x + 1$**

#### Fase C: La Riduzione Modulo $g(x)$ (Il Passaggio Saltato)

Questo polinomio enorme non entra in un Byte (ha grado 10, ci servono al massimo polinomi di grado 7). Dobbiamo fare la divisione in colonna per $g(x)$ e prendere il **resto**.

Dividiamo per $x^8 + x^4 + x^3 + x + 1$:

**Step 1:** Eliminiamo il termine più grande ($x^{10}$). Moltiplichiamo il divisore per $x^2$:

$x^2 \cdot (x^8 + x^4 + x^3 + x + 1) = x^{10} + x^6 + x^5 + x^3 + x^2$

Sottraiamo (facciamo lo XOR) questo blocco dal nostro polinomio espanso:

$$(x^{10} + x^8 + x^7 + x^5 + x^3 + x^2 + x + 1) \oplus (x^{10} + x^6 + x^5 + x^3 + x^2)$$

Annulliamo i doppioni ($x^{10}, x^5, x^3, x^2$).

Ci rimane: **$x^8 + x^7 + x^6 + x + 1$**

**Step 2:** Eliminiamo l'attuale termine più grande ($x^8$). Moltiplichiamo il divisore per $1$:

$1 \cdot (x^8 + x^4 + x^3 + x + 1) = x^8 + x^4 + x^3 + x + 1$

Facciamo lo XOR con quello che ci era rimasto prima:

$$(x^8 + x^7 + x^6 + x + 1) \oplus (x^8 + x^4 + x^3 + x + 1)$$

Annulliamo i doppioni ($x^8, x, 1$).

Ci rimane: **$x^7 + x^6 + x^4 + x^3$**

Il grado ora è 7, che è strettamente minore del grado del divisore (8). La divisione è finita. **Questo è il nostro resto!** Ed è esattamente il risultato magico che il professore ha scritto sulla lavagna!

#### Fase D: Ritorno all'Esadecimale

Tradiciamo il resto in binario:

$x^7 + x^6 + x^4 + x^3 \implies$ I bit attivi sono in posizione 7, 6, 4, 3.

In binario: `1101 1000`

Separiamolo a metà per l'esadecimale:

- `1101` in hex è **D** (8+4+1 = 13).
    
- `1000` in hex è **8** (solo l'8 è attivo).
    

Risultato finale: **$0xD8$**.

La matematica è spietata, laboriosa e assolutamente perfetta. Questo singolo calcolo è quello che il tuo telefono fa milioni di volte al secondo quando guardi un video in streaming protetto.

Per evitarti di consumare fogli interi a fare divisioni in colonna con gli XOR, ti ho preparato un calcolatore che esegue la crittografia esatta di questa lezione. Prova a moltiplicare due byte qualsiasi!

---

Finita la teoria, si entra in fabbrica. Queste due diapositive ci mostrano i primissimi ingranaggi del vero **AES-128**.

Il professore ci fa vedere come i dati vengono disposti sul tavolo di lavoro e ci svela finalmente come è costruita la leggendaria **S-Box** dell'AES, unendo l'algebra dei polinomi a una matrice pazzesca.

Smontiamo questa architettura passo dopo passo!

### 21. Il Tavolo di Lavoro: La Matrice di Stato (State Matrix)

**Testo Originale:**

> To begin with, the message $m \in \{0,1\}^{128}$ is arranged in a matrix of bytes:
> 
> $m_1, m_2 \dots m_{16} \dots m_{128}$ (16 bytes)
> 
> $\downarrow$
> 
> $\begin{pmatrix} m_{1,1} & m_{1,2} & m_{1,3} & m_{1,4} \\ m_{2,1} & m_{2,2} & m_{2,3} & m_{2,4} \\ \dots \end{pmatrix} \quad m_{i,j} \in \text{Bytes}$

**Cosa dice e cosa significa:**

L'AES-128 non lavora su una lunga stringa piatta di 128 zeri e uni. Appena il blocco entra nell'algoritmo, viene affettato in 16 Byte.

Questi 16 Byte vengono disposti ordinatamente in una **griglia 4x4**. Questa griglia è il cuore pulsante dell'algoritmo e si chiama **Matrice di Stato (State Matrix)**.

Tutte le operazioni che vedremo d'ora in poi (chiamate _subroutines_) non faranno altro che prendere questa griglia 4x4, mescolarla, sostituirne i pezzi, e passarla al round successivo.

### 22. Il Primo Ingranaggio: SubBytes

**Testo Originale:**

> **SubBytes**
> 
> The SubBytes (or SBox) function operates as a substitution cipher on **each byte** of the state matrix.
> 
> Given a function $S: \text{Bytes} \to \text{Bytes}$ it updates the state matrix like this:
> 
> $\begin{pmatrix} m_{1,1} \dots \\ \dots \end{pmatrix} \to \begin{pmatrix} S(m_{1,1}) \dots \\ \dots \end{pmatrix}$

**Cosa dice:**

La prima operazione dell'AES si chiama `SubBytes`. È facilissima da visualizzare: si prende la nostra funzione scatola-nera ($S$) e la si applica, uno per uno, a tutti i 16 byte della griglia, in modo totalmente indipendente. Il byte in alto a sinistra non sa cosa sta succedendo al byte in basso a destra.

Fin qui sembra il vecchio, debole cifrario a sostituzione. Ma la vera magia è _cosa c'è dentro_ quella funzione $S$.

### 23. L'Anatomia della S-Box: Matematica e Caos (Slide 2)

**Testo Originale:**

> The function S is defined as follows (here we are identifying Bytes = $\mathbb{F}_{256}$ as in Definition 5.15):
> 
> $S(x) := \begin{cases} A x^{-1} + b & x \neq 0 \\ b & x = 0 \end{cases}$

Questa formula è un capolavoro di ingegneria crittografica. L'S-Box dell'AES non è una tabella di numeri buttati a caso. È calcolata matematicamente "al volo" e si divide in due fasi distinte:

#### Fase A: L'Inverso Moltiplicativo ($x^{-1}$)

Prendi il tuo byte $x$. Se è $0x00$, lascialo a zero. Se non lo è, traducilo in polinomio e **calcola il suo Inverso Moltiplicativo nel Campo di Galois $\mathbb{F}_{256}$** (usando esattamente il mostruoso polinomio dell'AES che abbiamo visto nella lezione precedente!).

- **Il Commento Finale del Prof:** Guarda l'ultima frase in basso: _"Notice that $x \mapsto x^{-1}$ will be the only non-linear operation in the cipher"_. Questo passaggio è vitale. È lo scudo termico dell'AES. È l'unica cosa che impedisce agli hacker di risolvere l'AES come se fosse un normale sistema di equazioni lineari.
    

#### Fase B: La Trasformazione Affine ($A \cdot \dots + b$)

Una volta calcolato l'inverso, i crittografi si sono posti un problema: _"L'inverso è non-lineare, ma è un'operazione algebrica molto pulita e famosa. Se un hacker usa l'algebra avanzata, potrebbe trovare delle scorciatoie"_.

Per distruggere questa "pulizia algebrica", l'AES prende i bit dell'inverso appena calcolato e li butta in un frullatore lineare (la trasformazione affine).

Guarda la grossa matrice $A$ (8x8) e il vettore $b$ ($11000110$) scritti dal prof.

Il byte invertito viene messo in colonna e **moltiplicato per la matrice $A$** (usando le regole XOR, $1+1=0$).

Questa operazione sparpaglia i bit in modo pazzesco. Infine, al risultato **si somma (XOR) il vettore $b$**.

**Perché $x=0$ dà $b$?**

Dalla formula: se il byte in ingresso è $0x00$, non ha inverso. L'AES usa lo $0$ di default. Moltiplicare la matrice $A$ per $0$ dà $0$. Resta solo il vettore $+ b$. Quindi lo zero entra nella S-box ed esce come `11000110` (ovvero $0xC6$).

Per farti vedere come l'AES usa questa matrice $A$ per distruggere l'ordine dei bit dopo aver calcolato l'inverso, ti ho preparato un simulatore visivo della **Trasformazione Affine**!

L'AES è partito in quarta! Scommetto che le prossime slide mostreranno le altre tre subroutines che operano sulla Matrice di Stato (come _ShiftRows_ e _MixColumns_). Manda pure quando ci sei!

---

Avevamo indovinato perfettamente alla fine della scorsa lezione. Abbiamo visto la S-Box (`SubBytes`), che crea la non-linearità. Ma un buon cifrario ha bisogno sia di _Confusione_ (S-Box) che di _Diffusione_ (spargere i bit ovunque).

Queste due pagine ci mostrano le ultime tre operazioni che completano un "Round" standard dell'AES. Sono il frullatore perfetto.

Smontiamo queste tre subroutine e chiudiamo il motore dell'AES!

### 24. La Diffusione Orizzontale: `ShiftRows`

**Testo Originale:**

> The ShiftRows operation is a linear transformation that modifies the state matrix by shifting left
> 
> the 1st row of the matrix by 0 positions
> 
> " 2nd " " " " 1 "
> 
> " 3rd " " " " 2 "
> 
> " 4th " " " " 3 "

**Cosa dice e perché lo fa:**

La matrice di stato è una griglia 4x4 (16 byte). L'operazione `ShiftRows` sposta letteralmente i byte verso sinistra, facendoli rientrare da destra (shift circolare), ma in modo asimmetrico per ogni riga:

- **Riga 1:** Sta ferma (0 scatti).
    
- **Riga 2:** Slitta di 1 scatto a sinistra. Il primo byte della riga finisce in fondo.
    
- **Riga 3:** Slitta di 2 scatti a sinistra.
    
- **Riga 4:** Slitta di 3 scatti a sinistra.
    

**Il Genio dietro la mossa:** Se l'AES usasse solo la S-Box e non spostasse mai le colonne, i 4 byte della prima colonna rimarrebbero sempre nella prima colonna fino alla fine dell'algoritmo. Sarebbe come avere 4 piccoli cifrari separati invece di uno grande.

Facendo scivolare le righe in modo asimmetrico, `ShiftRows` distrugge le colonne: garantisce che i 4 byte che prima formavano una singola colonna vengano sparpagliati su **tutte e 4 le colonne diverse**.

### 25. La Diffusione Verticale: `MixColumns`

**Testo Originale:**

> The MixColumns is another linear function which modifies each column of the state matrix by multiplying it with a fixed byte matrix:
> 
> _(Matrice 4x4 moltiplicata per una colonna)_
> 
> $= 0x02 \cdot m_{11} + 0x03 \cdot m_{21} \dots$
> 
> [...] because the arithmetic is performed modulo the AES polynomial.

**Cosa dice:**

Se `ShiftRows` mescola in orizzontale, `MixColumns` è il frullatore verticale. Prende una colonna isolata (4 byte) e fa una moltiplicazione tra matrici usando una tabella di coefficienti fissa (piena di `0x01`, `0x02`, `0x03`).

**Il collegamento con la lezione precedente:**

Guarda la nota a margine del professore sull'aritmetica. Quando l'AES calcola $0x02 \cdot m_{11}$, cosa sta facendo in realtà?

- Sappiamo che $0x02$ corrisponde al polinomio $x$.
    
- Moltiplicare un byte per $0x02$ significa semplicemente prendere il polinomio di quel byte e moltiplicarlo per $x$ (cioè alzare di 1 il grado di tutti i suoi bit).
    
- Se il grado sfora e arriva a $x^8$? Il prof ce lo ricorda: **"modulo the AES polynomial"**. Applichiamo esattamente la divisione col resto per $x^8+x^4+x^3+x+1$ che abbiamo fatto a mano nella lezione scorsa!
    

L'effetto combinato di `ShiftRows` e `MixColumns` garantisce un _Effetto Valanga (Avalanche Effect)_ istantaneo: basta cambiare 1 singolo bit nel messaggio in chiaro, e dopo soli due Round l'intera griglia di 16 byte sarà completamente stravolta.

### 26. Il Lucchetto Finale: `AddRoundKey`

**Testo Originale:**

> The function AddRoundKey takes the state matrix $m$ and the round key $k_i \in \{0,1\}^{128}$ and updates the state matrix by computing $m := m \oplus k_i$
> 
> (notice that AddRoundKey operates as one-time-pad)

**Cosa dice:**

Dopo aver sostituito i byte, averli shiftati e averne frullato le colonne, la matrice è un disastro incomprensibile. Ma finora, tutte queste operazioni sono pubbliche (tutti sanno come funziona `MixColumns`).

La vera sicurezza entra in gioco nell'ultimo passaggio del round: si prende la **sotto-chiave del round ($k_i$)**, che è grande esattamente 128 bit (una matrice 4x4), e si fa un brutale **XOR ($\oplus$)** sovrapponendola alla matrice di stato.

Il prof sottolinea che agisce come un "One-Time Pad" perché maschera perfettamente i dati con del rumore. Senza la chiave, nessuno può riavvolgere la matrice al contrario.

Per farti visualizzare l'eleganza di questo rimescolamento, ti ho costruito un simulatore della matrice di stato che esegue lo **ShiftRows**. Prova ad attivarlo e guarda come una colonna ordinata viene letteralmente distrutta e sparpagliata in diagonale!

A questo punto, manca solo sapere come fa l'AES a generare le varie $k_i$ (il Key-Schedule) e come incolla tutti questi round insieme. Se hai le prossime slide, andiamo a chiudere il cerchio!

---


Qui il professore chiude il cerchio: ci mostra come si generano le chiavi, l'ordine esatto delle operazioni, come si decifra e, soprattutto, ci dimostra visivamente perché l'AES è considerato invincibile (il famoso _Effetto Valanga_).

Procediamo con l'autopsia completa!

### 27. La Fabbrica delle Chiavi: Il Key-Schedule

Finora avevamo assunto di avere magiche "sotto-chiavi" pronte per ogni round. Nelle prime due pagine, il prof ci spiega come vengono forgiate.

**L'Architettura:**

Si parte dalla **Master Key ($k$)** da 128 bit. Viene spaccata in 4 parole (words) da 32 bit ciascuna: $k_a, k_b, k_c, k_d$.

Da questi 4 blocchi, l'algoritmo deve generare **11 Round Keys** ($k_0, \dots, k_{10}$).

**Come si passa dalla chiave attuale alla successiva?**

Guarda il diagramma con i fili incrociati. È una catena di XOR a cascata, ma con una "scatola nera" in mezzo per distruggere la linearità.

L'ultima colonna della chiave attuale ($k_d$) subisce un trattamento d'urto prima di essere usata per generare la nuova colonna $k_{a'}$:

1. **Shift:** I 4 byte vengono ruotati (fili incrociati nel disegno).
    
2. **SubBytes:** Ogni byte passa nella S-Box non-lineare.
    
3. **Il Tocco di Genio (Round Counter $rc_i$):** Viene fatto uno XOR con una costante di round.
    

**Perché il Round Counter è vitale?**

Come per il GOST (ricordi l'attacco di Saarinen?), se non ci fosse un contatore, una chiave fatta tutta di zeri genererebbe sotto-chiavi tutte identiche, aprendo la porta agli _Slide Attacks_.

L'AES distrugge questa vulnerabilità sommando $rc_i = x^{i-1} \pmod{x^8+x^4+x^3+x+1}$.

Guarda la tabella dei contatori nell'ultima pagina:

`01, 02, 04, 08, 10, 20, 40, 80, 1B, 36`

Si moltiplica per $2$ (cioè per $x$) ad ogni round. Quando si arriva a `80` ($x^7$) e si moltiplica per $x$, si sfonda il tetto degli 8 bit ($x^8$)! Scatta la riduzione modulo il polinomio dell'AES, e il valore "droppa" magicamente a `1B`. Un caos deterministico perfetto.

### 28. L'Algoritmo Completo: La Catena di Montaggio

A pagina 3, il prof scrive finalmente lo pseudocodice completo di `AES-128(m, k)`. Notiamo due dettagli fondamentali che differenziano le fasi:

1. **Round 0 (Whitening):** Prima ancora di iniziare a frullare i dati, si fa un banale $m \oplus k_0$. Se non lo facessimo, un hacker potrebbe controllare gli input del primissimo round di S-Box.
    
2. **Round 1-9 (Typical Rounds):** Si applica la ricetta completa: `SubBytes` $\to$ `ShiftRows` $\to$ `MixColumns` $\to$ `AddRoundKey`.
    
3. **Round 10 (L'Eccezione):** L'ultimo round **SALTA** il `MixColumns`!
    
    - _Nota del mestiere (che il prof omette ma è utile sapere):_ Si salta perché aggiungere un `MixColumns` alla fine, subito prima di un `AddRoundKey`, non aggiunge alcuna sicurezza crittografica (si potrebbe fondere matematicamente nella chiave). Toglierlo rende l'hardware per la cifratura e decifratura più simmetrico ed efficiente.
        

### 29. L'Effetto Valanga (Avalanche Effect) - Il Capolavoro

A pagina 4 e 5, il prof disegna quello che è il cuore teorico della sicurezza moderna: il principio di diffusione di Shannon.

Cosa succede se due messaggi in chiaro ($m_1, m_2$) sono **identici in tutto, tranne che per 1 singolo bit**?

Il prof lo disegna con delle macchie verdi ("?" significa byte diverso, macchia unita significa byte uguale). Seguiamo il contagio:

- **Start:** Solo 1 byte su 16 ha un difetto (la X blu).
    
- **Round 0:** `AddRoundKey` è lineare. Il difetto resta in 1 solo byte.
    
- **Round 1:** * `SubBytes` altera il byte difettoso.
    
    - `ShiftRows` lo sposta in un'altra colonna.
        
    - `MixColumns` è il momento critico! Prende quella colonna e la mescola matematicamente. L'errore di 1 singolo byte **infetta l'intera colonna (4 byte)**.
        
- **Round 2:**
    
    - `ShiftRows` prende questi 4 byte infetti e li fa slittare in modo asimmetrico. Ora c'è **1 byte infetto in ognuna delle 4 colonne** della matrice!
        
    - `MixColumns` si attiva di nuovo. Trova 1 byte infetto in colonna 1, e infetta tutta la colonna 1 (4 byte). Lo fa per la colonna 2, 3 e 4.
        
    - **Risultato:** $4 \times 4 = 16$. **TUTTA LA MATRICE È INFETTA.**
        

In soli 2 round, l'AES raggiunge la _Full Diffusion_. Un singolo bit cambiato all'inizio corrompe irrimediabilmente l'intero crittogramma finale. L'hacker non ha più alcun punto di riferimento.

### 30. Decifratura, S-Box e Costi Nascosti

Nelle ultime due pagine, il prof ci porta nel back-office dell'algoritmo.

**Le Tabelle di Lookup (S-Box):**

Invece di fare calcoli polinomiali folli a ogni ciclo, i software reali usano delle tabelle precalcolate (le griglie numeriche che vedi).

- Esempio: Per fare il `SubBytes` di `00`, guardi la tabella e vedi che diventa `63`.
    
- Per decifrare si usa la tabella inversa (`InvSubBytes`).
    

**L'Asimmetria della Decifratura (InvMixColumns):**

Per decifrare, si eseguono le operazioni al contrario: `InvShiftRows`, `InvSubBytes`, `InvMixColumns`, `AddRoundKey`.

Ma guarda la matrice dell'`InvMixColumns`! Invece di usare numeri piccoli e agili come `01, 02, 03`, è costretta a usare mostruosità polinomiali come **`14, 11, 13, 9`** (che in hex sono `0E, 0B, 0D, 09`).

- _Dettaglio tecnico:_ Fare moltiplicazioni polinomiali per 14 è molto più lento che farle per 2 o 3. Questo significa che **in hardware, decifrare l'AES costa molta più fatica ed energia che cifrarlo!**
    

**Il Miracolo dell'Efficienza (L'ultima nota):**

Il prof chiude con un calcolo sbalorditivo.

Se volessimo mappare un cifrario casuale a 128 bit, dovremmo memorizzare una tabella gigantesca da $2^{128} \times 2^{128} = 2^{256}$ bit di roba (una roba che non starebbe negli hard disk di tutta la Terra messa insieme).

Invece, l'AES genera lo stesso livello pratico di caos impenetrabile chiedendo al computer di memorizzare **meno di $2^{10}$ bit** (solo il codice per generare le chiavi, la S-Box da 256 byte e le micro-matrici di MixColumns).

È letteralmente la vittoria suprema dell'algebra astratta sull'ingegneria del software.

Per farti dominare definitivamente il concetto più importante di queste 6 pagine, ti ho costruito il simulatore dell'**Effetto Valanga (Avalanche Effect)**. Prova a infettare un singolo byte e guarda come in 2 round l'AES distrugge l'intero blocco!