 Nuova lezione, nuovi disastri crittografici da analizzare. Il professore mantiene la promessa fatta prima: ci mostrerà come il "Textbook RSA" (l'RSA puro da libro di testo) crolla miseramente se viene implementato con pigrizia nel mondo reale. 

Iniziamo la rassegna dei "Classical misuses" (gli usi scorretti classici). Smontiamo questo primo, clamoroso errore!

---

### 1. L'Illusione del Modulo Condiviso (L'Attaccante Interno)

**Testo Originale:**
> **Classical misuses of RSA**
> RSA as it has been introduced in Section 4.3 (textbook RSA) is **not a secure** public-key cryptosystem, unless the attacker's capability are limited to those of condition (5) of Definition 4.1 (i.e. the attacker has no other capabilities than accessing encrypted messages [no capabilities at all!]). [...]
> **A1: use of a shared modulus**
> Assume we have a set of $t$ users who decide to use RSA, each with their own pairs of keys $(e_i, d_i)$ but with the **same RSA modulus $n$**.
> The weakness of this approach is twofold.
> If the **attacker** is a (malicious) **inner** user: assume that the attacker is the $i$-th member of the group [...] Using the algorithm from Theorem 4.16, the $i$-th member can obtain $p, q$ from the knowledge of $(n, e_i, d_i)$. Since $p$ and $q$ are the same for every member, the security of the other private keys $d_j$ for $j \neq i$ is compromised.

**Cosa dice:**
Il professore apre con una doccia fredda: il Textbook RSA è sicuro solo se l'hacker si limita a guardare i pacchetti che passano e basta. Se fa qualcosa di attivo, il sistema è vulnerabile.
Il primo errore clamoroso (A1) è condividere il modulo. Immagina un'azienda pigra che genera un solo enorme modulo $n = p \cdot q$ per tutti i dipendenti. Per differenziarli, l'azienda dà a ogni dipendente una coppia personalizzata di esponenti: la tua chiave pubblica $e_i$ e la tua privata $d_i$.
Se un singolo dipendente diventa cattivo (attaccante interno), il sistema esplode. Il dipendente cattivo conosce il modulo $n$ e la sua chiave privata $d_i$. Applicando l'Algoritmo di Las Vegas (Teorema 4.16) che abbiamo studiato l'ultima volta, la sua chiave $d_i$ gli permette di fattorizzare $n$ e scoprire $p$ e $q$. Essendo $p$ e $q$ identici per tutta l'azienda, l'impiegato traditore ora può calcolarsi le chiavi private di tutti i suoi colleghi!

**Spiegazione dettagliata:**
Questo dimostra quanto RSA sia delicato. La sicurezza non risiede solo nel tenere segreto $d$, ma nell'avere un'infrastruttura indipendente. Se condividi il "terreno di gioco" ($n$), basta che uno solo dei giocatori faccia trapelare i suoi dati per far saltare l'intera scacchiera. Ma il peggio deve ancora venire: cosa succede se ad attaccare non è un dipendente interno, ma uno da fuori?

---

### 2. Il Messaggio Sdoppiato (L'Attaccante Esterno)

**Testo Originale:**
> If the **attacker** is an **outer** user:
> suppose that a member cA of the group wants to send the same message $m$ to two different parties in the group, say $cA_1$ and $cA_2$.
> *(Diagramma con Alice che invia $m$ criptato con $e_1$ e con $e_2$)*
> Assume also that the attacker has reason to believe that cA is sending the same (unknown) message to both $cA_1$ and $cA_2$.
> In this setting cA computes
> $c_1 := m^{e_1} \bmod n \quad (\text{for } cA_1)$
> $c_2 := m^{e_2} \bmod n \quad (\text{for } cA_2)$

**Cosa dice:**
Cambiamo scenario. L'hacker è esterno: non ha nessuna chiave privata e non sa fattorizzare $n$. 
Alice deve inviare la stessa identica circolare top-secret ($m$) a due colleghi, Bob ($e_1$) e Charlie ($e_2$). 
Alice fa il suo lavoro: prende il testo $m$, lo cripta con la chiave di Bob creando il file $c_1$, poi riprende il testo $m$, lo cripta con la chiave di Charlie creando il file $c_2$.
L'hacker intercetta $c_1$ e $c_2$, e sa (o intuisce) che dentro c'è lo stesso documento $m$. Non conosce $m$, ma sta per compiere una magia.

**Spiegazione dettagliata:**
Fermati a riflettere: l'hacker non sa chiacchierare con $p$ o $q$, non ha rubato nulla dal server. Ha solo due scatole chiuse ($c_1$ e $c_2$) che contengono lo stesso oggetto. Poiché le due scatole appartengono alla stessa azienda, sono state chiuse usando lo stesso "lucchetto base" (il modulo condiviso $n$). Questo è il tallone d'Achille che l'hacker sta per colpire.

---

### 3. Il Trucco di Bézout (La Rottura Senza Fattorizzazione)

**Testo Originale:**
> The attacker $\mathcal{A}$, knowing that
> - $c_1$ and $c_2$ encrypt the same message $m$
> - $n_1 = n_2 = n$
> can compute $m$ proceeding as follows:
> 1) if $\gcd(e_1, e_2) \neq 1$, then ABORT
> 2) if $\gcd(e_1, e_2) = 1$, compute $s, t \in \mathbb{Z}$ such that $t \cdot e_1 + s \cdot e_2 = 1$
> 3) compute $c_1^t \cdot c_2^s$.
> We have that
> $c_1^t \cdot c_2^s = m^{e_1 \cdot t} \cdot m^{e_2 \cdot s} = m^{e_1 \cdot t + e_2 \cdot s} = m^1 = m$.

**Cosa dice:**
Ecco l'attacco, pulito e letale. L'hacker procede in 3 step:
1. Controlla se le chiavi pubbliche di Bob e Charlie ($e_1$ ed $e_2$) hanno dei divisori in comune. Se li hanno, l'attacco fallisce.
2. Se non li hanno (quindi $\gcd(e_1, e_2) = 1$), l'hacker usa la sua fidata **Identità di Bézout** (l'algoritmo di Euclide esteso). Trova due numeri interi, $s$ e $t$, in grado di combinare le due chiavi pubbliche in modo che la loro somma pesata faccia $1$.
3. Prende il primo file cifrato ($c_1$) e lo eleva alla $t$. Prende il secondo file cifrato ($c_2$) e lo eleva alla $s$. Li moltiplica tra loro. 

La dimostrazione matematica nell'ultima riga fa quasi paura per la sua semplicità. Sostituendo $c_1$ con $m^{e_1}$ e $c_2$ con $m^{e_2}$, per le regole delle potenze si ottiene $m$ elevato alla $(e_1 \cdot t + e_2 \cdot s)$. Ma per Bézout (step 2), quell'esponente gigante vale esattamente $1$. Il risultato è $m^1$, ovvero il messaggio originale $m$ in chiaro.

**Spiegazione dettagliata:**
Questo è l'apice dell'hacking algebrico. L'hacker **non ha fattorizzato** il numero $n$. Non ha calcolato nessuna chiave privata $d$. Ha letteralmente preso i due file cifrati e li ha "scontrati" l'uno contro l'altro. Poiché le due chiavi pubbliche ($e_1$ ed $e_2$) erano diverse e coprime, combinandole tramite Bézout si sono annientate a vicenda, lasciando il messaggio nudo e crudo.
Questa è la prova inconfutabile di quanto il professore diceva all'inizio del capitolo: *si può rompere RSA senza fattorizzare il modulo*.

---

### 4.Lezione Imparata (Lesson Learned)

**Testo Originale:**
> **Lesson learned:**
> - every user must have their own RSA modulus.
> - we could break RSA without factorizing the modulus (with a little extra power given to the attacker).
> 
> **A2: using a small public exponent in textbook RSA**
> It is a common practice when using real RSA to use a small public exponent $e$ to cut down encryption costs for the sender. This can also lead to security issues...

Il professore fissa le regole d'oro derivate da questo disastro:
1. **Mai, mai, mai condividere $n$.** Ogni singola persona sulla terra deve generare i propri numeri primi $p$ e $q$ e il proprio modulo personale.
2. Abbiamo rotto l'algoritmo usando solo la logica, dimostrando che Textbook RSA è fragilissimo se l'hacker sa intercettare flussi multipli di dati.

E la lezione si chiude con il prossimo bersaglio da smontare (A2): la brutta abitudine degli sviluppatori di scegliere chiavi pubbliche $e$ molto "piccole" (tipo il numero 3) per far fare meno fatica alla CPU dei computer. Spoiler: finirà malissimo.

---

Questa è in assoluto una delle mie vulnerabilità preferite da spiegare. Il professore introduce il secondo errore fatale (A2): l'**Attacco di Håstad** (o Broadcast Attack).

Questo attacco dimostra come l'ossessione per la velocità e le prestazioni possa distruggere la matematica perfetta di RSA.

Smontiamo questo capolavoro pezzo per pezzo!
### 5. La Trappola dell'Efficienza (Il Setup)

**Testo Originale:**

> **A2: using a small public exponent in textbook RSA**
> 
> It is a common practice when using real RSA to use a small public exponent $e$ to cut down encryption costs for the sender. This can also lead to security issues that must be addressed.
> 
> Let us consider a set of **three users** with different private keys $(n_1, d_1), (n_2, d_2), (n_3, d_3)$ but with the same encryption exponent $\mathbf{e=3}$:
> 
> $cA_1 \quad (n_1, 3, d_1)$
> 
> $cA_2 \quad (n_2, 3, d_2)$
> 
> $cA_3 \quad (n_3, 3, d_3)$
> 
> Suppose that someone is sending the same message $\mathbf{m}$ to the three users, computing:
> 
> $c_1 := m^3 \bmod n_1$
> 
> $c_2 := m^3 \bmod n_2$
> 
> $c_3 := m^3 \bmod n_3$

**Cosa dice:**

Calcolare potenze enormi ($m^e$) consuma molta CPU. Per far durare di più la batteria dei cellulari o velocizzare i server, molti sviluppatori impostano la chiave pubblica $e$ a un numero bassissimo, spessissimo $e=3$.

Immagina un capo che invia la stessa identica mail segreta ($m$) a tre dipendenti diversi. Ognuno ha il suo modulo personale ($n_1, n_2, n_3$), ma tutti usano la chiave pubblica $e=3$.

Il capo crea i tre file cifrati ($c_1, c_2, c_3$) elevando il messaggio al cubo per ciascun modulo. L'hacker intercetta questi tre file.

**Spiegazione dettagliata:**

L'hacker non ha nessuna chiave privata e non conosce $p$ o $q$ di nessuno dei tre utenti. Ha solo catturato tre pacchetti dati che contengono la stessa informazione cifrata in tre "lingue" matematiche leggermente diverse. Questo è sufficiente per far scattare la trappola.

---

### 6. Il Teorema Cinese del Resto (L'Equazione di Sistema)

**Testo Originale:**

> Since $\gcd(n_i, n_j) = 1$ for $i \neq j$ (otherwise $\gcd(n_i, n_j)$ is a prime factor of $n_i$ and $n_j$ and the RSA problem is solved), by CRT there exist a solution $x^*$ to the system
> 
> $(*) \begin{cases} x = c_1 \bmod n_1 \\ x = c_2 \bmod n_2 \\ x = c_3 \bmod n_3 \end{cases}$
> 
> which is unique modulo $n := n_1 \cdot n_2 \cdot n_3$.

**Cosa dice:**

L'hacker si accorge che i tre moduli ($n_1, n_2, n_3$) non hanno divisori in comune (se li avessero, farebbe un banale Massimo Comune Divisore e troverebbe i fattori primi, rompendo RSA all'istante!, se il $\gcd(n_1, n_2) > 1$, significa che hai trovato il "punto debole" della catena: un numero primo condiviso $\gcd(n_1, n_2) = p$).

Siccome sono tutti coprimi, l'hacker invoca un mostro sacro dell'algebra: il **Teorema Cinese del Resto (CRT)**.

Il CRT gli garantisce che esiste un unico numero $x$ capace di risolvere tutte e tre le equazioni contemporaneamente. E gli dà anche l'algoritmo esatto per trovarlo in una frazione di secondo. Questo numero $x$ vive in un "super-modulo" pari alla moltiplicazione dei tre moduli originali ($n_1 \cdot n_2 \cdot n_3$).

---

### 7. Il Colpo di Grazia (L'Asterisco Rosso "as integers")

**Testo Originale:**

> Now, since $m < n_i \quad \forall i \in \{1,2,3\}$, $m^3 < n_1 \cdot n_2 \cdot n_3 = n$
> 
> and $m^3$ is obviously a solution of $(*)$.
> 
> Therefore $m^3 = x$ **as integers** $\implies m = \sqrt[3]{x}$

**Cosa dice e cosa significa:**

Questo è il momento in cui cade la mascella.

La regola base di RSA impone che il messaggio $m$ in chiaro sia rigorosamente più piccolo del modulo ($m < n$). Se elevi al cubo qualcosa di più piccolo di tre numeri, il risultato sarà per forza più piccolo della moltiplicazione di quei tre numeri ($m^3 < n_1 \cdot n_2 \cdot n_3$).

Perché questo è letale? Perché significa che **non c'è stato nessun avvolgimento modulare**.

In matematica modulare (come le ore dell'orologio), se fai $14 \bmod 12$ ottieni $2$. C'è stato un avvolgimento.

Ma se fai $5 \bmod 12$, ottieni semplicemente $5$. Non c'è nessun taglio.

Poiché $m^3$ è più piccolo del "super-modulo" $n$, il numero $x$ che l'hacker ha trovato usando il Teorema Cinese del Resto non è una strana congruenza. È letteralmente, **esattamente uguale** al messaggio originale elevato al cubo ($m^3$) come se fossimo nel regno dei normali numeri interi (ecco perché il prof ha sottolineato in rosso **"as integers"**).

**La fine della partita:**

L'hacker prende $x$. Apre la calcolatrice di Windows. Preme il tasto della radice cubica normale ($\sqrt[3]{x}$).

Boom. Il messaggio originale $m$ compare sullo schermo.

Non ha fattorizzato nulla. Non ha rubato nessuna chiave privata $d$. Ha solo sfruttato la pigrizia dell'esponente piccolo combinata all'invio multiplo.

---

Il professore fa esattamente quello che avevamo anticipato. Prende la teoria dell'Attacco di Håstad (Broadcast Attack) e la esegue con numeri reali sulla lavagna, per poi trarre la conclusione definitiva che cambierà per sempre il modo in cui usiamo RSA.

Smontiamo quest'ultima esecuzione e la "Lezione Imparata"!


### 8. L'Attacco in Diretta (Example 4.18)

**Testo Originale:**
> **Example 4.18**
> Let $n_1 = 323, n_2 = 299, n_3 = 341$.
> Let $m = 42$. We obtain
> $\begin{cases} c_1 = 42^3 \bmod 323 = 121 \\ c_2 = 42^3 \bmod 299 = 235 \\ c_3 = 42^3 \bmod 341 = 91 \end{cases} \longrightarrow \text{available to } \mathcal{A}$
> $\mathcal{A}$ can compute the solution of
> $(*) \begin{cases} x = 121 \bmod 323 \\ x = 235 \bmod 299 \\ x = 91 \bmod 341 \end{cases}$
> and obtain $x = 74088$, which is unique $\bmod n_1 \cdot n_2 \cdot n_3$.
> Since $m^3 < n_1 \cdot n_2 \cdot n_3$ is also a solution of $(*)$, then
> $m^3 = x \implies m = \sqrt[3]{74088} = 42$.

**Cosa dice:**
Ecco la rapina in banca documentata passo passo. 
Il messaggio super segreto è $m = 42$. Il mittente lo invia a tre persone diverse, usando i loro moduli. I tre file cifrati che viaggiano su internet sono $121, 235, 91$. 
L'hacker ($\mathcal{A}$) cattura questi tre file. Li infila nel tritatutto del Teorema Cinese del Resto (CRT) impostando il sistema di equazioni $(*)$. 
Il CRT sputa fuori il numero magico: **74088**. 
Ora, facciamo il controllo della "mancata rottura modulare": il super-modulo ($323 \times 299 \times 341$) fa circa 32 milioni. Siccome 74088 è immensamente più piccolo di 32 milioni, sappiamo per certo che non si è "arrotolato". 
L'hacker apre la calcolatrice, fa la radice cubica di 74088 e... boom. Appare il **42**. Messaggio in chiaro recuperato senza sfiorare nessuna chiave privata.

---

### 9. La Falsa Speranza dell'Esponente Alto

**Testo Originale:**
> If we take $e = 251$, we need to collect $251$ encryption of the same message to apply the same argument. In that case though, the numbers involved will become huge!

**Cosa dice:**
Qualcuno potrebbe dire: *"Vabbè, allora basta non usare $e=3$! Usiamo $e=251$ e siamo a posto!"*
Il prof risponde: nì. Se usi $e=251$, per far funzionare questo attacco l'hacker dovrebbe intercettare esattamente lo stesso messaggio inviato a **251 persone diverse**. È molto più difficile e i computer farebbero fatica a maneggiare numeri così titanici. Ma concettualmente, l'algoritmo è ancora fallato. 
Aumentare $e$ è come mettere un cerotto su una diga che sta crollando. Serve una cura definitiva.

---

### 10. La Cura Definitiva: A Morte il Determinismo (Lesson Learned)

**Testo Originale:**
> **Lesson learned:**
> - (again) we could break RSA without factorizing the moduli
> - messages **must be randomized before** or during the encryption, in such a way encrypting twice the same message will produce two different ciphertexts, while keeping decryption deterministic.

**Cosa dice:**
Questa è la regola d'oro della crittografia del mondo reale, la "lezione" che separa l'RSA del libro di testo (Textbook) da quello che usi nel tuo browser HTTPS.
Il problema letale di Textbook RSA è che è **deterministico**: se cripti la parola "CIAO" un milione di volte con la stessa chiave, otterrai lo stesso identico codice incomprensibile un milione di volte. L'hacker usa questa prevedibilità contro di te.

La soluzione? **Randomizzare (Padding)**.
Prima di dare il messaggio in pasto alla funzione $m^e \bmod n$, il computer deve incollare al messaggio una stringa di bit generata totalmente a caso. 
Quindi non invii più $m$, ma invii $m + \text{spazzatura casuale}$. 
In questo modo, se devi inviare lo *stesso* messaggio a 3 persone, ogni persona riceverà in realtà un input matematico *completamente diverso*, perché la spazzatura casuale cambia ogni volta!
Il Teorema Cinese del Resto fallisce miseramente, perché l'hacker non ha più $m^3$ ovunque, ma ha $(m+r_1)^3, (m+r_2)^3, (m+r_3)^3$. L'equazione non si chiude. 

Allo stesso tempo, quando il destinatario decripta, il suo computer legge il file, toglie la spazzatura (in modo *deterministico* e matematico) e ti fa leggere solo il messaggio originale. Questo sistema oggi si chiama **OAEP (Optimal Asymmetric Encryption Padding)** ed è lo standard assoluto.

Abbiamo chiuso il capitolo delle vulnerabilità base di RSA! Il professore ci ha fatto vedere come l'algebra possa essere usata sia come scudo che come spada. Manda pure le prossime diapositive quando sei pronto per il prossimo argomento!


---

 Eccoci all'ultimo e forse più subdolo dei "misuses" classici di RSA. Dopo l'impiegato traditore (A1) e il messaggio sdoppiato (A2), il professore ci presenta **A3: L'Oracolo di Decifratura** (o attacco ad "accecamento").

Questo attacco dimostra una vulnerabilità intrinseca della matematica di RSA: la sua natura omomorfica (cioè il fatto che le moltiplicazioni "passano attraverso" la cifratura). 

Indossiamo il cappuccio nero e smontiamo l'attacco riga per riga!

### 11. Il Setup: Ingannare la Vittima (L'Oracolo)

**Testo Originale:**
> **A3. Offering decryptions for free (or accessing a decryption ORACLE)**
> Assume that an adversary $\mathcal{A}$ has intercepted an encrypted message $c$ sent from $cB$ to $cA$.
> *(Diagramma: $m \to m^e = c \to \text{intercettato da } \mathcal{A} \dots \to cA(n,e,d)$)*
> Assume that $\mathcal{A}$ is able to convince $cA$ to decrypt a random-looking message. (Plausible, since $cA$ may be asked to prove their identity by signing some random message.)

**Cosa dice:**
L'hacker ($\mathcal{A}$) ha rubato un file cifrato $c$ diretto ad Alice ($cA$). Non sa fattorizzare, non ha la chiave privata, non ha messaggi sdoppiati.
Però ha una possibilità: può parlare con Alice. Alice è una persona disponibile, o magari un server automatico, che accetta di "firmare" documenti per dimostrare la propria identità. Ma come abbiamo studiato qualche lezione fa, **firmare un documento significa letteralmente decifrarlo con la propria chiave privata!**
Ovviamente l'hacker non può mandare ad Alice il file $c$ rubato chiedendole "Scusa, mi firmi questo?", perché Alice riconoscerebbe il file top-secret e farebbe scattare l'allarme. 

**Spiegazione dettagliata:**
L'hacker ha bisogno che Alice decripti il file $c$ a sua insaputa. Deve "travestire" il file segreto, facendolo sembrare una sequenza di byte casuali e innocui (un *random-looking message*). Alice lo guarderà, penserà "Ah, è solo un test di connessione", ci applicherà la sua chiave privata $d$ e lo rispedirà all'hacker. Questo concetto in crittografia si chiama **Decryption Oracle** (Oracolo di decifratura).

---

### 12. Il Trucco dell'Accecamento (Blinding)

**Testo Originale:**
> $\mathcal{A}$ can proceed as follows:
> 1) take a random element $1 < k < n, \gcd(k, n) = 1$ *(Nota: used to fool $cA$ hiding the fact that $\mathcal{A}$ is attacking $c$)*
> 2) compute $c^* := k^e \cdot c \bmod n$, that will look like a random element to $cA$, unrelated to $c$
> 3) ask $cA$ to decrypt $c^*$
> 4) obtain $m^*$ (the decryption of $c^*$ made by $cA$).

**Cosa dice:**
Ecco l'attacco di *Blinding* (Accecamento).
1. L'hacker sceglie un numero totalmente a caso, $k$.
2. Usa la chiave pubblica di Alice ($e$) per "criptare" questo numero a caso ($k^e$) e lo moltiplica per il file segreto rubato ($c$). Nasce così un nuovo file "ibrido", $c^*$. Questo nuovo file sembra spazzatura cosmica, totalmente irriconoscibile.
3. L'hacker invia $c^*$ ad Alice: *"Ehi Alice, sono il server, mi firmi questo pacchetto di test per favore?"*
4. Alice applica la sua chiave privata $d$, decripta il file ibrido, e rispedisce il risultato ($m^*$) all'hacker.

---

### 13. Lo Scacco Matto Matematico (L'Estrazione)

**Testo Originale:**
> But
> $m^* = (c^*)^d = (k^e \cdot c)^d = k^{ed} \cdot c^d = k \cdot m$
> and $\mathcal{A}$ can recover $m$ by knowing $k$.

**Cosa dice:**
Qui la matematica di RSA si rivolta contro se stessa.
Quando Alice decripta $c^*$ elevandolo alla $d$, le proprietà delle potenze fanno sì che l'esponente $d$ si distribuisca su entrambi i pezzi del file ibrido.
Il pezzo finto ($k^e$) elevato alla $d$ diventa $k^{ed}$, che (come sappiamo bene) torna ad essere semplicemente **$k$**.
Il pezzo vero ($c$) elevato alla $d$ diventa magicamente il messaggio in chiaro originale **$m$**.
Quindi, il file che Alice rispedisce fiduciosa all'hacker ($m^*$) non è altro che il messaggio segreto $m$ moltiplicato per il numero a caso $k$ dell'hacker!
All'hacker basta prendere il file di Alice, dividerlo per $k$ (usando l'inverso modulare), e ha in mano il messaggio $m$ pulito e in chiaro.

**Spiegazione dettagliata:**
Questa è la cosiddetta **Proprietà Omomorfica Moltiplicativa** di RSA nudo e crudo: la decifratura di una moltiplicazione è uguale alla moltiplicazione delle decifrature. L'hacker ha "infilato" il suo lucchetto dentro a quello della vittima, ha fatto aprire tutto alla vittima e poi ha sfilato il suo pezzo. 
Anche qui, come per l'Esempio A2, la soluzione a questo disastro è il **Padding (Randomizzazione)**: se modifichiamo la struttura matematica del messaggio prima di cifrarlo, questo giochetto della moltiplicazione esplode e l'attacco fallisce.

---

### 14. Il Sigillo Finale sul Textbook RSA

**Testo Originale:**
> We have seen that the security of (textbook) RSA totters as soon as the attacker is given more power than the one from Definition 4.1 (no power at all).
> Let us now consider more realistic scenarios of the attacker's capability.

Il professore chiude il ciclo dei disastri. Ha mantenuto la premessa: ci ha dimostrato che la versione "scolastica" di RSA vacilla (totters) non appena l'hacker decide di fare qualcosa di appena più furbo che stare seduto a guardare lo schermo. 

L'ultima riga ci lancia dritti nel prossimo argomento. Ora che sappiamo che un hacker può manipolare attivamente i file, sdoppiarli o chiedere ad "oracoli" di decifrarli, come diamine facciamo a costruire un sistema che resista a questi scenari realistici?


---

 Dopo averci fatto sporcare le mani nel fango svelando i disastri pratici, il professore ci riporta nell'aula di teoria per alzare l'asticella.

Questa è la **Sezione 4.5: More security notions**. Qui il prof fa un'osservazione logica geniale che cambia completamente il modo in cui giudichiamo la sicurezza di un sistema crittografico.

Smontiamo questa rivelazione!
### 15. Il Paradosso della Chiave Pubblica (Da Passivo ad Attivo)

**Testo Originale:**

> If we think to the attacker capabilities more carefully, we will understand that, in a public-key setting, since encryption is everybody's faculty, the attacker will **always be able** to use the encryption machine if they require so. Therefore, in a public-key setting, the weakest attacker, that is the passive eavesdropper, corresponds to the so-called **chosen-plaintext attacker (CPA)**, who can encrypt as many messages as they want before trying to address the challenge.

**Cosa dice:**

Ti ricordi la Regola 5 della prima lezione? Il "gioco" in cui l'hacker vinceva se riusciva a decifrare il messaggio facendo solo il _passive eavesdropper_ (colui che ascolta passivamente il Wi-Fi).

Il prof ci fa notare un dettaglio clamoroso: **nella crittografia a chiave pubblica, l'hacker puramente passivo non esiste!** Perché? Perché la chiave pubblica (il "lucchetto" di Alice) è... pubblica! Chiunque può scaricarla. Quindi l'hacker non si limita ad ascoltare i messaggi che passano. Se vuole, prende la chiave di Alice, scrive un milione di finti messaggi ("Ciao", "Prova", "Password123"), li cripta, e si fa un gigantesco database per vedere che aspetto hanno da criptati.

**Spiegazione dettagliata:**

Questo trasforma l'hacker da un semplice "spettatore" a un **Chosen-Plaintext Attacker (CPA)**, ovvero un "Attaccante a Testo in Chiaro Scelto". Ha letteralmente in mano il simulatore della vittima e può usarlo per fare tutti i test che vuole prima di provare a craccare il bersaglio vero.

---

### 16. Il Minimo Sindacale (RSA è CPA Secure... a metà)

**Testo Originale:**

> Assuming that the security game of Definition 4.1 is hard is equivalent to saying that **RSA is CPA secure**.
> 
> In modern cryptography, this is a **minimal** requirement for a public-key cryptosystem, but for some applications it will not be enough: we need more advanced definitions of security.

**Cosa dice:**

Il professore ci rassicura (in parte): se assumiamo che il problema matematico della fattorizzazione sia difficile, allora RSA supera il test. È ufficialmente **CPA Secure**. Anche se l'hacker cripta un milione di messaggi di prova, non riuscirà a ricavare l'intero messaggio segreto $m$.

Tuttavia, il prof lancia subito un avvertimento: nel mondo reale di oggi, essere "CPA Secure" in questo modo è considerato solo il **minimo sindacale**. Per proteggere i conti in banca, ci serve molto di più.

---

### 18. Il Cliffhanger: La Caccia al Singolo Bit

**Testo Originale:**

> The definition of CPA formalizes the following requirement: the CP attacker must not be able to recover **the entire message**.
> 
> The next security definition will formalize a stronger requirement: the CP attacker must not be able to recover **any single bit** of the message.

**Cosa dice e cosa significa:**

Ecco la vera debolezza su cui il prof vuole farci concentrare.

Finora abbiamo detto: "RSA è sicuro perché l'hacker non riesce a ricostruire **tutto** il messaggio originale".

Ma pensaci un attimo. E se Alice stesse mandando a Bob un ordine di borsa con scritto solo "COMPRA" o "VENDI"? O un ordine militare "ATTACCA" o "RITIRATA"?

In casi come questo, all'hacker non serve leggere l'intero documento. Gli basta indovinare **un solo bit** di informazione (sì o no, 1 o 0) per vincere.

Siccome Textbook RSA è deterministico (la stessa parola criptata due volte dà sempre lo stesso codice incomprensibile), l'hacker può prendere la chiave pubblica, criptare da solo la parola "ATTACCA" e la parola "RITIRATA", e poi confrontare i due risultati con il messaggio top-secret che ha intercettato.

L'hacker non ha "rotto" RSA calcolando $d$ o $p$ e $q$. Ma ha scoperto esattamente quello che voleva sapere!

**Preparati per la prossima lezione:** Il prof ci introdurrà alla **Sicurezza Semantica**. Una crittografia moderna deve essere così caotica che l'hacker non solo non deve riuscire a leggere l'intero messaggio, ma guardando il file criptato non deve riuscire a dedurre nemmeno mezza sillaba, nemmeno un singolo bit di informazione sul contenuto originale.


---

 Eccoci arrivati al livello "Paranoia Assoluta" della crittografia. Come avevamo anticipato, non ci basta più che l'hacker sia incapace di leggere l'intero messaggio. Vogliamo che sia incapace di dedurre **anche un singolo bit** di informazione.

Per garantire questo, il professore introduce il test definitivo della crittografia moderna: la sicurezza **IND-CPA (Indistinguishability under Chosen-Plaintext Attack)**.

Smontiamo questo "gioco" matematico che condanna a morte il nostro caro Textbook RSA!
### 19. Il Gioco delle Tre Carte (Definition 4.19)

**Testo Originale:**

> **Definition 4.19**
> 
> Let $(Gen, Enc, Dec)$ be a public-key cryptosystem on $(M, C, K)$. Let us consider the following game between a PPT algorithm $\mathcal{A}$ and a challenger $\mathcal{C}$:
> 
> 1. $\mathcal{C}$ runs $Gen(N)$ and obtains $(pk, sk)$
>     
> 2. $\mathcal{A}$ chooses two messages $m_1, m_2 \in M$
>     
> 3. $\mathcal{C}$ flips a random coin and picks $i \in_R \{1, 2\}$
>     
> 4. $\mathcal{C}$ computes $c := E_{pk}(m_i)$ and sends $c$ to $\mathcal{A}$.
>     
> 5. $\mathcal{A}$ outputs $j \in \{1, 2\}$
>     
>     $\mathcal{A}$ wins the game $\iff i = j$.
>     

**Cosa dice:**

Questo è il collaudo finale. L'Hacker ($\mathcal{A}$) e lo Sfidante ($\mathcal{C}$) fanno una scommessa:

1. Lo Sfidante crea le chiavi e pubblica il lucchetto ($pk$).
    
2. L'Hacker sceglie **due messaggi a sua scelta**, completamente diversi (es. $m_1 =$ "COMPRA", $m_2 =$ "VENDI"). Li manda allo Sfidante.
    
3. Lo Sfidante lancia in segreto una moneta (Testa = 1, Croce = 2).
    
4. In base alla moneta, prende uno solo dei due messaggi, lo chiude nella cassaforte con la chiave pubblica, e rimanda la scatola chiusa ($c$) all'Hacker.
    
5. L'Hacker guarda la scatola chiusa e deve indovinare ($j$) quale dei due messaggi c'è dentro. Se indovina, vince.
    

**Spiegazione dettagliata:**

Fermati a riflettere su quanto sia crudele questo gioco per il crittosistema. L'hacker sa _esattamente_ quali sono le due uniche opzioni possibili! Se il sistema ha la minima sbavatura (es. i messaggi lunghi producono file cifrati leggermente più pesanti, o lettere simili producono pattern simili), l'hacker capirà quale dei due è nascosto dentro.

---

### 20. La Formula dell'Ignoranza Assoluta

**Testo Originale:**

> We say that $(Gen, Enc, Dec)$ is **IND-CPA secure** (indistinguishable against a chosen-plaintext attacker) if
> 
> $2 \cdot \left| P(\mathcal{A} \text{ wins the game}) - \frac{1}{2} \right|$ is a negligible function in the security parameter $\forall$ PPT $\mathcal{A}$.

**Cosa dice:**

Quando possiamo dichiarare che un sistema ha superato il test? Quando l'hacker **tira a caso**.

Se l'hacker non ha letteralmente idea di quale messaggio ci sia nella scatola, l'unica cosa che può fare è lanciare una moneta a sua volta. Questo significa che ha esattamente il 50% di probabilità di vincere ($1/2$).

La formula matematica del prof fa proprio questo controllo: prende la probabilità di vittoria dell'hacker, sottrae $1/2$ (il 50%), e ne fa il valore assoluto. Se il sistema è perfetto, questa differenza fa zero (o è "trascurabile"). Se l'hacker vince col 51% di probabilità, il sistema è rotto, perché significa che ha trovato un modo per far pendere l'ago della bilancia.

---

### 21. La Morte del Determinismo (Perché RSA Base fallisce)

**Testo Originale:**

> It is not hard to realize that if we want $(Gen, Enc, Dec)$ to be IND-CPA secure, then the encryption process must be **probabilistic** (repeated twice on the same input can give different results).
> 
> Indeed: _(il testo si interrompe)_

**Cosa dice e cosa anticiperà:**

Il professore trancia un giudizio definitivo: un sistema per superare questo test deve per forza usare la casualità (probabilistico).

Perché? Te lo spiego io completando la frase troncata "Indeed...":

**Infatti, Textbook RSA fallisce il gioco IND-CPA in 2 secondi netti.**

Pensa a cosa farebbe l'hacker se stessimo usando l'RSA base che abbiamo studiato finora:

1. L'hacker manda "COMPRA" ($m_1$) e "VENDI" ($m_2$).
    
2. Lo Sfidante manda indietro il file cifrato $c$.
    
3. Poiché l'RSA base è deterministico, l'Hacker prende la chiave pubblica (che è a disposizione di tutti), si calcola da solo $E_{pk}(m_1)$ e $E_{pk}(m_2)$.
    
4. Guarda quale dei due calcoli è identico alla scatola $c$ dello Sfidante.
    
5. L'hacker vince il 100% delle volte. IND-CPA distrutto.
    

Ecco perché nell'Esempio 4.18 avevamo tratto la "Lesson Learned" di dover **randomizzare** i messaggi aggiungendo spazzatura prima di cifrarli. Se il sistema aggiunge spazzatura casuale (Padding), quando lo Sfidante cifra "COMPRA", il risultato sarà sempre un codice incomprensibile nuovo e imprevedibile. L'hacker non potrà più fare il trucchetto del confronto!


---

Come volevasi dimostrare! Avevamo appena fatto questa esatta deduzione logica alla fine della lezione precedente, e ora il professore la trasforma in un teorema matematico formale, mettendo la pietra tombale sul "Textbook RSA".

Smontiamo questa pagina, che segna la fine di un'era e l'inizio di una nuova avventura crittografica!
### 22. L'Esecuzione Pubblica di Textbook RSA (Proposition 4.20)

**Testo Originale:**

> **Proposition 4.20**
> 
> textbook RSA (Definition 4.9) is **not** IND-CPA secure.
> 
> **Proof**
> 
> Let $m_1, m_2 \in M$ be the two messages produced by the adversary $\mathcal{A}$. At lines 3-4 of the security game of Definition 4.19 the challenger flips a random coin, obtains $i \in \{1, 2\}$ and returns $c := m_i^e \bmod n$.
> 
> The adversary's goal is to recover $i$, so they can proceed as follows:
> 
> 1. $\mathcal{A}$ computes $m_1^e \bmod n$
>     
> 2. $m_1^e = c \implies$ returns 1
>     
>     $m_1^e \neq c \implies$ returns 2.
>     

**Cosa dice:**

Questa è la formalizzazione del trucchetto che abbiamo usato nel simulatore prima.

L'hacker $\mathcal{A}$ sa benissimo quali sono i due messaggi in gioco ($m_1$ e $m_2$) perché li ha scelti lui.

Quando lo Sfidante gli manda la scatola chiusa $c$ (che contiene uno dei due), l'hacker non deve faticare. Prende il primo messaggio ($m_1$), lo eleva alla $e$ col modulo pubblico $n$, e guarda il risultato.

- Se il risultato è identico a $c$, significa che lo sfidante aveva scelto $m_1$. L'hacker urla "1!" e vince.
    
- Se il risultato è diverso da $c$, per esclusione logica, la scatola deve contenere $m_2$. L'hacker urla "2!" e vince.
    

**Spiegazione dettagliata:**

La falla letale qui è il **determinismo**. In matematica, una funzione deterministica dà sempre lo stesso output per lo stesso input. Non essendoci casualità, il calcolo fatto dallo Sfidante e il calcolo fatto dall'Hacker generano la stessa identica sequenza di bit. L'hacker usa la chiave pubblica come uno "scanner" per verificare le sue ipotesi.

---

### 23. La Pagella Finale

**Testo Originale:**

> And $2 \cdot | P(\mathcal{A} \text{ wins the game}) - 1/2 | = 1$ is not negligible. $\blacksquare$
> 
> For now we have (textbook) RSA :
> 
> CPA $\color{blue}{\text{V}}$
> 
> IND-CPA $\color{red}{\text{X}}$

**Cosa dice:**

Il prof calcola il punteggio della formula di sicurezza. Siccome l'hacker vince sempre (probabilità $1$), la formula diventa: $2 \cdot |1 - 0.5| = 2 \cdot 0.5 = 1$. E il numero $1$ è il massimo possibile, tutt'altro che "trascurabile"! Il quadratino nero chiude la dimostrazione senza appello.

La pagella di Textbook RSA è chiara:

- **CPA (Sicuro a metà):** Se l'hacker non conosce le opzioni in anticipo, non riesce a decifrare da zero un intero messaggio pescato a caso nell'universo.
    
- **IND-CPA (Bocciato):** Se l'hacker vuole dedurre anche solo un singolo bit di informazione distinguendo tra due opzioni note (es. "Sì" o "No"), il sistema crolla.
    

---

### 24. Il Nuovo Sfidante: Diffie-Hellman colpisce ancora!

**Testo Originale:**

> We will now introduce another public-key cryptosystem with **native randomized encryption** (thus it can achieve IND-CPA security), which will be based on the hardness of DH-like problems. We will address the IND-CPA security of a modified version of RSA at the end of the chapter.

**Cosa dice:**

Questa è la transizione verso il prossimo capitolo. Visto che RSA "base" è rotto sotto questo punto di vista, il professore lo mette in pausa. Prima di insegnarci come "riparare" RSA (aggiungendo quel famoso _Padding_ casuale di cui parlavamo), vuole farci vedere un sistema crittografico che **nasce già casuale**.

E su cosa si baserà questo nuovo sistema? Sui problemi tipo **DH (Diffie-Hellman)**! Quelli con cui abbiamo aperto l'intero corso (i logaritmi discreti, i gruppi ciclici, ecc.).

_Spoiler crittografico:_ Sta per presentarci il sistema **ElGamal**. È un sistema stupendo in cui, ogni singola volta che cripti una parola, il computer lancia un dado virtuale ("effimero") che rende il testo cifrato completamente diverso dalla volta precedente. IND-CPA è garantito fin dalla fabbrica!

Quando sei pronto a scoprire come trasformare l'accordo di chiavi di Diffie-Hellman in un vero e proprio sistema per scambiarsi messaggi!


---

![[Pasted image 20260420234843.png|153]]![[Pasted image 20260420234849.png|152]]

Il professore ha appena sganciato la bomba atomica finale. Ha preso tutto il programma del corso che abbiamo studiato finora e ci ha tirato sopra una riga gigante. 

Sta parlando della **Minaccia Quantistica** e della **Post-Quantum Cryptography**. 

Rispondo subito alla tua domanda: **Ne abbiamo già parlato negli appunti?**
**Sì e no.** Non avevamo mai nominato i computer quantistici, ma la matematica che vedi alla lavagna per rompere RSA è **esattamente l'Algoritmo di Las Vegas (Teorema 4.16)** che abbiamo "smontato" pochissime pagine fa!

Smontiamo questa lavagna apocalittica per capire perché il prof sta facendo il funerale alla nostra crittografia.

### 25. Il Becchino: L'Algoritmo di Shor (Pannello di destra)

**Cosa c'è scritto:**
> 1994 Shor
> PFP for a quantum computer is polynomial time.
> factoring and discrete log are PFP

**Cosa significa e dove lo colleghiamo:**
Nel 1994, un matematico di nome Peter Shor ha scoperto che se un giorno riusciremo a costruire un Computer Quantistico funzionante, questo computer saprà risolvere un problema specifico chiamato **PFP (Period Finding Problem, Problema della Ricerca del Periodo)** in tempo *polinomiale* (cioè velocissimo, in pochi minuti o secondi).
Il dramma? Shor ha dimostrato che sia la Fattorizzazione (su cui si basa RSA) sia il Logaritmo Discreto (su cui si basa Diffie-Hellman) non sono altro che varianti del problema PFP mascherate. 

Ti ricordi quando abbiamo detto che la Fattorizzazione si risolve al massimo con il "General Number Field Sieve" in tempo sub-esponenziale (lentissimo)? Ecco, per un computer quantistico quel limite non vale. Lui lo risolve istantaneamente.

---

### 26. La Lapide: Post-Quantum Crypto (Pannello centrale)

**Cosa c'è scritto:**
> find $k$
> $x^k = 1$
> POST-QUANTUM CRYPTO
> DH (Cancellato)
> RSA (Cancellato)

**Cosa significa e dove lo colleghiamo:**
Ecco il collegamento diretto con i nostri appunti. Ti ricordi **l'Esempio 4.17 (la Slot Machine di Las Vegas)**? 
In quell'attacco, l'hacker che aveva rubato la chiave privata cercava disperatamente un esponente (lo chiamavamo $k$) tale che $x^k \equiv 1 \pmod n$, per poi poterne fare le radici quadrate e fattorizzare il modulo $n$ scoprendo $p$ e $q$.
Per noi "umani" con computer normali (classici), trovare quel $k$ andando per tentativi su numeri giganti è impossibile senza avere già in mano la chiave privata (la botola). 
**Ma il computer quantistico non ha bisogno della chiave privata!** Sfruttando la fisica quantistica, calcola tutti gli stati contemporaneamente e "sente" il periodo della funzione, sputando fuori quel numero $k$ dal nulla. Una volta che il quantistico ti regala $k$, tu applichi il Teorema 4.16 dei nostri appunti e disintegri RSA.

Ecco perché il prof scrive a caratteri cubitali "POST-QUANTUM CRYPTO" e cancella con una X cattivissima sia **DH** (Diffie-Hellman) che **RSA**. Se domani accendono un computer quantistico su larga scala, questi due algoritmi muoiono sul colpo. 

---

### 27. La Matematica Quantistica (Pannelli laterali)

**Cosa c'è scritto a sinistra:**
> $m = pq$
> $x \in \mathbb{Z}_n^*$
> $f: \mathbb{Z} \to \mathbb{Z}_n^*$
> $r \mapsto x^r$
> $f(r+k) = x^{r+k} = x^r x^k = x^r = f(r)$

**Cosa c'è scritto nell'altra foto a destra:**
> $f(a+x', b+y') = f(a,b) = f(a+x'', b+y'')$
> $g^{a+x'} h^{-b-y'} = g^{a+x''} h^{-b-y''}$
> $... \implies d\log$

**Cosa significa:**
Il professore sta solo formalizzando matematicamente quello che abbiamo appena detto:
* **A sinistra (Morte di RSA):** Definisce la funzione esponenziale modulare $f(r) = x^r$. Mostra che è una funzione *periodica*, cioè che dopo un certo intervallo $k$ si ripete ($f(r+k) = f(r)$). L'algoritmo di Shor trova proprio questo intervallo $k$.
* **A destra (Morte di DH):** Fa la stessa cosa per il Logaritmo Discreto. Imposta una funzione periodica a due variabili basata su $g$ e $h$. Il computer quantistico trova i periodi $(x', y')$ e da questi si ricava immediatamente l'esponente segreto.

### In sintesi:
Il prof ha appena concluso il capitolo della crittografia asimmetrica classica. Ci ha insegnato come funziona RSA, come lo si attacca nel mondo reale, e infine ci ha detto: *"Tutto molto bello, ma preparatevi, perché tra 10 o 20 anni la fisica quantistica raderà al suolo tutto questo, e dovremo inventare sistemi matematici completamente nuovi (Post-Quantum Crypto)"*. 

![[Pasted image 20260424123532.png|382]]


### 1. Cosa c'è scritto sulla lavagna (L'Analisi)

Il professore sta dimostrando una proprietà fondamentale di un modello matematico chiamato **Processo di Poisson**. È un modello usato per contare quanti "eventi random" succedono in un certo periodo di tempo (es. quante automobili passano a un casello, o quanti pacchetti dati arrivano a un server).

**Il Pannello di Destra (Lo Sviluppo di Taylor):**

Guarda le equazioni a destra. Il prof sta calcolando la probabilità che avvengano _almeno 2 eventi_ ($\ge 2$) in un intervallo di tempo minuscolo (chiamato $I_i^m$, che è lungo $\frac{T}{m}$).

Usa la formula della distribuzione di Poisson (quella con $e^{-\lambda \dots}$) e per risolverla usa lo **Sviluppo in Serie di Taylor** dell'esponenziale (trasforma le $e^x$ in somme di frazioni e cancella i termini opposti con delle sbarre diagonali).

Il risultato finale di quello sbarramento è che la probabilità è lentissima, proporzionale al quadrato del tempo $O(\frac{1}{m^2})$.

**Il Pannello di Sinistra (La Conclusione):**

A sinistra, prende quel risultato e lo somma su tutti i piccoli intervalli $m$.

Moltiplicando $m \cdot O(\frac{1}{m^2})$, il risultato tende a zero.

Arriva a scrivere nel riquadro: **$\mathbb{P}(A) \le 0 \implies \mathbb{P}(A) = 0$**.

**Cosa significa in italiano?**

Il prof ha appena dimostrato matematicamente che, in un Processo di Poisson, **due eventi non possono mai accadere nello stesso identico istante**. Se tagli il tempo in fettine infinitamente piccole, la probabilità che due macchine arrivino al casello nello stesso microsecondo spaccato è esattamente zero.

---

### 2. C'è un collegamento con quello che abbiamo fatto?

**Diretto? No.** Nella crittografia a chiave pubblica non ci interessa sapere l'arrivo dei pacchetti nel tempo.

**Indiretto? Sì, uno piccolissimo.** Ti ricordi quando abbiamo studiato il **Paradosso del Compleanno** e le collisioni di Pollard's Rho?

In quell'occasione, il prof per dimostrare quante estrazioni servivano per trovare un doppione, ha dovuto calcolare una probabilità lunghissima e l'ha "semplificata" usando gli esponenziali ($1-x \le e^{-x}$) e facendone un integrale infinito.

Ecco, le abilità di manipolazione delle probabilità e degli esponenziali (Serie di Taylor) che sta usando su questa lavagna sono le stesse che vi sono servite per dimostrare i tempi di esecuzione degli attacchi hacker.

---

### 3. Perché lo stai vedendo (Prospettive future)

Se questo fa parte dello stesso corso (o di un corso parallelo di Ingegneria/Informatica), stai studiando i Processi Stocastici per un motivo ben preciso che vedrai in futuro: **L'Analisi delle Reti e le Code.**

- **Teoria delle Code:** Come fai a dimensionare un server web affinché non crashi? Devi modellare matematicamente come gli utenti si collegano. E indovina un po'? Si collegano in modo casuale nel tempo, seguendo proprio il **Processo di Poisson** che il prof sta spiegando!
    
- **Sicurezza e Traffico:** A volte, gli hacker non cercano di rompere la password matematicamente (come con RSA), ma fanno attacchi DDoS (inondando il server di richieste) o "Timing Attacks" (misurando in quanti millisecondi il server risponde). Per studiare il traffico dati di un server sotto attacco, devi usare esattamente queste equazioni probabilistiche.
