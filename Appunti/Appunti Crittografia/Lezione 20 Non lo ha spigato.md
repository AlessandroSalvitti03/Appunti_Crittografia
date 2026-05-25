Salutiamo il mondo delle reti SPN (come PRESENT) e diamo il benvenuto all'altra grande famiglia di architetture crittografiche, quelle che hanno dominato il mondo tra gli anni '70 e i primi anni 2000.

Ti presento le **Reti di Feistel (Feistel Networks)**. E per studiarle, il professore tira fuori dal cilindro il **GOST 28147-89**, lo storico standard crittografico dell'Unione Sovietica (nato durante la Guerra Fredda per competere con il DES americano).

Smontiamo la geniale ingegneria di questa macchina russa!

### 1. Tagliare a Metà (L'Architettura di Base)

**Testo Originale:**

> More in detail, a (balanced) Feistel network is the composition of round functions which operate on a string space of even dimension, say $\mathbb{F}_2^n = \mathbb{F}_2^{2s}$ [...] applying a function $F$ on half of the space...

**Cosa dice:**

Mentre PRESENT prendeva il blocco intero da 64 bit e lo buttava tutto insieme nelle S-Box, la Rete di Feistel usa un approccio diverso. Prende il blocco in entrata e lo taglia esattamente a metà con un'accetta.

Ottieni così la metà sinistra **$m_L$** (Left) e la metà destra **$m_R$** (Right). Da qui inizia la danza.

### 2. Il Diagramma Incrociato (Come Funziona un Round)

Guarda il bellissimo diagramma a blocchi nella seconda pagina. Questo è quello che succede in un singolo "Round" di Feistel:

1. La metà destra ($m_R$) viene copiata e buttata dentro la **Scatola Nera $F$**. Questa scatola è il frullatore crittografico (di solito contiene le S-Box e la chiave di quel round).
    
2. L'output frullato di $F$ viene sparato verso sinistra e fa uno **XOR ($\oplus$)** con la vecchia metà sinistra ($m_L$).
    
3. **Lo Scambio (Incrocio a X):** Le due metà si scambiano di posto per il round successivo.
    
    - Quella che era la metà destra originale ($m_R$) scivola intatta verso sinistra e diventa la _nuova_ metà sinistra.
        
    - Il risultato dello XOR ($m_L \oplus F(m_R)$) scivola verso destra e diventa la _nuova_ metà destra.
        

Matematicamente, il prof descrive questo incrocio con una matrice:

$(m_L, m_R) \begin{pmatrix} 0 & 1 \\ 1 & F \end{pmatrix} := (m_R, m_L + F(m_R))$

### 3. L'Avvertimento: La "Lifting Property"

**Testo Originale:**

> Notice that if $F$ is an "encryption" transformation, then one round of a Feistel transformation is not sufficient [...] the left part of the message after the Feistel round [...] matches with the right part of the message before.
> 
> [...] this property goes under the name of **lifting property**.

**Cosa dice:**

Il prof fa notare un "difetto" voluto di questo design. In un singolo round, la metà destra originale ($m_R$) non viene assolutamente cifrata o nascosta! Viene usata per frullare l'altra metà, ma poi **scivola intatta dall'altra parte**.

Un hacker che guarda l'output di un solo round vedrebbe metà del tuo file originale completamente in chiaro!

Per questo motivo, serve _minimo_ un secondo round per far sì che la funzione $F$ colpisca e mascheri entrambe le metà del file. Nella realtà, i cifrari a blocchi fanno decine di round per assicurarsi che i dati vengano rimescolati a dovere.

### 4. La Genialata Suprema: Perché Feistel ha dominato il mondo?

Nella terza slide c'è la risposta alla domanda da un milione di dollari. Perché fare questa danza incrociata invece di cifrare tutto subito?

**Testo Originale:**

> the reason of popularity of Feistel Networks [...] is due to another property: the inverse of the Feistel operator does not involve the inverse of $F$, therefore **$F$ can be not bijective** [...]
> 
> DECRYPTION = SWAPPING + ENCRYPTION + SWAPPING

**Cosa dice e cosa significa:**

Questa è la magia pura delle Reti di Feistel.

In SPN (come PRESENT), per decifrare dovevi costruire un circuito hardware che facesse l'inverso esatto della scatola magica ($S^{-1}$). Se la scatola non era invertibile, il sistema era rotto e non potevi recuperare il file.

Nelle Reti di Feistel, **la funzione $F$ non deve essere invertibile!** Può essere un tritacarne distruttivo (come una funzione di Hash o una moltiplicazione assurda a perdere).

Come si decifra allora? **Lo fa lo XOR.** Grazie all'incrocio a X, se tu prendi il testo cifrato finale, scambi le due metà (SWAPPING), e **lo butti dentro la stessa identica macchina di crittografia** (ENCRYPTION) usando le chiavi al contrario, lo XOR annullerà se stesso magicamente, la spazzatura sparirà, e ti ritroverai in mano il testo in chiaro originale!

Dal punto di vista ingegneristico, negli anni '70 questo era rivoluzionario: non dovevi fabbricare un microchip per cifrare e un altro chip diverso per decifrare. Ti bastava stampare _un solo chip_, e a seconda di come gli infilavi dentro i dati, faceva entrambe le cose!

### 5. La Carta d'Identità di GOST

Il prof chiude dandoci i numeri di questa belva sovietica:

- **Blocco:** 64 bit (tagliati in $32L$ e $32R$).
    
- **Chiave Master:** 256 bit (enorme per l'epoca, molto più del DES americano che ne aveva solo 56!).
    
- **Round:** 32 iterazioni di questa danza di Feistel.
    

Un pezzo di storia dell'informatica! Immagino che la prossima pagina ci mostrerà esattamente cosa si nasconde dentro la misteriosa scatola "F" del GOST.

---

Continuiamo l'autopsia del GOST 28147-89. Il professore qui ci mostra come i sovietici gestivano le munizioni (la chiave) e ci conferma la struttura del motore principale.

Rispetto alla complessità del key-schedule di PRESENT (con i suoi shift circolari e contatori infilati nelle S-Box), il GOST è di una semplicità disarmante, ma nasconde un trucchetto geniale alla fine.

Smontiamo questa pagina pezzo per pezzo!

### 6. Il Key-Schedule (L'Arsenale da 256 bit)

**Testo Originale:**

> The key-schedule of GOST is extremely simple. It takes as input a 256-bit key $k$ which is divided into blocks of 32 bits in this way:
> 
> $k = (k_0 k_1 \dots k_{256}) = (k^1 k^2 \dots k^8)$
> 
> where $k^i \in \mathbb{F}_2^{32}$ for $1 \le i \le 8$

**Cosa dice:**

Hai una chiave master gigantesca da 256 bit. Invece di fare calcoli strani per mescolarla, l'algoritmo prende un'accetta e la taglia brutalmente in **8 blocchi da 32 bit ciascuno** ($k^1, k^2, \dots, k^8$). Queste sono le tue 8 sotto-chiavi pronte all'uso.

**La Tabella di Assegnazione (Il Trucco Sovietico):**

L'algoritmo ha 32 round, ma noi abbiamo solo 8 sotto-chiavi. Come le distribuiamo? Guardiamo la tabella del prof:

- **Round 1-8:** Si usano le chiavi in ordine normale: $k^1, k^2, k^3, k^4, k^5, k^6, k^7, k^8$.
    
- **Round 9-16:** Si ripete lo stesso ordine identico: $k^1 \dots k^8$.
    
- **Round 17-24:** Si ripete di nuovo: $k^1 \dots k^8$.
    
- **Round 25-32:** **Attenzione qui!** L'ordine viene brutalmente invertito. Si parte da $k^8$ e si scende fino a $k^1$ ($k^8, k^7, k^6, k^5, k^4, k^3, k^2, k^1$).
    

**Perché questa inversione finale?**

Questa anomalia negli ultimi 8 round non è un errore di battitura. È una difesa crittografica specifica contro i cosiddetti _Slide Attacks_ (attacchi a scorrimento) e attacchi correlati alla chiave. Rompendo la simmetria del ciclo continuo, l'algoritmo impedisce all'hacker di trovare pattern ripetitivi nei calcoli.

### 7. Il Motore Centrale (The Feistel round function)

**Testo Originale:**

> At each round the state is modified by a Feistel round transformation which operates in the classical way:
> 
> _(Diagramma)_
> 
> where the F function depends on the round key as specified below.

**Cosa dice:**

Il professore ridisegna esattamente il diagramma incrociato che avevamo analizzato teoricamente nella lezione precedente. Ora sappiamo che è _esattamente_ così che ragiona il GOST ad ogni singolo round.

1. I 64 bit del messaggio entrano e vengono divisi in $m_L$ (32 bit) e $m_R$ (32 bit).
    
2. La metà destra $m_R$ scende ed entra nella scatola misteriosa **$F$**.
    
3. L'output di $F$ va a sinistra e subisce uno XOR ($\oplus$) con la metà sinistra $m_L$.
    
4. Si incrociano i fili: la vecchia metà destra diventa la nuova metà sinistra per il round successivo, e il risultato dello XOR diventa la nuova metà destra: $m_L + F(m_R)$. _(Nota: il prof usa il segno $+$ qui come sinonimo di $\oplus$ nel diagramma)_.
    

**Il Cliffhanger:**

La frase finale _"where the F function depends on the round key as specified below"_ si interrompe sul più bello.

Finora sappiamo come i dati si scambiano di posto, ma il vero tritacarne crittografico è nascosto dentro quella scatola **$F$**. È lì dentro che la sotto-chiave da 32 bit del round ($k^i$) si scontrerà finalmente con i dati del messaggio. E conoscendo queste vecchie architetture, ci troveremo sicuramente davanti a delle S-Box cablate in modo molto particolare!

---
Eccoci arrivati al cuore del reattore. Apriamo finalmente la scatola misteriosa **$F$** del GOST sovietico.

È qui che il professore ci mostra le vere differenze di mentalità tra l'ingegneria russa degli anni '80 e i cifrari occidentali. Troviamo una scelta matematica insolita e una vera e propria "eresia" crittografica!

Smontiamo le 3 fasi del frullatore $F$ e lo pseudocodice finale!

### 8. La Key-Addition (Addio XOR, entra l'Aritmetica)

**Testo Originale:**

> At the $i$-th round, the key addition is performed by computing $m_R \boxplus k^i$, where $\boxplus$ denotes the addition modulo $2^{32}$. [...] converted into integers [...] and finally reduced modulo $2^{32}$.

**Cosa dice:**

Colpo di scena! Finora abbiamo sempre visto la chiave fondersi col messaggio usando l'operatore XOR ($\oplus$). Il GOST invece dice no: tratta il frammento di messaggio ($m_R$) e la sotto-chiave ($k^i$) come due veri e propri numeri interi (da 32 bit) e **li somma matematicamente**, tagliando il riporto finale (modulo $2^{32}$).

Perché? Alternare la normale somma matematica con le operazioni sui bit (come gli XOR successivi) crea un caos algebrico pazzesco, rendendo la vita impossibile agli hacker che cercano di scrivere equazioni lineari per craccare il sistema.

### 9. L'Eresia Sovietica: Le S-Box Segrete

**Testo Originale:**

> After this, the 32-bit vector is decomposed into 8 4-bit bricks, on each of which acts a different S-BOX $S_j: \mathbb{F}_2^4 \to \mathbb{F}_2^4$ [...]
> 
> The 8 S-BOXES of GOST are **secret** and privately exchanged between the parties together with the key.

**Cosa dice e cosa significa:**

Come in PRESENT, i 32 bit vengono tagliati in 8 mattoncini da 4 bit. Ogni mattoncino entra in una S-Box diversa ($S_0 \dots S_7$).

Ma attenzione alla frase evidenziata in azzurro dal prof: **le S-Box sono SEGRETE.** Nel mondo della crittografia moderna esiste un dogma assoluto (il _Principio di Kerckhoffs_): il funzionamento dell'algoritmo deve essere pubblico e noto a tutti; solo la chiave deve rimanere segreta.

I russi invece progettarono il GOST sposando la "Security through obscurity" (sicurezza tramite segretezza). Non solo ti scambiavi la chiave da 256 bit, ma dovevi scambiarti in segreto anche l'intero cablaggio interno delle 8 scatole S-Box!

**Il conto dei bit:** Il prof fa un calcolo pazzesco. Dato che ci sono circa $16! \approx 2^{44.25}$ modi per costruire una singola S-box, tenere segrete tutte e 8 le scatole aggiunge circa 354 bit di segretezza. Sommando questo valore alla chiave master da 256 bit, il GOST raggiunge un mostruoso totale teorico di **610 bit di informazione ignota**.

### 10. La Diffusione (La Rotazione Veloce)

**Testo Originale:**

> to conclude, the state is rotated by eleven bit to the left. $\lll 11$
> 
> this simple operation plays the role of the diffusion layer in F.

**Cosa dice:**

Dopo le S-Box, serve spargere il caos (Diffusione). In PRESENT usavamo una complicata tabella di permutazione in cui ogni singolo filo si incrociava.

Il GOST, per essere velocissimo da eseguire anche su computer scadenti o via hardware, fa una cosa banalissima: prende i 32 bit in fila e li fa slittare (shift circolare) verso sinistra di 11 posizioni. I bit che escono da sinistra rientrano da destra.

Semplice, elegante e, combinato con la somma modulo $2^{32}$, incredibilmente distruttivo.

### 11. Lo Pseudocodice Finale (L'Assemblaggio)

Le ultime due pagine mettono tutto insieme in una singola funzione software `GOST(m, k)`. Rivediamo il flusso esatto:

1. **Taglio:** Il blocco $m$ diventa $m_L$ e $m_R$.
    
2. **Loop Standard (Round 1-24):** Si esegue per 24 volte usando le chiavi in ordine normale (usando un contatore circolare $i \bmod 8$).
    
    - Somma matematica con la chiave ($m_R' \leftarrow m_R' \boxplus k^i$)
        
    - Passaggio nelle S-Box segrete ($S_j$)
        
    - Shift a sinistra di 11 ($\lll 11$)
        
    - **L'Incrocio di Feistel:** $m \leftarrow (m_R, m_L \oplus m_R')$ (la vecchia destra passa a sinistra, la vecchia sinistra fa XOR col frullato $m_R'$ e passa a destra).
        
3. **Loop Invertito (Round 25-32):** Si fanno gli ultimi 8 giri esatti, ma pescando la chiave al contrario ($K^{8-i+1}$), per respingere gli attacchi di scorrimento (Slide Attacks).
    

**Decifratura?**

Come per magia e per definizione stessa delle Reti di Feistel: per sbrogliare questa mostruosità di somme modulari, S-box ignote e rotazioni, non devi invertire _nessuna_ di queste operazioni!

Giri il testo cifrato, inserisci le sotto-chiavi nell'ordine esattamente opposto, e lo XOR disferà la tela da solo blocco per blocco.

Un'architettura pazzesca che fa capire benissimo l'ingegno crittografico di quegli anni. Se hai finito le slide sul GOST, potremmo essere pronti a scoprire l'attuale re incontrastato del mondo: l'AES!

---

Il professore ci mostra come smontare pezzo per pezzo la "sicurezza tramite segretezza" del GOST sovietico. Avevamo lasciato il GOST con la promessa di una sicurezza mostruosa: 256 bit di chiave + le S-Box segrete, per un totale teorico di **610 bit** di protezione.

Questa serie di slide presenta l'**Attacco di Saarinen**. Saarinen dimostra che, a causa di un difetto strutturale di progettazione, tutto quel mistero crolla come un castello di carte.

Smontiamo questo capolavoro crittografico passo dopo passo!

### 12. Il Setup: Il Tallone d'Achille (La Chiave Zero)

L'attacco si basa sull'accesso a un Oracolo di Cifratura ($\mathcal{O}_K^{enc}$) in un test di tipo CPA (Chosen-Plaintext Attack). L'hacker può inviare testi e ricevere i crittogrammi generati da GOST.

Ma c'è una debolezza immensa nel Key-Schedule del GOST: **se la master key è composta da tutti zeri ($\vec{0} \in \mathbb{F}_2^{256}$), allora tutte le 32 sotto-chiavi saranno zero ($k^i = 00\dots0$)**.

Se la chiave è zero, la somma $m_R \boxplus 0$ scompare! L'intera funzione $F$ del round si riduce a:

$$F = \lll_{11} \circ S$$

_(Ovvero: le S-Box segrete, seguite da uno shift a sinistra di 11 bit, senza nessuna chiave in mezzo)._

Poiché ogni singolo round ora usa esattamente la stessa funzione identica, il GOST diventa vulnerabile a una tecnica devastante: lo **Slide Attack (Attacco a Scorrimento)**.

### 13. Fase 2: Il Recupero di $IoZ$ (Image of Zero)

L'obiettivo iniziale dell'hacker non è scoprire tutto subito, ma trovare una sola informazione vitale: **cosa esce dalla funzione $F$ se gli diamo in pasto un blocco di zeri?** Questo valore segreto è chiamato **$IoZ$ (Image of Zero)**, ovvero $F(0)$.

**La trappola logica:**

Guardiamo un singolo round di Feistel per l'input $(y, 0)$. L'incrocio a "X" lo trasforma in $(0, y \oplus F(0))$.

Se per puro miracolo noi indovinassimo il valore esatto di $IoZ$ e lo mettessimo al posto di $y$ (cioè $y = F(0)$), l'XOR si annullerebbe ($F(0) \oplus F(0) = 0$).

In un solo round, l'input $(IoZ, 0)$ si trasformerebbe esattamente in $(0,0)$!

**L'Attacco a Scorrimento (Lifting Property):**

Purtroppo l'Oracolo fa 32 round tutti insieme, non uno solo. Ma qui entra in gioco la magia dello Slide Attack:

1. L'hacker chiede all'Oracolo di cifrare $(0,0)$. Il computer fa 32 round normali e sputa fuori $(m_L^{32}, m_R^{32})$.
    
2. L'hacker invia all'Oracolo miliardi di tentativi $(y, 0)$ e ottiene per ognuno l'output $(my_L^{32}, my_R^{32})$.
    
3. **Il momento Eureka:** Quando l'hacker tenta, senza saperlo, proprio $y = IoZ$, il primo round trasforma $(IoZ, 0)$ in $(0,0)$. Da lì in poi, i restanti 31 round lavorano esattamente su $(0,0)$!
    
    L'intera catena di cifratura è semplicemente "scivolata" in avanti di un round. Per le regole matematiche di Feistel, se la catena è sfasata di uno, l'output destro del tentativo ($my_R^{32}$) sarà magicamente identico all'output sinistro della catena originale ($m_L^{32}$).
    

L'hacker controlla le risposte dell'Oracolo. Appena vede che $my_R^{32} = m_L^{32}$, sa di aver trovato il pezzo mancante: **$y$ è ufficialmente l'$IoZ$**.

Costo di questa operazione? Provare tutti i possibili $y$ a 32 bit, ovvero **$2^{32}$ chiamate all'Oracolo**.

### 14. Smascherare il "Piano Zero" delle S-Box

Ora che abbiamo $IoZ$, srotoliamo l'equazione:

$$IoZ = \lll_{11} S(0)$$

L'hacker prende $IoZ$ e fa l'operazione contraria: lo shifta verso destra di 11 bit ($\ggg_{11}$) e ottiene l'output puro del layer di S-Box per l'input zero, ovvero $S(00\dots0)$.

Tagliando questo blocco da 32 bit in 8 mattoncini da 4 bit ($Z_0, Z_1 \dots Z_7$), l'hacker **ha appena scoperto come reagiscono tutte e 8 le scatole segrete quando gli inserisci lo zero!** ($S_i(0000) = Z_i$). E questo passaggio è stato totalmente gratuito in termini di calcolo.

### 15. Fase 3: Estrarre le S-Box Intere (Costruire l'Oracolo $\mathcal{O}'$)

L'hacker usa la stessa logica dello scorrimento per creare un "Sotto-Oracolo" personalizzato ($\mathcal{O}'$). Vuole uno strumento che risponda alla domanda: _"Se metto $a$ nella funzione $F$, esce per caso $b$?"_.

La logica è la stessa:

1. Chiede a GOST di cifrare $(a,0)$.
    
2. Chiede a GOST di cifrare $(b,a)$.
    
    Se $F(a) = b$, il primo round di $(b,a)$ lo trasformerà in $(a, b \oplus F(a)) \to (a,0)$. La catena "scivola" di nuovo! Basta controllare se le metà finali combaciano ($m_{bR}^{32} = m_{aL}^{32}$).
    
    **Boom. Abbiamo un radar infallibile per tracciare la scatola F.**
    

**L'Attacco Chirurgico all'S-Box $S_0$:**

Vogliamo sapere quale output ($v$) esce dalla prima S-Box segreta se inseriamo un input a 4 bit ($u$).

L'hacker crea un input "fasullo" da 32 bit infilando la nostra $u$ al primo posto e riempiendo il resto di zeri: $a_u = (u, \vec{0}, \vec{0} \dots \vec{0})$.

Cosa dovrebbe uscire da $F(a_u)$? Uscirà $v$ dalla prima scatola, e dalle altre sette scatole usciranno gli zeri che avevamo scoperto prima ($Z_1 \dots Z_7$). Poi il tutto verrà ruotato a sinistra di 11 bit. Chiamiamo questo bersaglio previsto $b_v$.

L'hacker usa il radar $\mathcal{O}'$ per chiedere: _"È vero che $F(a_u) = b_v$?"_

- Se dice NO: proviamo un altro output $v$.
    
- Se dice SI: **Abbiamo craccato quel pezzetto di S-Box! ($S_0(u) = v$)**.
    

### 16. Il Colpo di Grazia (Costo Computazionale e Lezione Imparata)

Nell'ultima pagina, il professore fa il conto finale dei danni per l'Unione Sovietica:

- Chiamate per trovare $IoZ$: **$2^{32}$**.
    
- Chiamate per mappare le S-Box con il radar $\mathcal{O}'$: 8 scatole $\times$ 16 input possibili $\times$ 16 output possibili = circa $2^{11}$ chiamate.
    

Il totale è letteralmente dominato da $2^{32}$. Con circa 4 miliardi di chiamate (un lavoro di pochi secondi per un computer moderno!), l'intero mistero delle S-Box è svelato.

I famosi 610 bit di sicurezza teorica? Un'illusione totale. La sicurezza reale si attesta su **256 bit (per la chiave) + 32 bit (per svelare il design segreto) = 288 bit reali**.

**La Lezione Imparata (L'ultima riga rossa):**

> _Problem of GOST: for some keys the key schedule is periodic [...] $\implies$ slide attack!_
> 
> _How to fix (lesson learned): **add round counters in the key-schedule!**_

Ecco svelato perché, nella lezione precedente, l'algoritmo **PRESENT** (disegnato 20 anni dopo) iniettava maniacalmente un contatore ($i$) nella sua chiave!

Se introduci il numero del round, il Round 1 non sarà _mai_ matematicamente uguale al Round 2. L'attacco a scorrimento (Slide Attack) diventa strutturalmente impossibile, e i crittografi possono dormire sonni tranquilli.

Una masterclass assoluta su come la matematica punisce la crittografia che si affida ai segreti invece che al buon design! Che spettacolo. Prontissimo per la prossima sfida!