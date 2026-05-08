Iniziamo una nuova avventura. Il professore chiude definitivamente il colossale capitolo sulla crittografia a chiave pubblica (RSA, ElGamal) e volta pagina.

Benvenuti al **Capitolo 5: Private-Key Encryption (Crittografia a Chiave Privata / Simmetrica)**.

Torniamo alle origini: algoritmi velocissimi usati per cifrare enormi quantità di dati, ma con una regola ferrea. Esiste **una sola chiave segreta** per chiudere e aprire il lucchetto.

Smontiamo queste 6 pagine, dove il prof ridefinisce completamente le regole del gioco e la gerarchia degli hacker!

---

### 1. Il Ritorno della Chiave Condivisa (Definition 5.1)

**Testo Originale:**

> **5. PRIVATE-KEY ENCRYPTION**
> 
> [...] methods for efficient encryption of large amounts of data, once a shared key is available (usually the shared private encryption key is obtained by PubKc methods, like DH, RSA, etc...).
> 
> [...] Let us now give a general definition of a private-key cryptosystem, which will have only **one crucial difference** with Definition 4.1 (the absence of public information).

**Cosa dice:**

Il prof contestualizza subito dove ci troviamo nel mondo reale. RSA e Diffie-Hellman sono lenti, quindi li usiamo solo all'inizio per scambiarci una chiave segreta in modo sicuro. Una volta che tu e io abbiamo questa chiave, passiamo alla crittografia a chiave privata (come l'algoritmo AES, che vedremo) per scambiarci i giga di video e documenti, perché è mille volte più veloce.

La Definizione 5.1 è identica a quella vecchia, ma con una differenza enorme: **non c'è più la chiave pubblica**. La funzione `Gen` sputa fuori una sola chiave $k$. Tu usi `Enc(k, m)` per cifrare e io uso `Dec(k, c)` per decifrare. Se l'hacker non ha $k$, è totalmente al buio.

---

### 2. Lo Zoo degli Hacker: Pubblica vs Privata

Nelle pagine successive, il prof disegna una tabella fondamentale che confronta i tipi di attacco tra il mondo Pubblico (PubKc) e quello Privato (PriKc).

**Testo Originale:**

> ||**PubKc**|**PriKc**|
> |---|---|---|
> |Passive|EAV = CPA / IND-CPA|EAV = CO / IND-EAV|
> |Active|CCA / IND-CCA|CPA/IND-CPA, CCA/IND-CCA|
> 
> The weakest attacker against a PriKc is EAV and it is also called a **ciphertext-only attacker (CO)**.

**Cosa dice e cosa significa:**

Ti ricordi la grande rivelazione della scorsa settimana? Nel mondo a chiave pubblica, l'hacker più scarso (quello passivo) in realtà era potentissimo: essendo la chiave pubblica a disposizione di tutti, lui poteva cifrare finti messaggi da solo. Quindi l'attacco "passivo" coincideva con il temibile CPA (Chosen-Plaintext Attack).

Nel mondo a chiave privata, la musica cambia! Siccome la chiave $k$ ce l'abbiamo solo io e te, l'hacker che ci spia sul Wi-Fi è **veramente passivo**. Non può crittografare nulla da solo. È costretto a guardare file incomprensibili senza poter fare test. Questo hacker di "Livello 1" si chiama **CO (Ciphertext-Only)**.

---

### 3. I Livelli di Potere dell'Hacker (L'Escalation)

Il prof elenca (e disegna i diagrammi) della gerarchia di potere degli hacker nel mondo simmetrico. Dalla forma base a quella onnipotente, la catena alimentare è:

**$CO \le KPA \le CPA \le CCA$**

Smontiamoli uno a uno in base ai diagrammi forniti:

- **Livello 1: CO (Ciphertext-Only).** L'hacker cattura il tuo file cifrato $c$. Deve indovinare il messaggio $m$ partendo da zero. È quasi impossibile.
    
- **Livello 2: KPA (Known-Plaintext Attacker).** L'hacker non sceglie i messaggi, ma per qualche motivo ha intercettato vecchie coppie di messaggi in chiaro e relativi file cifrati $(m_1, c_1), (m_2, c_2)$. Cerca di studiarli per estrarre la chiave $k$ o decifrare il nuovo bersaglio.
    
- **Livello 3: CPA (Chosen-Plaintext Attacker).** Qui l'hacker fa un salto di qualità. Ha ottenuto l'accesso a un **Oracolo di Cifratura ($\mathcal{O}_k^{enc}$)**. Magari ha trovato un tuo server mal configurato: l'hacker invia al server la parola "ATTACCO", e il server gliela rispedisce cifrata con la tua chiave segreta $k$. L'hacker può farsi un database infinito di test!
    
- **Livello 4: CCA (Chosen-Ciphertext Attacker).** Il boss finale. L'hacker ha accesso sia all'Oracolo di Cifratura ($\mathcal{O}^{enc}$) sia a un **Oracolo di Decifratura ($\mathcal{O}^{dec}$)**. Può fare _qualsiasi cosa_, tranne chiedere all'Oracolo di decifrare esattamente il file bersaglio (altrimenti sarebbe troppo facile).
    

---

### 4. Il Gioco IND-CCA e la Morte del Determinismo (Di Nuovo!)

Nell'ultima pagina, il professore costruisce il diagramma del collaudo supremo: il gioco **IND-CCA (Indistinguishability under Chosen-Ciphertext Attack)** adattato per la chiave privata.

**Le regole del gioco (Diagramma finale):**

1. L'hacker sceglie due messaggi $m_1, m_2$.
    
2. Lo sfidante lancia la moneta ($b \in \{1,2\}$), cifra uno dei due e gli manda la sfida $c$.
    
3. Durante tutto il gioco, l'hacker può stressare i server inviando messaggi infiniti all'Oracolo di Cifratura ($\mathcal{O}_k^{enc}$) e file cifrati all'Oracolo di Decifratura ($\mathcal{O}_k^{dec}$), ovviamente senza passargli la sfida $c$.
    
4. L'hacker deve indovinare se la sfida $c$ contiene $m_1$ o $m_2$.
    

**Il Cliffhanger Finale:**

> **Testo Originale:**
> 
> Notice that (again) a deterministic PriKc cannot have IND-CCA security (not even IND-CPA) because the attacker can access the encryption oracle.
> 
> In the remainder of the course we will show how PriKc requirements can be met in real-life standards.

Proprio come succedeva con Textbook RSA, il professore lancia un avvertimento categorico: **la crittografia deterministica è morta**.

Se un algoritmo a chiave privata, a parità di chiave $k$, cifra la parola "CIAO" producendo _sempre_ lo stesso identico codice, fallirà il gioco IND-CPA e IND-CCA all'istante.

Perché? Perché l'hacker, sapendo che la scatola misteriosa contiene o $m_1$ o $m_2$, andrà dall'Oracolo di Cifratura, gli farà cifrare $m_1$, e guarderà se il risultato è identico alla scatola misteriosa. Vittoria al 100%!

Anche nel mondo simmetrico dovremo trovare un modo per introdurre un "dado casuale" (come l'$IV$ che avevamo intravisto in Merkle-Damgård) per rendere ogni cifratura unica e irripetibile.

Un'introduzione puramente teorica e tassonomica per mettere in chiaro le definizioni. Quando sei pronto a vedere questi "real-life standards" in azione (probabilmente i famosi Block Ciphers come AES).

---
Il professore fa un tuffo nel passato. Prima di mostrarci le armi moderne della crittografia simmetrica, ci porta in un museo degli orrori crittografici.

Ci presenta due esempi "sbagliati" (oggi totalmente insicuri), ma fa una premessa fondamentale: presi da soli sono debolissimi, ma **se usati come mattoncini all'interno di sistemi più grandi, diventano i pilastri della sicurezza moderna** (spoiler: sta parlando di AES, lo standard mondiale attuale!).

Smontiamo il primo di questi "dinosauri": il **Cifrario a Sostituzione**.

### 5. Il Dizionario Segreto (La Definizione)

**Testo Originale:**

> **Substitution cipher**
> 
> $\mathbb{A} = \{A, B, C, \dots, X, Y, W, Z\}$, $\#\mathbb{A} = 26$
> 
> $M := \mathbb{A}^\infty$
> 
> $K := \{f \mid f: \mathbb{A} \to \mathbb{A}, f \text{ is bijective}\}$
> 
> the set of all the permutations of $\mathbb{A}$ [...]

**Cosa dice:**

Dimentica i numeri primi giganti per un attimo, torniamo alle lettere dell'alfabeto ($\mathbb{A}$).

Il messaggio $M$ è semplicemente un testo lungo a piacere fatto di lettere.

La vera magia (o illusione) sta nella **Chiave $K$**. La chiave non è una password come "ciao123", ma è una **funzione biiettiva $f$**, ovvero un'intera tabella di permutazione dell'alfabeto. In pratica, decidi che la 'A' diventa 'Q', la 'B' diventa 'F', la 'C' diventa 'Z', e così via per tutte e 26 le lettere, senza mai ripeterne una.

---

### 6. L'Illusione dell'Infinito (Il Numero di Chiavi)

**Testo Originale:**

> $\#K = 26! \sim 2^{88}$

**Cosa dice e cosa significa:**

Questo è il passaggio dove i crittografi del passato si sentivano invincibili.

Quante di queste tabelle di conversione esistono? Il calcolo combinatorio ci dice che sono il fattoriale di 26 ($26!$).

Questo numero è un mostro: equivale a circa **$2^{88}$ chiavi diverse**.

Per darti un'idea, se un hacker provasse a fare un attacco "Brute Force" (provando tutte le combinazioni una per una come un lucchetto della bici), ci metterebbe secoli anche con i computer moderni!

Su carta, questo sistema sembra impenetrabile.

---

### 7. La Fabbrica di Testo (Le Funzioni `Gen`, `Enc` e `Dec`)

**Testo Originale:**

> Gen: picks a random key $f \in K$ (a random permutation of the alphabet).
> 
> Enc: on input a key $f$ and a message $m \dots$ returns
> 
> $Enc(f, m) = f(L_1) f(L_2) \dots f(L_t)$
> 
> Dec: [...] returns $Dec(f, c) = f^{-1}(R_1) f^{-1}(R_2) \dots f^{-1}(R_t)$

**Cosa dice:**

La meccanica è banalissima, è letteralmente il "Gioco dell'Enigmistica":

- **Gen:** Il computer mescola l'alfabeto a caso e crea la tua tabella segreta $f$.
    
- **Enc (Cifratura):** Prendi il tuo messaggio lettera per lettera ($L_1, L_2 \dots$). Guardi la tabella $f$ e sostituisci ogni lettera con la sua corrispondente cifrata.
    
- **Dec (Decifratura):** Chi riceve il testo incomprensibile, usa la tabella al contrario ($f^{-1}$). Se vede una 'Q', guarda la tabella, scopre che la 'Q' originale era una 'A', e la sostituisce.
    

### 8.Il Bug di Sistema (Perché è considerato "sbagliato"?)

Guarda bene la formula della cifratura: **$f(L_1)$ è sempre uguale a $f(L_1)$**.

Questo cifrario è **spietatamente deterministico a livello di singola lettera**. Se la parola è "ATTACCO", le due 'T' diventeranno sempre la stessa lettera cifrata (es. "X"), e le due 'C' diventeranno sempre un'altra lettera gemella (es. "Y"). La parola cifrata avrà la forma _ X X _ _ Y Y _.

Questa struttura fissa permette agli hacker di distruggere l'immenso spazio di $2^{88}$ chiavi in pochi secondi senza usare il Brute Force, ma usando l'**Analisi delle Frequenze**.

Per farti capire quanto sia evidente questo difetto strutturale a occhio nudo, ho preparato un simulatore del Cifrario a Sostituzione. Prova a scrivere una parola con molte doppie o lettere ripetute (come "PRECIPITEVOLISSIMEVOLMENTE") e guarda come il pattern si trasferisce intatto nel testo cifrato!

Esattamente come avevamo intuito alla fine della scorsa lezione, il determinismo di questo cifrario è la sua condanna a morte. Ma il professore alza incredibilmente il livello: non ci insegna solo a romperlo "a mano", ci mostra come **creare un algoritmo per far fare il lavoro sporco al computer da solo**.

Smontiamo questa genialata crittografica e l'introduzione agli Algoritmi Evolutivi!

---

### 9. Il Tradimento della Lingua (Theorem 5.2 e Tabelle)

**Testo Originale:**

> **Theorem 5.2 [informal]**
> 
> If, in the game of CO-security, the messages $m \in M$ are distributed with a sufficiently non-uniform distribution, then the substitution cipher is not CO-secure.
> 
> [...] the substitution cipher is weak w.r.t. the **frequencies analysis attack**.

**Cosa dice:**

Il teorema è spietato. Un hacker di livello base (CO, che possiede solo il testo cifrato) può distruggere il cifrario se la lingua originale non è puramente casuale. E nessuna lingua umana è casuale!

Come mostra la tabella del prof, in inglese la lettera 'E' compare il 12.31% delle volte, la 'T' il 9.59%, mentre la 'Z' o la 'Q' sono rarissime (distribuzione non uniforme).

Poiché _la stessa lettera viene cifrata sempre nello stesso modo_, la statistica si trasferisce intatta: se nel testo incomprensibile il simbolo 'X' compare il 12% delle volte, l'hacker sa già che 'X' = 'E'.

---

### 10. Oltre la Singola Lettera: I Bigrammi

**Testo Originale:**

> Assume that the **bigram** distribution $M \in [0,1]^{26 \times 26}$ of the plaintext language is known.
> 
> $\hookrightarrow$ groups of 2 consecutive letters. Bigrams of "hello" are he, el, ll, lo.

**Cosa dice:**

Contare le singole lettere va bene per testi lunghissimi, ma per messaggi brevi la statistica potrebbe ingannarci. I crittografi usano un'impronta digitale molto più precisa: i **Bigrammi**.

Invece di guardare una lettera alla volta, guardiamo le _coppie_. In inglese, la coppia "TH" o "HE" è frequentissima. La coppia "QZ" non esiste.

Il computer memorizza questa impronta digitale perfetta in una grande matrice matematica $M$ (26x26).

---

### 11. L'Attacco Evolutivo (Darwin incontra gli Hacker)

**Testo Originale:**

> We will attack this cipher by means of an **evolutionary approach**:
> 
> [...] is $m^*$ in English? (hard to ask to a machine)
> 
> is the bigram distribution of $m^*$ "close" to M? (easy to ask)

**Cosa dice e come funziona l'attacco:**

Ecco la vera magia. Come diciamo a un computer di decifrare il testo da solo, provando e scartando le chiavi?

Non possiamo chiedere al computer: _"Ehi, questo testo decifrato ti sembra avere un senso in inglese?"_, perché i computer non capiscono il significato delle parole (senza scomodare modelli linguistici enormi).

Ma possiamo fargli una domanda matematica: _"Ehi, la matrice dei bigrammi di questo testo decifrato assomiglia alla matrice $M$ dell'inglese vero?"_

Qui entra in gioco il fantastico diagrammino con i robot muscolosi e i razzi: **Gli Algoritmi Evolutivi**.

Funziona letteralmente come la selezione naturale di Darwin:

1. **Creazione:** Il computer genera 100 chiavi a caso (100 robotetti sgangherati).
    
2. **Valutazione (Fitness):** Usa le 100 chiavi per decifrare il testo. Poi misura la distanza matematica tra i bigrammi ottenuti e l'inglese vero.
    
3. **Selezione:** I robotetti che hanno prodotto testi che assomigliano _anche solo vagamente_ all'inglese (magari hanno azzeccato 3 o 4 lettere) sopravvivono ("Nice!"). Gli altri vengono cestinati ("Aw, dang").
    
4. **Ricombinazione e Mutazione:** I robotetti sopravvissuti si "accoppiano", mescolando parti delle loro tabelle crittografiche (Recombination), e applicando scambi casuali di lettere (Mutation) per creare la generazione successiva.
    
5. **Loop:** Si ripete! Generazione dopo generazione, la chiave "evolve" e si adatta, finché il testo decifrato combacia perfettamente con le statistiche dell'inglese. Il computer ha trovato la chiave corretta senza che nessun umano sia mai intervenuto!
    

Questo dimostra perché il Cifrario a Sostituzione è un giocattolo. Siamo pronti per il secondo "wrong example" del professore.

---
Ecco il "motore" del nostro algoritmo hacker. Il professore ci mostra le esatte istruzioni matematiche da dare al computer per fargli craccare il Cifrario a Sostituzione in totale autonomia.

Questa è la traduzione in codice della selezione naturale di Darwin. Smontiamo i 6 passi dell'evoluzione!
### 12. Il "Termometro" dell'Inglese (Definition 5.4)

**Testo Originale:**

> The distance between two bigram distributions $M, D \in [0, 1]^{26 \times 26}$ is defined as follows:
> 
> $d(M, D) = \sum_{i,j=1}^{26} | M_{ij} - D_{ij} |$

**Cosa dice:**

Come fa il computer a capire se sta andando nella direzione giusta? Usa questa formula per calcolare la **Distanza ($d$)**.

- **$M$** è la matrice perfetta (il "modello") dell'inglese vero. Sappiamo esattamente quanto spesso compaiono coppie come "TH" o "ER".
    
- **$D$** è la matrice del testo che il computer ha appena provato a decifrare.
    
    La formula prende le frequenze della matrice $M$, le sottrae a quelle della matrice $D$, ne fa il valore assoluto (per togliere i segni negativi) e somma tutti i 676 risultati ($26 \times 26$).
    
- **Se $d$ è un numero enorme:** Il testo decifrato è spazzatura illeggibile.
    
- **Se $d$ è vicino a 0:** Bingo! Il testo ha le stesse identiche frequenze dell'inglese. Abbiamo trovato la chiave.
    

---

### 13. Il Ciclo della Vita (L'Algoritmo)

Il prof descrive l'algoritmo genetico passo dopo passo. L'hacker ha in mano il file cifrato $c$.

1. **La Creazione:** `picks a random key r1`. Il computer crea la primissima tabella di decifratura sparando lettere totalmente a caso (il _Genitore_).
    
2. **La Mutazione:** `define r2 ... equal to r1 except that two elements are swapped`. Il computer clona il Genitore, ma commette un "errore di copiatura" volontario: scambia di posto due lettere a caso (nell'esempio del prof, scambia la **S** con la **X**). Nasce così il _Figlio_ ($r_2$).
    
3. **Il Test del Genitore:** `decrypt c using r1 and compute ... D1`. Il computer usa la chiave del Genitore per decifrare il testo e ne calcola la Distanza $D_1$ dall'inglese perfetto.
    
4. **Il Test del Figlio:** `decrypt c using r2 and compute ... D2`. Fa la stessa identica cosa con la chiave del Figlio e ottiene la Distanza $D_2$.
    
5. **La Selezione Naturale (Mors tua vita mea):** `if d(D1, M) <= d(D2, M), continue with r1, otherwise with r2`. Questa è la spietata legge dell'evoluzione. Il computer guarda i due punteggi. Chi ha il punteggio di distanza più basso vince, perché significa che ha prodotto un testo più simile all'inglese. Il perdente viene cancellato per sempre dalla memoria.
    
6. **L'Eterno Ritorno:** `Iterate 2-5 until d is sufficiently small`. Il vincitore diventa il nuovo Genitore. Subisce una nuova mutazione, affronta un nuovo Figlio, e il ciclo si ripete migliaia di volte al secondo finché il punteggio $d$ non crolla a zero e il testo appare in chiaro sul monitor.
    

Per farti toccare con mano l'eleganza di questo scontro generazionale (Passi 2-5), ti ho preparato un piccolo simulatore dell'Algoritmo Evolutivo. Prova a far mutare la chiave e guarda chi sopravvive!

Questo è il motivo per cui nessuno usa più i cifrari a sostituzione semplice! I computer li mangiano a colazione usando nient'altro che la statistica e le leggi di Darwin.
