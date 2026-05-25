
La pagina si apre con l'epitaffio definitivo sul Cifrario a Sostituzione e introduce il secondo "dinosauro" della crittografia: il leggendario **One-Time Pad (OTP)**.

Smontiamo questa lezione!

---

### 1. L'Epitaffio del Cifrario a Sostituzione

**Testo Originale:**

> We have learned that:
> 
> small perturbation in the key = small perturbation in the ciphertext $\implies$ the key guess can be improved $\implies$ No CO-security!

**Cosa dice:**

Il professore riassume il motivo per cui l'algoritmo evolutivo della lezione precedente funziona così bene. Se tu cambi una sola lettera nella chiave (piccola perturbazione), il testo decifrato cambia solo un pochino. Questo permette al computer di capire se si sta "scaldando" o "raffreddando" mentre gioca a indovinare.

Questa prevedibilità distrugge la sicurezza. Verdetto finale: **Nessuna sicurezza CO** (Ciphertext-Only).

---

### 2. Il Secondo "Wrong Example": Il One-Time Pad (OTP)

**Testo Originale:**

> **One-time pad**
> 
> OTP is another "classical" cipher and maybe one of the first for which it could be possible to achieve a proof of security (in some sense).
> 
> It has (too) many limitations that makes it completely useless as it is, but it is important from a didactic perspective, since it appears, as the substitution cipher, in modern constructions.

**Cosa dice:**

Entra in scena il One-Time Pad (Cifrario di Vernam). Al contrario del cifrario a sostituzione (che era pratico ma insicuro), l'OTP è l'esatto opposto: **è matematicamente perfetto, ma è un incubo da usare nella vita reale.**

Perché lo studiamo? Perché, come dice il prof, il "motore" matematico dell'OTP è letteralmente il cuore di tutti i cifrari moderni (come l'AES o il ChaCha20) che proteggono le nostre connessioni internet oggi.

---

### 3. La Matematica dell'OTP: Il Trionfo dello XOR

**Testo Originale:**

> Given a security parameter $N$:
> 
> $M = K = \{0,1\}^N$
> 
> Gen: takes a random key $k = k_1 k_2 \dots k_N, k_i \in \{0,1\}$
> 
> Enc: given $m = m_1, m_2 \dots m_N$ and a key $k$ as above returns
> 
> $c := m \oplus k$ (bit-wise XOR of $m$ and $k$)
> 
> $= m_1 + k_1 \bmod 2 \parallel m_2 + k_2 \bmod 2 \parallel \dots \parallel m_N + k_N \bmod 2$.
> 
> Dec: given $c = c_1 c_2 \dots c_N$ and $k = k_1 k_2 \dots k_N$ returns $m = c \oplus k$.

**Cosa dice:**

Dimentica le lettere dell'alfabeto, qui si lavora a basso livello, sui bit.

Sia il messaggio $m$ che la chiave $k$ sono stringhe di bit esattamente della stessa lunghezza ($N$).

- **Gen:** Il computer lancia una moneta $N$ volte per creare una chiave $k$ di bit totalmente casuali.
    
- **Enc:** L'algoritmo di cifratura è l'operazione logica **XOR** (indicata col simbolo $\oplus$). Prendi il primo bit del messaggio, lo sommi al primo bit della chiave e fai il modulo 2. Se i bit sono uguali il risultato è 0, se sono diversi è 1. Ripeti per ogni bit ($\parallel$ significa concatenazione).
    
- **Dec:** La magia dello XOR. Se fai lo XOR tra il testo cifrato $c$ e la _stessa_ chiave $k$, i bit si capovolgono di nuovo esattamente al loro posto originale, restituendo il messaggio $m$ intatto.
    

---

### 4. Le Due Regole di Ferro (e il Cliffhanger)

**Testo Originale:**

> When, as long as we assume that
> 
> - the key is as long as the message
>     
> - the key is used only once
>     
> 
> it is possible to prove: _(la pagina si interrompe)_

**Cosa dice:**

Perché l'OTP è "inutile" come sistema a sé stante? Per colpa di questi due requisiti drastici scritti dal professore:

1. **La chiave deve essere lunga quanto il file:** Se vuoi mandare un film da 2 Giga a un amico in modo totalmente sicuro con OTP, devi prima incontrarlo di persona e consegnargli una chiavetta USB con 2 Giga di numeri generati a caso. A quel punto, facevi prima a dargli direttamente il film!
    
2. **La chiave si usa UNA VOLTA SOLA ("One-Time"):** È il requisito vitale. Se usi la stessa chiave per cifrare due messaggi diversi, la matematica dello XOR fa crollare tutto e l'hacker può decifrare entrambi i testi istantaneamente.
    

La pagina si interrompe su _"it is possible to prove:"_. Sicuramente la prossima slide inizierà dimostrando il miracolo matematico dell'OTP: la **Perfect Secrecy (Segretezza Perfetta)**. 


---

Entriamo nel vivo della lezione. Il professore qui ci porta sulle montagne russe: prima ci mostra che il One-Time Pad (OTP) è matematicamente perfetto, e un secondo dopo ci dimostra come un solo errore umano possa trasformarlo in un disastro totale.

Smontiamo questa montagna di algebra e disegni!

### 5. Il Sacro Graal: Il Teorema di Shannon

**Testo Originale:**

> **Theorem 5.5 (Shannon) [informal]**
> 
> When using OTP, an attacker intercepting a ciphertext $c = E_k(m)$ obtains "knowledge" of $m$ that is equal to the a-priori knowledge they have on $m$. (Entropy)

**Cosa dice:**

Questo è il famoso concetto di **Segretezza Perfetta (Perfect Secrecy)** formulato da Claude Shannon.

Significa che guardare il file cifrato dell'OTP non ti dà letteralmente _nessuna_ informazione in più rispetto a quella che avevi prima di guardarlo. Se l'entropia (il disordine) è assoluta, il testo cifrato è rumore bianco puro.

Se rispetti le due regole d'oro (chiave lunga quanto il testo e usata una volta sola), il livello CO (Ciphertext-Only) è un muro di cemento armato invalicabile. Neanche un computer quantistico con tempo infinito può craccarlo, perché non c'è matematica da invertire: ci sono solo infinite probabilità tutte ugualmente valide.

---

### 6. La Fragilità (La Morte per KPA)

**Testo Originale:**

> Moreover, CO-security is the maximum level of security that we can reach. In a known plaintext scenario the attacker computes $k = m \oplus c$.

**Cosa dice:**

L'OTP è invincibile al livello 1 (CO), ma crolla istantaneamente al livello 2 (KPA - Known Plaintext Attack).

Se l'hacker riesce a mettere le mani su _un solo_ vecchio messaggio in chiaro ($m$) e sul suo relativo testo cifrato ($c$), ha vinto.

Perché? Per la magia dell'operatore logico XOR ($\oplus$). Se $c = m \oplus k$, basta spostare i termini per scoprire che **$k = m \oplus c$**.

L'hacker fa lo XOR tra il file in chiaro e il file cifrato, e la chiave segreta gli cade letteralmente in mano. Non ha dovuto nemmeno calcolare niente!

---

### 7. Il Disastro del "Two-Time Pad" (I Disegnini)

**Testo Originale:**

> ## Or, if the key is reused (even in a CO-scenario): $m_1 \oplus k = c_1$ $m_2 \oplus k = c_2$
> 
> $m_1 \oplus m_2 = c_1 \oplus c_2$ ($m_1 + m_2$ is leaked)

**Cosa dice e cosa significano i disegni:**

Questo è l'errore umano più mortale della crittografia: **riutilizzare la chiave OTP per due messaggi diversi**.

Se io cifro il messaggio 1 ($m_1$) con la chiave $k$, e poi cifro il messaggio 2 ($m_2$) con la _stessa_ chiave $k$, l'hacker che intercetta i due file cifrati ($c_1$ e $c_2$) fa una cosa semplicissima: **li somma tra loro (XOR)**.

Guarda l'algebra: $c_1 \oplus c_2 = (m_1 \oplus k) \oplus (m_2 \oplus k)$.

Siccome fare lo XOR di una cosa con se stessa dà sempre zero ($k \oplus k = 0$), la chiave scompare dall'equazione! Il risultato è **$m_1 \oplus m_2$**.

Il prof lo disegna perfettamente:

1. Hai l'immagine di un quadrato con dei puntini ($m_1$) e un'immagine con una riga storta ($m_2$).
    
2. Le cifri con la stessa maschera di rumore casuale (il quadrato verde/azzurro).
    
3. Entrambi i cifrati ($c_1, c_2$) sembrano rumore bianco.
    
4. L'hacker li somma ($c_1 \oplus c_2$). Il rumore si cancella da solo, e l'hacker vede **il quadrato a puntini sovrapposto alla riga storta**. Ha perso la chiave, ma ha recuperato in chiaro l'impronta strutturale di entrambi i messaggi!
    

---

### 8. La Radice del Male: La Linearità

**Testo Originale:**

> this issue comes from the fact that OTP acts as an affine transformation of the message.
> 
> [...] $c_1 + c_2 = A(m_1 + m_2) \implies m_1 + m_2 = A^{-1}(c_1 + c_2)$
> 
> non-linear $\implies f(x+y) \neq f(x) + f(y)$

**Cosa dice:**

Nell'ultima pagina, il professore dà una spiegazione matematica formale a questo disastro.

Il problema dell'OTP è che lo XOR è un'operazione **Lineare / Affine**.

Nelle funzioni lineari vale la regola: la funzione della somma è uguale alla somma delle funzioni ($f(x+y) = f(x) + f(y)$). Questo significa che le operazioni "passano attraverso" la crittografia. Sommare i testi cifrati fuori dalla scatola equivale a sommare i testi in chiaro dentro la scatola.

**La soluzione per i cifrari moderni?** Lo scrive nell'ultima riga. Dobbiamo inventare funzioni di crittografia **Non-Lineari**. Sistemi in cui mescolare due cifrati restituisce spazzatura assoluta e non svela nulla dell'interno. È qui che entreranno in gioco i "Block Ciphers" moderni!

Per farti vedere con i tuoi occhi quanto sia letale riutilizzare una chiave OTP, ti ho preparato un simulatore visuale. Prova a disegnare due messaggi segreti e impersona l'hacker che li sovrappone

---

Dimentichiamo i dinosauri teorici. Il professore ci fa fare un salto quantico e ci porta nel presente.

Benvenuti nel mondo dei **Cifrari a Blocchi (Block Ciphers)**. Questa è l'architettura esatta su cui si basa tutta la crittografia simmetrica moderna (come il famoso AES che protegge il tuo Wi-Fi e le tue password oggi).

Smontiamo questa nuova architettura e il diagramma a cascata!


### 9. Affettare il Messaggio (L'Idea di Base)

**Testo Originale:**

> **5.2 Block ciphers**
> 
> With the aim of building a PriKc on $M = \{0,1\}^\infty$, we set an alphabet $V := \{0,1\}^n$ and we encrypt each message $m \in \{0,1\}^\infty$ by splitting it into **blocks** of size $n$ (elements of $V$).

**Cosa dice:**

Invece di cercare di ingoiare un intero file da 1 Giga in un colpo solo, o di cifrare un singolo bit alla volta (come faceva il One-Time Pad), il cifrario a blocchi trova il compromesso perfetto.

Prende il file infinito e lo "affetta" in blocchi di dimensione fissa $n$ (ad esempio, blocchi da 128 bit). La crittografia lavorerà su un singolo mattoncino alla volta.

---

### 10. La Fabbrica delle Permutazioni (Definition 5.6)

**Testo Originale:**

> Let $V = \{0,1\}^n$, $K$ be a key space $\#K = 2^e$.
> 
> A **block cipher** on $V$ is a family of encryption functions $\{E_k: V \to V, k \in K, E_k \text{ is bijective}\}$
> 
> such that $\phi: K \to Sym(V), k \mapsto E_k$ is efficiently computable.
> 
> **The encryption functions of the cipher must not be linear.**

**Cosa dice:**

Questa definizione ricorda molto il vecchio cifrario a sostituzione (quello dell'alfabeto), ma pompato agli steroidi.

Esiste un universo matematico enorme contenente tutte le possibili permutazioni ($Sym(V)$) con cui puoi mescolare un blocco di 128 bit. Sono talmente tante che il numero supera gli atomi nell'universo.

La tua **Chiave Segreta ($k$)** funziona come un "puntatore". Quando scegli una chiave, stai selezionando una specifica e precisissima funzione di mescolamento ($E_k$) da quell'universo infinito. E siccome è biiettiva, può essere eseguita al contrario per decifrare.

**La Regola d'Oro:** Il prof lo scrive in modo perentorio: _le funzioni non devono essere lineari_. È la lezione che abbiamo imparato a caro prezzo distruggendo l'OTP nella pagina precedente! L'algebra deve essere così caotica che sommare due testi cifrati non deve avere alcun senso logico.

---

### 11. L'Unione fa la Forza (Il Cifrario Iterato)

Come costruiamo una funzione matematica che sia caotica, non-lineare e velocissima da calcolare per il processore?

Il professore ci illustra l'idea geniale di Claude Shannon: il **Cifrario Iterato**. Invece di inventare una singola, mostruosa e lentissima equazione matematica, creiamo una funzioncina facilissima e "debole" (Weak), e **la ripetiamo decine di volte di fila**.

Guarda il diagramma della seconda pagina, è una catena di montaggio perfetta. Si divide in due parti:

1. **Il Key-Schedule (L'Espansione della Chiave):** Tu hai una sola password principale ("Private key / Master key"). Il computer la prende e la "stira", generando una serie di sotto-chiavi diverse, chiamate **Round Keys ($k_1, k_2, \dots, k_R$)**. Ce n'è una per ogni ciclo della macchina.
    
2. **La Catena di Montaggio (Le Iterazioni/Rounds):** * Il blocco di messaggio $m$ entra nella prima scatola "WEAK" (Round Function). Qui viene mescolato insieme alla prima sotto-chiave $k_1$.
    
    - Il risultato non è ancora sicuro, è solo un po' sporco. Ma non esce! Viene spinto immediatamente nella seconda scatola "WEAK", dove viene rimescolato usando la seconda sotto-chiave $k_2$.
        
    - Il processo si ripete per $R$ cicli (iterazioni).
        

**L'Analogia dell'Impasto:** Pensa a un panettiere che fa la pasta sfoglia. Il singolo passaggio è "debole" e banale: stende la pasta, ci mette il burro e la piega a metà. Ma se ripete questo singolo passaggio per 10 o 20 volte di fila (iterazioni), gli strati diventano milioni e si crea una struttura complessa e inseparabile.

Alla fine dei round, il blocco di dati originale $m$ è talmente "ripiegato" su se stesso con le diverse sotto-chiavi che l'output finale $c$ è una massa di rumore bianco crittograficamente indistruttibile.

Spettacolo puro. Scommetto che nella prossima lezione il professore "aprirà" una di quelle scatole WEAK per farci vedere esattamente che tipo di frullatore ci sta dentro (spoiler: reti di Feistel o SPN!). 


---
Hai chiesto precisione maniacale, e precisione maniacale avrai.

Abbandoniamo la teoria e mettiamo le mani nel motore di un algoritmo crittografico vero, usato nel mondo reale per dispositivi leggeri (IoT, smart card): **PRESENT** (introdotto nel 2007).

PRESENT è un classico esempio di **SPN (Substitution-Permutation Network)**. Il professore qui ci descrive esattamente come i concetti "deboli" che abbiamo visto prima (sostituzione e XOR) vengano fusi insieme per creare un bunker impenetrabile.

Analizziamo l'architettura millimetro per millimetro.

### 12. Le Specifiche Tecniche (La Carta d'Identità)

Prima di accendere il motore, dobbiamo definire le dimensioni dei pezzi:

- **Dimensione del Blocco ($n$):** 64 bit. Il file infinito viene tagliato a fette da 64 bit ($\mathbb{F}_2^{64}$).
    
- **Dimensione della Chiave Master ($l$):** 80 bit (PRESENT-80).
    
- **Numero di Round ($R$):** 31 round. L'operazione "debole" verrà ripetuta 31 volte.
    
- **Sotto-chiavi (Round Keys):** Il sistema espanderà la chiave master da 80 bit per generare 32 sotto-chiavi da 64 bit ciascuna ($k_0, k_1, \dots, k_{31}$).
    

---

### Fase 1: Anatomia del Singolo Round (La Funzione Debole)

Il cuore del cifrario a blocchi è il _Round Function_. Prende i 64 bit, li mescola, e li passa al round successivo. Ogni round è composto da 3 livelli (Layer):

#### 1. Il Livello di Sostituzione (S-BOX) - _L'Origine del Caos_

Il blocco da 64 bit viene mentalmente diviso in **16 mattoncini da 4 bit ciascuno** (i _bricks_).

Ogni mattoncino entra in una scatola nera chiamata **S-Box (Substitution Box)**.

- **Cosa fa:** Prende 4 bit in ingresso (un numero da 0 a F in esadecimale) e sputa fuori 4 bit in uscita secondo una tabella cablata (es. se entra 0 esce C, se entra 1 esce 5, se entra 5 esce 0, ecc.).
    
- **Perché è fondamentale:** Come sottolinea il prof, l'S-Box è **NON-LINEARE**. È l'unico punto di tutto l'algoritmo in cui la matematica non segue regole proporzionali. È un mini-cifrario a sostituzione (come quello delle lettere dell'alfabeto, ma su 4 bit). Da solo fa ridere, ma è la scintilla che impedisce all'hacker di usare l'algebra lineare per decifrare il sistema. Le 16 S-Box lavorano in parallelo, ognuna sul suo pezzettino.
    

#### 2. Il Livello di Diffusione (P-LAYER) - _Il Frullatore_

I 64 bit escono dalle S-Box rimescolati a gruppi di 4. Se ci fermassimo qui, un hacker potrebbe attaccare i singoli mattoncini separatamente.

Qui entra in gioco la **Permutazione Lineare (P-Box)**.

- **Cosa fa:** Prende i 64 bit e ne cambia la posizione fisica scambiando i fili di rame, seguendo una tabella precisissima (il bit 1 va in posizione 16, il bit 2 in posizione 32, il bit 3 in posizione 48, ecc.).
    
- **Perché è fondamentale:** Guarda il diagramma con i fili azzurri (l'immagine con scritto "at round i" e "at round i+1"). Questa è la magia della **Diffusione (Effetto Valanga)**. I 4 bit che escono da una singola S-Box vengono letteralmente _sparati_ verso 4 S-Box diverse nel round successivo.
    
    Se cambi anche **un solo bit** nel testo in chiaro:
    
    - Round 1: 1 S-box produce un output diverso.
        
    - Round 2: Quei 4 bit finiscono in 4 S-box diverse $\implies$ 4 S-box cambiano output.
        
    - Round 3: Quei 16 bit finiscono in 16 S-box diverse $\implies$ **Tutto il blocco da 64 bit esplode in modo caotico.** L'hacker perde completamente il controllo.
        

#### 3. Il Livello di Aggiunta della Chiave (Key-Addition Layer) - _Il Lucchetto_

Dopo il P-Layer, i 64 bit subiscono uno XOR ($\oplus$) con la Sotto-Chiave di quel round ($k_i$).

- **Cosa fa:** Inverte i bit in base a dove la chiave ha degli '1'. Agisce esattamente come un One-Time Pad.
    
- **Perché è fondamentale:** Senza questo livello, il sistema sarebbe pubblico. Lo XOR è ciò che lega il destino dei dati al tuo segreto personale.
    

---

### Fase 2: Il Flusso dell'Algoritmo (Il Loop di 31 Round)

Il prof ci mostra lo pseudocodice completo. L'assemblaggio è meticoloso:

1. **Whitening Step:** $m \leftarrow m \oplus k_0$. Prima ancora di iniziare, facciamo uno XOR con la primissima sotto-chiave. Questo "sporca" il testo in chiaro da subito, impedendo all'hacker di manipolare l'input della prima S-Box.
    
2. **Il Loop principale (i da 1 a 31):**
    
    - $m \leftarrow S(m)$ (Sostituzione)
        
    - $m \leftarrow P(m)$ (Diffusione)
        
    - $m \leftarrow m \oplus k_i$ (Aggiunta chiave)
        
3. **Ritorno:** Dopo 31 giri completi, il risultato è il file cifrato.
    

Il prof ci tiene a ribadire: S-Box da sola = debole (Cifrario a Sostituzione). Key-add da sola = debole (One-Time Pad mal usato). La combinazione iterata = Indistruttibile.

---

### Fase 3: Il Key Schedule (Come Mungere la Chiave)

Da dove escono fuori quelle 32 chiavi da 64 bit, se noi abbiamo solo una master key da 80 bit?

Vengono generate al volo da un registro a scorrimento, riga per riga.

Il registro K contiene gli 80 bit: $K = k_{79} k_{78} \dots k_1 k_0$.

In ogni round $i$, succedono queste operazioni chirurgiche:

1. **Estrazione:** La chiave del round $k_i$ è semplicemente costituita dai 64 bit più a sinistra del registro ($k_{79} \dots k_{16}$).
    
2. **L'Aggiornamento per il prossimo round:**
    
    - **Shift Circolare (Rotazione di 61 bit):** L'intero registro viene "ruotato" a sinistra di 61 posizioni. I bit escono da sinistra e rientrano a destra. Ora il registro è tutto scombinato.
        
    - **Passaggio in S-Box:** I 4 bit finiti più a sinistra (in cima) vengono fatti passare attraverso una singola S-Box. Questo inietta non-linearità anche nella chiave, non solo nel messaggio!
        
    - **Round Counter (Il tocco di classe):** Prende i bit in posizione 15, 16, 17, 18, 19 e fa uno XOR con il **numero del round attuale ($i$)**, che usa 5 bit.
        
        - _Perché è geniale?_ Se non ci fosse questo contatore, e tu avessi una chiave fatta tutta di zeri, ogni round userebbe la stessa identica sotto-chiave. Un hacker potrebbe usare un attacco chiamato "Slide Attack". Inserendo il numero del round dentro la chiave, ci assicuriamo matematicamente che il Round 1 sia crittograficamente un universo diverso dal Round 2.
            

### Decifratura?

Una riga per chiudere: siccome la S-box è invertibile ($S^{-1}$) e la permutazione dei fili è banale da invertire ($P^{-1}$), decifrare significa semplicemente applicare le chiavi al contrario, smontando il pacchetto a ritroso.

Per farti vedere in tempo reale la vera potenza distruttiva della Rete a Sostituzione-Permutazione, ti ho costruito un simulatore visivo dell'**Effetto Valanga (Avalanche Effect)**. Guarda come un singolo bit ribaltato all'ingresso infetti l'intera rete!

