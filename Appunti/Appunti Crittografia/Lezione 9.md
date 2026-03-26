Nelle scorse lezioni abbiamo visto il **Pohlig-Hellman I (Outer step)**, che frantumava un gruppo gigante dividendolo nei suoi fattori primi (es. $18$ diventava $2 \times 9$).

Ma cosa succede se il numero totale degli elementi del gruppo è la **potenza di un singolo numero primo**, e quindi non puoi dividerlo con il Teorema Cinese del Resto?

È qui che entra in gioco questo **Example 3.28 (Pohlig-Hellman II - Inner step)**. In queste tre pagine il professore ci svela il "trucco dell'esponente binario".

Leggiamo gli appunti e smontiamo questa magia matematica passo dopo passo!

---

## Il Campo di Battaglia

- **Il Gruppo:** $G = (\mathbb{Z}_{257}^*, \cdot)$. Essendo 257 un numero primo, la grandezza del gruppo è $256$ (cioè $p-1$).
    
- **Il Problema:** Il numero $256$ è esattamente $2^8$. Non ci sono altri fattori primi se non il 2.
    
- **La Sfida:** Il generatore è $g=3$. Abbiamo intercettato il messaggio $h=219$. Vogliamo calcolare il logaritmo discreto per trovare l'esponente segreto $x$ tale che **$3^x = 219 \pmod{257}$**.
    

## L'Idea Geniale: Il Codice Binario

Il professore scrive: _the idea is that if $0 \le x \le 255$ [...] we can write $x$ in binary._

Invece di provare tutti i 256 numeri a caso, l'hacker sa che qualsiasi numero $x$ può essere scritto come una sequenza di 8 bit (fatta di $0$ e $1$):

$$x = x_0 + 2x_1 + 4x_2 + 8x_3 + \dots + 128x_7$$

Dove i vari $x_0, x_1, \dots$ possono valere solo **0** oppure **1**.

L'obiettivo dell'attacco ora non è più trovare l'intero $x$ in un colpo solo, ma **scoprire un singolo bit alla volta**, partendo da $x_0$ per arrivare a $x_7$.

---

## Lo Step 1: Trovare il primo bit ($x_0$)

L'equazione di partenza è $g^x = h$. Se sostituiamo la $x$ con la sua versione binaria, otteniamo:

$$g^{x_0 + 2x_1 + \dots + 128x_7} = h$$

**Il trucco del "Vaporizzatore":**

L'hacker vuole isolare $x_0$ e far sparire tutti gli altri bit. Come fa? Sappiamo che la grandezza del gruppo è 256, quindi **$g^{256} = 1$** (tutto ciò che è moltiplicato per 256 si vaporizza).

Il prof prende l'equazione e **eleva entrambi i lati alla potenza 128**:

$$(g^{x_0 + 2x_1 + \dots})^{128} = h^{128}$$

Moltiplicando gli esponenti per 128, guarda cosa succede:

- $128 \cdot x_0$ rimane $128x_0$.
    
- $128 \cdot 2x_1$ diventa **$256x_1$**.
    
- Tutti i termini successivi diventano multipli di 256.
    

Poiché $g^{256} = 1$, tutta la coda dell'equazione evapora magicamente! Rimane solo il primissimo bit:

$$(g^{128})^{x_0} = h^{128}$$

**Il Calcolo (Inizio Pagina 2):**

Il prof calcola i due pezzi (usa un software matematico chiamato _Magma_ per fare i conti grossi, la "M" verde che vedi scritta in piccolo):

- $g^{128} \pmod{257}$ fa sempre $-1$, che in questo orologio corrisponde a **$256$**.
    
- $h^{128} \pmod{257}$ (calcolato da Magma) fa **$256$**.
    

Quindi l'equazione è: $256^{x_0} = 256$.

Qual è quell'esponente (che può essere solo 0 o 1) che lascia il 256 uguale a se stesso? Ovviamente è 1.

**Abbiamo trovato il primo bit: $x_0 = 1$.**

---

## Lo Step 2: Trovare il secondo bit ($x_1$)

Ora che sappiamo che $x_0 = 1$, possiamo usarlo per sbloccare il livello successivo.

Ripartiamo dall'equazione originale, ma stavolta **eleviamo tutto alla potenza 64** (così il termine con $x_1$, che era moltiplicato per 2, diventerà 128, e tutti i termini successivi diventeranno 256 e si vaporizzeranno).

$$(g^{x_0 + 2x_1 + 4x_2 \dots})^{64} = h^{64}$$

$$g^{64x_0 + 128x_1 + 256x_2 \dots} = h^{64}$$

Il $256x_2$ evapora. Isoliamo il pezzo con $x_1$ portando l'altro dall'altra parte dell'uguale (cambiando di segno l'esponente):

$$(g^{128})^{x_1} = h^{64} \cdot g^{-64x_0}$$

Siccome sappiamo già che $x_0 = 1$, la formula a destra è tutta formata da numeri noti!

- Magma calcola $h^{64} \cdot g^{-64}$ e scopre che fa **$1$**.
    
- L'equazione diventa: $256^{x_1} = 1$.
    

Quale bit fa diventare un numero uguale a 1? Lo zero!

**Abbiamo trovato il secondo bit: $x_1 = 0$.**

---

## La Reazione a Catena 

Il professore capisce che il meccanismo è sempre lo stesso e applica la formula in modo iterativo per tutti gli altri bit. Ogni volta dimezza l'esponente per cui elevare (32, 16, 8, 4, 2, 1) e sottrae tutto ciò che ha già scoperto nei passaggi precedenti.

I calcoli di _Magma_ gli restituiscono questi risultati:

- Per $x_2$ (elevando a 32): il risultato a destra è $256 \implies \textbf{x}_2 \textbf{= 1}$
    
- Per $x_3$ (elevando a 16): il risultato a destra è $256 \implies \textbf{x}_3 \textbf{= 1}$
    
- Per $x_4$ (elevando a 8): il risultato a destra è $1 \implies \textbf{x}_4 \textbf{= 0}$
    
- Per $x_5$ (elevando a 4): il risultato a destra è $256 \implies \textbf{x}_5 \textbf{= 1}$
    
- Per $x_6$ (elevando a 2): il risultato a destra è $1 \implies \textbf{x}_6 \textbf{= 0}$
    
- Per $x_7$ (elevando a 1): il risultato a destra è $1 \implies \textbf{x}_7 \textbf{= 0}$
    

## La Ricostruzione Finale 

L'hacker ha collezionato tutti i bit! Ora li mette in fila dal più grande al più piccolo (da $x_7$ a $x_0$) per ricostruire il numero binario finale:

$$00101101_2$$

Per scoprire qual è la password $x$ nel nostro normale sistema decimale, basta accendere i valori corrispondenti alle potenze di due:

- $x_0$ vale 1
    
- $x_2$ vale 4
    
- $x_3$ vale 8
    
- $x_5$ vale 32
    

Facciamo la somma: $32 + 8 + 4 + 1 = \textbf{45}$.

Il logaritmo discreto è risolto: l'esponente segreto è **$45$**! E se provi a calcolare $3^{45} \pmod{257}$, otterrai esattamente $219$.

Questo metodo è devastante perché invece di provare $256$ moltiplicazioni a forza bruta, ha trasformato il problema in $8$ equazioni banalissime.

Il professore ha fatto una cosa utilissima: ha preso l'incubo teorico di prima (il Pohlig-Hellman II con il trucco binario) e lo ha applicato a un gruppo molto più piccolo. In questo modo **puoi fare tutti i calcoli a mano** senza dipendere dal software _Magma_ e vedere ogni singolo ingranaggio che gira!

Smontiamo questa lavagna passo dopo passo, perché è un capolavoro di aritmetica modulare.

---

## Il Setup: La Sfida

- **Il Gruppo:** Lavoriamo in $\mathbb{Z}_{17}^*$ (le moltiplicazioni modulo 17).
    
- **La Grandezza:** Essendo 17 un numero primo, la grandezza del gruppo è $17 - 1 = \textbf{16}$.
    
- **Il vantaggio hacker:** Il numero 16 è un'esatta potenza di due ($2^4$). Questo significa che possiamo usare il trucco binario! Avendo esponente massimo 4, ci serviranno solo **4 bit** per trovare la soluzione ($x_0, x_1, x_2, x_3$).
    
- **L'Equazione:** Il generatore è 3. Il messaggio intercettato è 11. Dobbiamo risolvere:
    
    $$3^x = 11 \pmod{17}$$
    

L'hacker scompone subito la $x$ in codice binario:

$x = x_0 + 2x_1 + 4x_2 + 8x_3$ (dove ogni bit vale o 0 o 1).

---

## Step 1: Trovare il primo bit ($x_0$)

Sostituisce la $x$ nell'equazione:

$$3^{x_0 + 2x_1 + 4x_2 + 8x_3} = 11$$

**Il trucco del vaporizzatore:** Per isolare $x_0$, il prof eleva tutto alla potenza **8**. Perché proprio 8? Perché moltiplicando l'esponente per 8, il secondo termine ($2x_1$) diventerà $16x_1$. E noi sappiamo (per il Piccolo Teorema di Fermat) che in questo gruppo **$3^{16} = 1$**. Tutto ciò che ha il 16 svanisce!

Facendo i calcoli:

- A sinistra: $(3^8)^{x_0} \cdot 3^{16(\text{roba})} \implies$ la coda sparisce, resta $(3^8)^{x_0}$.
    
- A destra: $11^8 \pmod{17} = \textbf{16}$. _(Nota: 16 equivale a dire -1)_.
    

Sappiamo anche che $3^8 \pmod{17} = 16$. Quindi l'equazione diventa:

$$16^{x_0} = 16$$

Quale esponente lascia il 16 identico? L'1.

**Risultato: $x_0 = 1$.**

---

## Step 2: "Sbucciare" l'equazione per trovare $x_1$

Questa è la parte che prima faceva il computer e che qui vedi calcolata a mano. Ora che sappiamo che $x_0 = 1$, lo sostituiamo nell'equazione e **lo portiamo dall'altra parte dell'uguale** per togliercelo di torno.

- L'equazione era $3^{1 + 2x_1 + 4x_2 + 8x_3} = 11$.
    
- Separa il $3^1$: $3 \cdot 3^{2x_1 + 4x_2 + 8x_3} = 11$.
    
- Sposta il 3 a destra moltiplicando per il suo inverso modulare ($3^{-1}$).
    
- _Calcolo a mente dell'inverso:_ Quale numero moltiplicato per 3 fa 1 (modulo 17)? È il **6** (perché $3 \cdot 6 = 18$, che in modulo 17 fa proprio 1).
    
- Quindi a destra ottiene: $11 \cdot 6 = 66$, che modulo 17 fa **15**.
    

Nuova equazione pulita:

$$3^{2x_1 + 4x_2 + 8x_3} = 15$$

Ora usa di nuovo il vaporizzatore, ma stavolta **eleva tutto alla potenza 4** (così il termine $4x_2$ diventerà 16 e sparirà).

- A sinistra resta: $(3^8)^{x_1}$, che sappiamo essere $16^{x_1}$.
    
- A destra: $15^4 \pmod{17} = \textbf{16}$.
    
    L'equazione è $16^{x_1} = 16$.
    
    **Risultato: $x_1 = 1$.**
    

---

## Step 3 e 4: Ripetere fino alla fine

Ormai il meccanismo è un loop perfetto. Sostituisci il bit trovato, sposta il pezzo noto a destra moltiplicando per l'inverso, e poi eleva al quadrato o alla potenza necessaria per far evaporare il resto.

**Trovare $x_2$:**

- Equazione base pulita: $3^{4x_2 + 8x_3} = 11 \cdot 6^3 \pmod{17} = \textbf{13}$.
    
- Eleva tutto alla potenza **2**.
    
- $(3^8)^{x_2} = 13^2 \pmod{17} = \textbf{16}$.
    
- Equazione: $16^{x_2} = 16$.
    
    **Risultato: $x_2 = 1$.**
    

**Trovare $x_3$:**

- Equazione base pulita: $3^{8x_3} = 11 \cdot 6^7 \pmod{17} = \textbf{1}$.
    
- Non serve nemmeno elevare! $(3^8)^{x_3} = 1 \implies 16^{x_3} = 1$.
    
- L'unico modo per far diventare un numero 1 è elevarlo alla zero.
    
    **Risultato: $x_3 = 0$.**
    

---

## Il Gran Finale: La Ricostruzione

L'hacker ha raccolto tutti i 4 bit del nostro codice segreto. Li rimette nella formula iniziale:

$$x = x_0 + 2x_1 + 4x_2 + 8x_3$$

$$x = 1 + 2(1) + 4(1) + 8(0)$$

$$x = 1 + 2 + 4 + 0 = \textbf{7}$$

**La Password craccata è 7!**

Il professore scrive $x=7$ in fondo e chiude la lezione. Per curiosità, se calcoli $3^7 \pmod{17}$ ottieni esattamente **11**. Il lucchetto è stato aperto chirurgicamente a mano.
__________________________________________________________________________


![[Pasted image 20260326100441.png|388]]
![[Pasted image 20260326100424.png|390]]

Il professore sta usando esattamente il "trucco binario" di prima, ma applicato a una nuova equazione. E la cosa più bella è che ci svela **un trucchetto pratico** (il numero 86) per velocizzare i calcoli quando si "sbuccia" l'equazione!

Smontiamo la lavagna pannello per pannello.

---

## Il Setup (Lavagna a sinistra)

- **Il Gruppo:** Lavoriamo sempre in $\mathbb{Z}_{257}^*$.
    
- **La Grandezza:** Essendo 257 primo, gli elementi sono $256$ (ovvero $2^8$).
    
- **Il Generatore:** $g = 3$.
    
- **L'Obiettivo:** Risolvere il logaritmo discreto **$3^x = 22 \pmod{257}$**.
    
- **Il trucco binario:** Il prof scrive la $x$ come somma di 8 bit:
    
    $x = x_0 + 2x_1 + 4x_2 + 8x_3 + 16x_4 + 32x_5 + 64x_6 + 128x_7$.
    

## Step 1: Le prime "Vaporizzazioni" (Lavagna sinistra e centro)

Il professore inizia a scovare i bit uno alla volta, partendo dal più piccolo ($x_0$).

Come avevamo visto, eleva entrambi i lati dell'equazione a potenze sempre più piccole (128, 64, 32...) in modo da far diventare l'esponente totale un multiplo di 256, che fa "evaporare" la coda dell'equazione lasciando come risultato 1.

- **Trova $x_0$:** Eleva a 128. Ottiene $3^{128x_0} = 22^{128} \pmod{257}$. Il risultato è 1.
    
    Quale bit dà come risultato 1? Lo zero! $\implies \mathbf{x_0 = 0}$.
    
- **Trova $x_1$:** Eleva a 64. Ottiene $3^{128x_1} = 22^{64} = 1$.
    
    Anche qui, risultato 1. $\implies \mathbf{x_1 = 0}$.
    
- **Trova $x_2$:** Eleva a 32. Ottiene $3^{128x_2} = 22^{32} = 256$. (Ricorda che 256 equivale a -1).
    
    Risultato 256 $\implies \mathbf{x_2 = 1}$.
    

## Step 2: Il trucco del "Numero Magico 86" (Lavagna centrale)

Arrivato a $x_3$, il prof deve "sbucciare" via il pezzo che ha appena scoperto ($x_2 = 1$, che nella formula vale $4 \cdot 1 = 4$).

Deve spostare il $3^4$ dall'altra parte dell'uguale. Come si sposta una moltiplicazione in aritmetica modulare? **Moltiplicando per l'inverso!**

Qual è l'inverso moltiplicativo di 3 in modulo 257? È **86**.

Infatti, se fai $3 \cdot 86$ ottieni 258, che diviso per 257 dà resto 1.

Ecco svelato il mistero di quel numero! Invece di scrivere diviso 3, il prof moltiplica tutto per 86.

- Quindi sposta il $3^4$ a destra scrivendolo come $\mathbf{86^4}$.
    
- L'equazione pulita diventa: $3^{\text{resto}} = 22 \cdot 86^4 = 32$.
    
- Eleva a 16 e trova $x_3 = 0$.
    

## Step 3: La tecnica dell'Accumulatore (Lavagna a destra)

Nel pannello di destra, il prof cambia leggermente il modo di scrivere per non impazzire con i calcoletti intermedi. Invece di spostare un pezzettino alla volta, **sostituisce nella formula base il totale della $x$ che ha scoperto fino a quel momento!**

Guarda la prima riga in alto a destra:

- **Perché scrive $3^{20}$?**
    
    Perché finora ha scoperto che $x_2 = 1$ (che vale 4) e $x_4 = 1$ (che vale 16). La somma fa **20**.
    
    Quindi l'equazione originale $3^x = 22$ diventa $3^{20 + \text{resto}} = 22$.
    
    Sposta il $3^{20}$ a destra usando sempre il numero magico: $22 \cdot 86^{20} = \mathbf{253}$.
    
    Eleva a 4 e trova $\mathbf{x_5 = 1}$.
    
- **Perché poi scrive $3^{52}$?**
    
    Perché ha appena scoperto che $x_5 = 1$ (che vale 32). Lo somma al 20 di prima e ottiene **52**.
    
    Nuova equazione: $3^{\text{resto}} = 22 \cdot 86^{52} = \mathbf{16}$.
    
    Eleva al quadrato e trova $\mathbf{x_6 = 1}$.
    
- **Perché infine scrive $3^{116}$?**
    
    Perché ha scoperto che $x_6 = 1$ (che vale 64). Lo somma al 52 e ottiene **116**.
    
    Nuova equazione: $3^{\text{resto}} = 22 \cdot 86^{116} = \mathbf{256}$.
    
    L'ultimo bit è $\mathbf{x_7 = 1}$.
    

## Il Gran Finale

Adesso ha tutti gli 8 bit! Li somma insieme in base ai loro "pesi" (le potenze di 2):

- $x_2 \implies 4$
    
- $x_4 \implies 16$
    
- $x_5 \implies 32$
    
- $x_6 \implies 64$
    
- $x_7 \implies 128$
    

Fa la somma totale: $4 + 16 + 32 + 64 + 128 = \mathbf{244}$.

Il professore scrive vittorioso in fondo: $\implies x = 244$.

**Il lucchetto è stato scassinato!**

Hai visto come l'inverso modulare (l'86) sia stato il vero passepartout per pulire le equazioni a ogni passaggio? Ti piacerebbe provare a fare noi stessi la "prova del nove", provando a calcolare se $3^{244}$ in modulo 257 dà davvero 22 usando l'algoritmo di Esponenziazione Veloce (Square and Multiply) che abbiamo studiato all'inizio?

![[Pasted image 20260326100707.png|231]]
![[Pasted image 20260326100654.png|232]]
![[Pasted image 20260326100722.png|294]]## Il Setup: La cassaforte da scassinare

- **Il Gruppo:** $\mathbb{Z}_p^*$ con $p = 2898919$ (un numero primo).
    
- **La Grandezza:** L'ordine del gruppo è $N = p-1 = 2898918$.
    
- **La scomposizione:** Magma scompone questo numerone e scopre il suo punto debole. È formato da tre "mattoncini" primi:
    
    $N = 2^1 \cdot 3^2 \cdot 11^5$
    
- **L'Equazione:** Il generatore è $g=6$. Il messaggio intercettato è $h = 2026326$. Dobbiamo risolvere:
    
    **$6^x = 2026326 \pmod{2898919}$**
    

Poiché la grandezza del gruppo è divisa in tre mattoncini ($2$, $9$ e $161051$), l'hacker sa che può proiettare il problema su tre assi diversi e fare tre mini-logaritmi, per poi unire tutto col Teorema Cinese del Resto.

## Step 1: Il logaritmo Modulo 2 ($x \bmod 2$)

Per isolare il modulo 2, dobbiamo usare il "vaporizzatore" ed elevare tutto alla potenza degli _altri_ mattoncini moltiplicati tra loro, ovvero $3^2 \cdot 11^5 = 9 \cdot 11^5$.

- L'equazione diventa: $(6^{9 \cdot 11^5})^x = h^{9 \cdot 11^5} \pmod p$.
    
- Non vediamo i calcoli di Magma qui, ma il prof scrive che il risultato di $h$ elevato a quel numerone fa **1**.
    
- Quale esponente dà risultato 1? Lo zero.
    
    $\implies \textbf{x = 0} \pmod 2$. Primo pezzo trovato!
    

---

## Step 2: Il logaritmo Modulo 9 ($x \bmod 9$) e lo Schermo Blu!

Per isolare il modulo $3^2$ (cioè 9), eleviamo l'equazione originale alla potenza dei mattoncini rimanenti, ovvero $2 \cdot 11^5$.

- Nuova base target: $6^{2 \cdot 11^5}$
    
- L'equazione è: $(6^{2 \cdot 11^5})^x = h^{2 \cdot 11^5} \pmod p$.
    

**Qui entra in gioco il terminale blu!**

Il professore deve risolvere questo logaritmo, ma invece di farlo a mente, dice a Magma di usare la **forza bruta**.

Guarda il codice sul monitor:

`> for x in [0..8] do`

`for> g^(2*11^5*x);`

`for> end for;`

Sta dicendo al computer: _"Provami tutte e 9 le opzioni possibili (da 0 a 8) calcolando questa potenza, e fammi un elenco dei risultati"_.

Il terminale sputa fuori una lista di 9 numeri. L'ultimo numero in fondo alla lista (che corrisponde all'ottavo tentativo, $x=8$) è **`326323`**.

Se guardi gli appunti cartacei al punto 2, c'è scritto proprio che il bersaglio era diventato $326323$.

Match perfetto! Magma ha trovato la soluzione:

$\implies \textbf{x = 8} \pmod 9$. Secondo pezzo trovato!

---

## Step 3: Il logaritmo Modulo $11^5$ e l'Errore di Copiatura!

Ultimo step: per isolare il modulo $11^5$, eleviamo tutto per gli altri due mattoncini ($2 \cdot 3^2 = 18$).

- Dobbiamo calcolare la nuova base $g_1 = 6^{18}$ e il nuovo bersaglio $h_1 = h^{18}$.
    

**Guardiamo il terminale blu in basso per i risultati:**

Il prof digita:

`> h^(9*2);` (che significa $h^{18}$)

Risultato di Magma: **`2490689`**

Guarda ora gli appunti cartacei al punto 3). Il ragazzo ha scritto:

$(6^{18})^x = \textbf{2190689} = h_1$

**C'è un palese errore di trascrizione!** Ha scritto un 1 al posto del 4. Il numero target corretto è `2490689`.

Subito dopo, sul terminale il prof calcola la nuova base:

`> g^18;`

Risultato: `2433789`.

Quindi l'equazione finale corretta per il punto 3 è:

**$2433789^x = 2490689 \pmod{2898919}$**

## E adesso?

Adesso il problema gigante è diventato questo sistema di equazioni modulari:

1. $x = 0 \pmod 2$
    
2. $x = 8 \pmod 9$
    
3. $x = \text{?} \pmod{11^5}$
    

Per trovare il "?" del punto 3, essendo $11^5$ la potenza di un numero primo, il prof dovrà usare il **Pohlig-Hellman II (Inner step)** che avevamo visto per il trucco binario. Solo che, lavorando in base 11, invece dei bit userà cifre da 0 a 10!

Una volta risolto quello, applicherà il Teorema Cinese del Resto a questi tre risultati e il lucchetto di $h = 2026326$ salterà definitivamente.
_______________________________________________________________________

![[Pasted image 20260326101415.png]]![[Pasted image 20260326101438.png]]
