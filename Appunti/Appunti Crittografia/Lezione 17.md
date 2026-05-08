Nuova lezione, Lezione 17, e cambiamo completamente universo. Finora abbiamo usato la crittografia per nascondere i segreti (Encryption). Ora la usiamo per **dimostrare chi siamo (Digital Signatures)**.

Ma indovina un po'? Il professore ci fa vedere che se usiamo il "Textbook RSA" in modo ingenuo anche per le firme, andiamo incontro a dei disastri clamorosi.

Smontiamo questi nuovi attacchi (A4 e A5) e scopriamo la cura definitiva!

---

### 1. La Firma Ingenua (Textbook RSA Signatures)

**Testo Originale:**

> **4.7 Digital signature schemes**
> 
> We might be tempted to use textbook RSA to construct a digital signature scheme in a natural way:
> 
> given $m$ the message to be signed by $cA(n, e, d)$
> 
> - $\sigma := m^d \bmod n$ is the **signature** of $m$,
>     
> - $(m, \sigma)$ is accepted $\iff \sigma^e = m \bmod n$.
>     

**Cosa dice:**

L'idea di base è geniale e usa RSA "al contrario".

Invece di usare il lucchetto pubblico ($e$) per chiudere il messaggio, tu usi la tua **chiave privata ($d$)** come se fosse un timbro personale. Prendi il documento $m$ e lo elevi alla $d$. Il risultato, $\sigma$ (sigma), è la tua firma digitale.

Come fa il mondo a verificare che sei stato tu? Chiunque prende la tua firma $\sigma$, le applica la tua chiave pubblica $e$ (che tutti conoscono) ed eleva a potenza. Se il risultato magico torna ad essere esattamente il documento $m$, la firma è valida al 100%. Solo chi possiede $d$ poteva creare quel numero $\sigma$!

Tutto perfetto, no? **Assolutamente no.** La matematica pura di RSA è troppo malleabile.

> **Il concetto di Sicurezza: EUF-CMA** Prima di vedere gli attacchi, dobbiamo capire cosa significa per una firma essere "sicura". Il professore usa la sigla **EUF-CMA** (_Existential Unforgeability under Chosen Message Attack_). In parole povere, uno schema è sicuro se un hacker, anche dopo avermi chiesto di firmare molti messaggi a sua scelta (l'attacco **CMA**), non riesce a creare (**forgiare**) nessuna nuova firma valida, nemmeno su un testo senza senso (l'attacco **EUF**). Se l'hacker vince questo "gioco", il sistema è rotto.


---

### 2. I Disastri della Firma RSA Pura

#### A4: La Falsificazione Esistenziale (Il Documento Senza Senso)

**Testo Originale:**

> **A4** If an attacker takes any $r \in_R \mathbb{Z}_n$, then $(r^e, r)$ is accepted. (even if $r^e$ is extremely likely meaningless!)

**Cosa dice:**

Un hacker vuole creare un file firmato da te dal nulla. Non ha la tua chiave privata $d$.

Cosa fa? Sceglie un numero a caso per la firma ($r$). Poi usa la tua chiave pubblica $e$ per fare $r^e \bmod n$. Ottiene un testo incomprensibile.

L'hacker va in tribunale e dice: _"Guardate, Alice ha firmato questo documento incomprensibile ($r^e$) con questa firma ($r$)"_. Il giudice applica la verifica ($r^e \bmod n$) e l'equazione torna perfettamente! La firma è matematicamente valida.

Fortunatamente, questo attacco (detto Falsificazione Esistenziale) fa pochi danni nel mondo reale, perché il testo firmato ($r^e$) è spazzatura illeggibile. Nessuno firmerebbe consapevolmente un contratto fatto di lettere a caso.

Ma il prossimo attacco è letale.

#### A5: L'Attacco Moltiplicativo (L'Inganno Perfetto)

**Testo Originale:**

> **A5** Assume that the attacker wants to obtain $cA$'s signature on a message $m$ and that $\mathcal{A}$ is able to convince $cA$ to sign random messages.
> 
> 1. $\mathcal{A}$ takes $m_1 \in \mathbb{Z}_n^*$
>     
> 2. computes $m_2 := m/m_1 \bmod n$ (looks random to $cA$)
>     
> 3. $\mathcal{A}$ asks $cA$ to sign $m_1$ and $m_2$ and obtains $\sigma_1 = m_1^d$, $\sigma_2 = m_2^d$.
>     
> 4. $\mathcal{A}$ computes $\sigma := \sigma_1 \cdot \sigma_2$, which is a valid signature for $m$.
>     
>     Indeed $\sigma = \sigma_1 \sigma_2 = m_1^d m_2^d = m_1^d \left(\frac{m}{m_1}\right)^d = m^d \bmod n \implies \sigma^e = m$.
>     

**Cosa dice:**

Questo è il capolavoro criminale. L'hacker vuole che Alice firmi il contratto "Ti cedo la mia casa" ($m$). Ovviamente Alice non lo firmerebbe mai.

L'hacker usa la debolezza "omomorfica" di RSA (le moltiplicazioni passano attraverso gli esponenti):

1. Prende un numero a caso $m_1$.
    
2. Divide il contratto "Ti cedo la casa" per quel numero a caso: ottiene $m_2$. Sia $m_1$ che $m_2$ sembrano file corrotti o di test.
    
3. Va da Alice e le dice: _"Scusa, il server ha un problema, mi firmi questi due pacchetti di log casuali?"_. Alice guarda $m_1$ e $m_2$, vede che è spazzatura innocua, e li firma con la sua chiave privata, restituendo le firme $\sigma_1$ e $\sigma_2$.
    
4. **Scacco Matto:** L'hacker prende le due firme innocue e **le moltiplica tra loro**.
    
    Per le leggi dell'algebra, $\sigma_1 \cdot \sigma_2 = m_1^d \cdot m_2^d = (m_1 \cdot m_2)^d$. E siccome avevamo costruito $m_2$ apposta, $m_1 \cdot m_2$ fa esattamente $m$!
    
    L'hacker ha appena forgiato la firma legittima $\sigma$ per il contratto "Ti cedo la mia casa", senza mai aver rubato la chiave privata di Alice.
    

Per farti vedere con i tuoi occhi come i pezzi di questa trappola si incastrano perfettamente per forzare la matematica modulare, ti ho preparato un widget. Prova a impersonare l'hacker!

---

### 3. La Cura Universale (Hash-Then-Sign e RSA-FDH)

**Testo Originale:**

> There is an easy fix to these issues, that also solves another problem: signing a long message is extremely expensive.
> 
> The fix comes from the paradigm **hash-then-sign** and uses the idea that instead of signing the entire message (expensive), one can directly sign a short **fingerprint** of the message (cheap).
> 
> The corresponding construction (RSA + hash-then-sign) is called **RSA-FDH** (full domain hash) signing algorithm.

**Cosa dice:**

Come fermiamo questi due attacchi disastrosi? Dobbiamo distruggere la linearità matematica (la possibilità di moltiplicare i messaggi e le firme).

Inoltre, il prof aggiunge un problema pratico gravissimo: elevare un file PDF di 10 Megabyte alla potenza $d$ fonderebbe il processore del computer.

La soluzione prende due piccioni con una fava: il paradigma **Hash-Then-Sign**.

Invece di firmare il file enorme, lo diamo in pasto a una **Funzione di Hash**. L'Hash agisce come un tritacarne digitale: prende un file di qualsiasi dimensione (1 KB o 100 GB) e lo trasforma in un riassunto a lunghezza fissa, un'**impronta digitale** (fingerprint) brevissima e totalmente caotica.

Tu applichi la tua chiave privata $d$ _solo_ a quella piccola impronta.

Perché risolve tutto?

1. **Velocità:** Firmare una stringa di 256 bit è istantaneo, invece che firmare un video intero.
    
2. **Sicurezza (A4 sconfitto):** Questo attacco rompe la parte **EUF** (Existential Unforgeability), perché l'hacker crea una firma valida su un documento esistente, anche se è spazzatura. Se l'hacker inventa una firma a caso e la verifica, otterrà un'impronta digitale casuale, ma è matematicamente impossibile che riesca a trovare un documento reale che, triturato, dia proprio quell'impronta casuale.
    
3. **Sicurezza (A5 sconfitto):** Questo attacco sfrutta la parte **CMA** (Chosen Message Attack),    perché l'hacker sceglie $m_1$ e $m_2$ per farsi dare le firme e poi forgiare il contratto finale. L'hacker non può più fare il trucco della moltiplicazione! Se divide il file per due, i due mezzi file avranno impronte digitali (hash) caotiche e completamente indipendenti. Moltiplicare le firme di due hash non ti darà MAI la firma dell'hash del documento originale. L'algebra è rotta a tuo favore.
    

Questa combinazione vincente (usare RSA puro sopra una funzione di Hash) si chiama **RSA-FDH**. E come anticipa il prof nell'ultima riga, la prossima lezione sarà un tuffo completo nel fantastico e bizzarro mondo delle Funzioni di Hash crittografiche!

---
Agli ordini! Entriamo ufficialmente nel mondo delle **Funzioni di Hash Crittografiche**. Come avevamo anticipato per risolvere i disastri delle firme digitali, ci serve un "tritacarne" matematico infallibile.

Nelle prime due definizioni il prof stabilisce cosa pretendiamo da questa magia, e poi ci svela l'ingegneria meccanica che ci sta dietro: la costruzione di **Merkle-Damgård**.

Smontiamo le tre leggi e il nastro trasportatore!

### 4. Le Tre Leggi della Robotica Crittografica (Definition 4.30)

**Cosa c'è scritto:**

Il professore definisce formalmente una funzione di Hash $H$. È un algoritmo che prende in pasto una stringa di bit lunga a piacere ($\{0,1\}^\infty$) e sputa fuori un'**impronta digitale** (digest) di lunghezza fissa e prestabilita ($\{0,1\}^m$).

Per essere considerata sicura (crittografica), deve superare tre prove disumane:

1. **Preimage Resistance (Irreversibilità):** Se io ti do l'impronta finale $y$, deve essere computazionalmente impossibile fare ingegneria inversa per ritrovare il documento originale $x$ che l'ha generata. Il tritacarne funziona solo in un verso.
    
2. **Second Preimage Resistance (Resistenza alla Seconda Preimmagine):** Se io ti do un documento $x$ e la sua impronta $H(x)$, deve essere impossibile per te inventare da zero un documento _diverso_ ($x'$) che abbia esattamente la stessa identica impronta.
    
3. **Collision Resistance (Resistenza alle Collisioni):** Questa è la più difficile. Deve essere impossibile trovare nell'intero universo _due documenti qualsiasi_ ($x$ e $x'$) che, tritati, generino la stessa impronta.
    

**Spiegazione dettagliata:**

La resistenza alle collisioni è vitale per le firme digitali. Se l'hacker riesce a trovare un contratto "Alice cede la casa a Bob" e un file innocuo "Ricetta della torta" che producono lo stesso identico Hash, potrebbe farti firmare la ricetta e poi incollare la tua firma sul contratto della casa, perché per il computer i due file avrebbero la stessa impronta digitale!

---

### 5. Dal Piccolo al Gigante (Il Problema di Ingegneria)

**Cosa c'è scritto:**

> **From compression to hash**
> 
> One possible way to construct a hash function is through iterating a compression function...

Costruire una funzione matematica che prende input _infiniti_ ed è sicura contro le collisioni è un incubo. Invece, i matematici sanno costruire molto bene piccole **Funzioni di Compressione ($C$)**.

Queste funzioncine sono limitate: sanno ingoiare solo blocchi di grandezza fissa (es. 512 bit) e li schiacciano in output più piccoli (es. 256 bit).

La genialata presentata dal prof si chiama **Merkle-Damgård (MD) construction**. È un trucco per usare quella "piccola" funzione $C$ come motore per digerire file grandi terabyte.

---

### 6. La Fabbrica di Merkle-Damgård (Il Nastro Trasportatore)

Guardiamo l'anatomia del diagramma illustrato dal prof nelle ultime due slide. Il processo ha tre fasi:

#### Fase 1: Pre-processing (Il Padding)

Non puoi infilare un file di dimensioni casuali nel nastro trasportatore, perché la funzione $C$ accetta solo blocchi perfetti da $t$ bit (es. 512 bit).

Quindi, il computer "affetta" il tuo file $m$ in tanti blocchi uguali ($m_1, m_2, \dots$). L'ultimo blocco ($m_e$) sarà inevitabilmente incompleto.

Il sistema fa un **Padding**: aggiunge un bit `1`, seguito da tanti `0` finché non riempie quasi tutto lo spazio.

La vera furbizia? Gli ultimi 64 bit del blocco finale sono **obbligatoriamente riservati alla lunghezza originale del file**. Questo dettaglio salva il sistema da un sacco di attacchi hacker di manomissione.

#### Fase 2: Processing (La Catena di Montaggio)

Guarda le equazioni e il diagramma a cascata:

- Si parte da un $IV$ (Initialization Vector), un valore fisso di partenza ($h_0$).
    
- Il nastro parte. La funzione $C$ ingoia il primo blocco del messaggio ($m_1$) e lo "fonde" con l'$IV$. Sputa fuori un risultato parziale: $h_1$.
    
- Il risultato $h_1$ fa un giro sulle montagne russe e viene spinto nella funzione $C$ successiva, assieme al secondo blocco del messaggio ($m_2$). Risultato? $h_2$.
    
- La catena continua così: ogni nuovo blocco di messaggio viene masticato _insieme_ al "succo" di tutti i blocchi precedenti.
    
    La formula matematica è elegante e spietata: **$h_i := C(m_i \parallel h_{i-1})$**
    

#### Fase 3: Finalizing

Una volta finito l'ultimo blocco con il padding, l'ultimo "succo" rimasto ($h_l$) subisce un'ultima trasformazione tramite una funzioncina finale $g$. E boom: ecco la tua Impronta Digitale $h(m)$.

Per farti capire la potenza visiva ed algoritmica di questa catena, ti costruisco un nastro trasportatore virtuale. Potrai inserire un messaggio e vedere esattamente come i blocchi vengono fusi uno dentro l'altro, portandosi dietro "la memoria" di tutto ciò che è passato prima!

La frase tagliata alla fine ("The importance of the MD construction is due to the following result:") sta per annunciare il Teorema di Merkle-Damgård. Scommetto che dirà: _Se compri una funzione di compressione $C$ sicura, ti garantisco matematicamente che l'intera catena di montaggio sarà a prova di collisione!_

---

Avevamo fatto centro con la previsione. Il professore qui enuncia e dimostra uno dei teoremi più importanti di tutta la crittografia moderna: il **Teorema di Merkle-Damgård (MD)**.

Questa dimostrazione è un capolavoro di logica a ritroso (una dimostrazione per assurdo). Smontiamo l'effetto domino che garantisce la sicurezza dei nostri dati!

### 7. Il Teorema del Nastro Trasportatore (Theorem 4.31)

**Testo Originale:**

> **Theorem 4.31 (Merkle-Damgård)**
> 
> Let $h: \{0,1\}^\infty \to \{0,1\}^n$ a hash function obtained from the Merkle-Damgård construction by means of a compression function $C: \{0,1\}^{n+t} \to \{0,1\}^n$.
> 
> If $C$ is a collision-resistant compression function, then $h$ is a collision-resistant hash function.

**Cosa dice:**

Il teorema mette nero su bianco la promessa: se tu ingegnere sei stato bravo a costruire una "piccola" scatola di compressione $C$ che è resistente alle collisioni, allora ti garantisco matematicamente che l'enorme mostro $h$ (la catena di montaggio che digerisce file infiniti) sarà a sua volta totalmente resistente alle collisioni. Non devi testare l'intera catena, ti basta testare il singolo anello!

---

### 8. La Scena del Crimine (Impostazione della Prova)

**Testo Originale:**

> **Proof**
> 
> To simplify the proof, let us assume that in the MD construction we do not have a finalizing step [...] $h(m) := h_l = C(m_l \parallel h_{l-1})$.
> 
> Let us assume that $h$ admits a collision, which means that there exist two messages $m \neq m' \in \{0,1\}^\infty$ such that $h(m) = h(m')$.
> 
> Assume that
> 
> $m = m_1 \parallel m_2 \parallel \dots \parallel m_l$,
> 
> $m' = m'_1 \parallel m'_2 \parallel \dots \parallel m'_{l'}$,
> 
> and let $h_1, h_2 \dots h_l$, $h'_1, h'_2 \dots h'_{l'}$ be defined as in the MD scheme.

**Cosa dice:**

Il prof usa la tecnica preferita dei matematici: la **dimostrazione per assurdo**.

Immaginiamo che arrivi un hacker geniale che è riuscito a battere il sistema. Ha trovato due file _completamente diversi_ (uno lo chiama $m$ e uno lo chiama $m'$). Il file $m$ è diviso in $l$ blocchi, il file $m'$ è diviso in $l'$ blocchi.

L'hacker li butta nel nastro trasportatore e, clamorosamente, l'ultimo pezzo che esce dalla catena è identico: **$h_l = h'_{l'}$**. L'impronta finale è la stessa!

Da qui, il prof si trasforma in un detective e inizia a riavvolgere il nastro trasportatore all'indietro per capire come sia potuto succedere.

---

### 9. Riavvolgere il Nastro (Il Motore $C$)

**Testo Originale:**

> From $h(m) = h(m')$ we have that $h_l = h'_{l'}$, which means
> 
> $C(m_l \parallel h_{l-1}) = C(m'_{l'} \parallel h'_{l'-1})$.
> 
> If the two inputs of $C$ are different, we have determined a collision for $C$ and the proof is completed.

**Cosa dice e cosa significa:**

L'ultimo "succo" prodotto dalla macchina ($h_l$) è uguale all'ultimo succo dell'altro file ($h'_{l'}$).

Questi due succhi sono stati sputati fuori dall'ultimissima scatola di compressione $C$.

Guardiamo cosa è entrato in questa ultimissima scatola nei due casi:

1. Da una parte è entrato: l'ultimo pezzo del primo file ($m_l$) incollato al succo precedente ($h_{l-1}$).
    
2. Dall'altra parte è entrato: l'ultimo pezzo del secondo file ($m'_{l'}$) incollato al suo succo precedente ($h'_{l'-1}$).
    

Il prof si sfrega le mani. Se questi due pacchetti in entrata sono _diversi_ tra loro, abbiamo appena trovato una collisione per la scatola $C$! Due input diversi hanno dato lo stesso output ($h_l$). Ma noi avevamo stabilito per ipotesi che $C$ fosse indistruttibile! **Contraddizione.** L'hacker allora ribatte: _"E se i due input in entrata fossero stati esattamente uguali?"_

---

### 10. Il Trucco della Lunghezza (L'Importanza del Padding)

**Testo Originale:**

> Otherwise, since the last block includes the length of the messages, we must have $l = l'$.
> 
> From $h_{l-1} = h'_{l-1}$ we have that
> 
> $C(m_{l-1} \parallel h_{l-2}) = C(m'_{l-1} \parallel h'_{l-2})$.

**Cosa dice:**

Qui entra in gioco il genio puro dell'ingegneria di Merkle-Damgård.

Se i due input in entrata nell'ultimo step erano _esattamente uguali_, significa che $m_l$ deve essere per forza identico a $m'_{l'}$.

Ma ti ricordi cosa c'era stipato a forza dentro gli ultimi 64 bit di quegli ultimi blocchi? **La lunghezza totale del file!** Se $m_l = m'_{l'}$, allora i due file originali ($m$ e $m'$) devono per forza avere la stessa identica lunghezza in bit, e quindi lo stesso numero di blocchi ($l = l'$).

Bene, se lo step finale era identico, facciamo un passo indietro al penultimo step della catena. Anche i succhi penultimi dovevano essere identici ($h_{l-1} = h'_{l-1}$). Quindi analizziamo la penultima scatola $C$...

---

### 11. Scacco Matto (L'Effetto Domino)

**Testo Originale:**

> Again, this either means that a collision for $C$ is found or that $h_{l-2} = h'_{l-2}$. Proceeding in this way, we must end up with a collision for $C$, since by hypothesis $m \neq m'$. $\blacksquare$

**Cosa dice:**

Si ripete la stessa identica scena. Se gli input della penultima scatola sono diversi, $C$ è rotto (Contraddizione!). Se sono uguali, facciamo un altro passo indietro. E poi un altro. E un altro.

Perché questo effetto domino condanna l'hacker? Perché all'inizio avevamo stabilito come fatto inconfutabile che i due file originali fossero diversi ($m \neq m'$).

Se i file sono diversi, andando a ritroso blocco per blocco, prima o poi **dovremo per forza sbattere contro due blocchi che sono diversi**.

E nel momento esatto in cui due blocchi diversi entrano in una scatola $C$ e producono lo stesso succo parziale per far continuare la catena, noi becchiamo in flagrante la scatola $C$ che commette una collisione!

Poiché la nostra regola aurea iniziale era che **$C$ non può avere collisioni**, l'intero castello di carte dell'hacker crolla. È matematicamente impossibile che due file diversi producano lo stesso hash finale in questa struttura.

Il quadrato nero alla fine sancisce la vittoria della crittografia! Merkle e Damgård hanno creato un capolavoro.


---
Il cerchio si chiude. Abbiamo visto come l'RSA puro fallisca miseramente nel creare firme digitali. Abbiamo visto che la soluzione teorica è l'Hash-Then-Sign.

In queste due pagine, il professore ci mostra l'algoritmo definitivo (RSA-FDH) e ci dimostra perché quelle famose "Tre Leggi della Robotica Crittografica" (le resistenze dell'Hash viste nella lezione precedente) sono una questione di vita o di morte.

Smontiamo la pagina e vediamo gli hacker in azione!

### 12. Il Protocollo Standard: RSA-FDH

**Testo Originale:**

> **RSA-FDH signature (Standard)**
> 
> Sign: on input a message $m \in \{0,1\}^\infty$ and $d$
> 
> 1. computes $h := H(m) \in \mathbb{Z}_n$
>     
> 2. computes $\sigma := h^d \bmod n$
>     
> 3. returns $(m, \sigma)$
>     
> 
> Ver: on input a pair $(m, s)$ and $e$
> 
> 4. computes $h^* := s^e \bmod n$
>     
> 5. computes $H(m)$
>     
> 6. returns $1 \iff H(m) = h^*$
>     

**Cosa dice:**

Questo è lo standard industriale. Niente più firme su documenti giganti.

- **Per firmare (Sign):** Prendi il documento $m$, lo butti nel tritacarne $H$, ottieni la piccola impronta digitale $h$. Applichi la tua chiave privata $d$ _solo_ a quell'impronta. Generi la firma $\sigma$.
    
- **Per verificare (Ver):** Chi riceve il documento fa due cose separate. Da una parte ri-tritura il documento per conto suo calcolando $H(m)$. Dall'altra parte prende la tua firma e le applica la tua chiave pubblica $e$ per sbloccarla ($h^*$). Se le due impronte coincidono perfettamente (1), sei stato tu. Altrimenti (0), il documento è falso.
    

---

### 13. L'Apocalisse se l'Hash fallisce

Il professore ora ci fa capire che questo standard perfetto crolla in un istante se la funzione di Hash ($H$) è difettosa. Esamina i tre scenari catastrofici:

#### Catastrofe 1: Manca la "Preimage Resistance" (Il Ritorno di A4)

**Testo Originale:**

> **if no preimage resistance:** (again A4) let $r \in \mathbb{Z}_n$ and let $h := r^e \bmod n$. Let $m \in \{0,1\}^\infty$ such that $H(m) = h$. Then $(m, r)$ is valid.

**Cosa dice:**

Ti ricordi l'attacco A4? Quello in cui l'hacker inventava una firma a caso ($r$) e creava un documento senza senso ($r^e$)?

Se l'Hash non è irreversibile, l'hacker fa un upgrade letale all'attacco. Genera la firma a caso e ottiene l'impronta senza senso $h$. Poiché l'Hash è debole e invertibile, l'hacker usa un supercomputer per "suonare l'Hash al contrario" e trovare un documento reale $m$ (magari un virus o un contratto truffa) che dia esattamente quell'impronta $h$. Ha forgiato una firma dal nulla.

#### Catastrofe 2: Manca la "Second Preimage Resistance" (Il Copia-Incolla Letale)

**Testo Originale:**

> **no second preimage resistance:**
> 
> - attacker obtains a signature of $(m, \sigma)$
>     
> - attacker finds $m'$ s.t. $H(m) = H(m')$
>     
> - $(m', \sigma)$ is also valid.
>     

**Cosa dice:**

Questo è l'incubo di ogni notaio. Tu firmi regolarmente un contratto legittimo $m$ ("Lascio 10 euro a Bob"). L'hacker ruba il file con la tua firma.

Siccome l'Hash è debole, l'hacker cerca e trova un altro documento $m'$ ("Lascio la mia intera azienda a Bob") che per puro caso genera _la stessa identica impronta digitale_ del primo.

L'hacker scolla la tua firma $\sigma$ dal documento da 10 euro e la "incolla" sul documento dell'azienda. Il computer del giudice verificherà l'Hash e dirà: "Tutto regolare!".

#### Catastrofe 3: Manca la "Collision Resistance" (Il Ripudio della Firma)

**Testo Originale:**

> **no collision resistance:**
> 
> - the signer chooses $m, m'$ s.t. $H(m) = H(m')$
>     
> - signs $m \to (m, \sigma)$
>     
> - signer later says that they have signed $m'$ (signature repudiation).
>     

**Cosa dice:**

Qui il cattivo non è un hacker esterno, ma **sei tu (il firmatario)**!

Se l'Hash fa pena, tu puoi generare due contratti diversi con lo stesso Hash. Firmi quello buono davanti a tutti. Un anno dopo, quando ti fa comodo, tiri fuori dal cassetto il contratto cattivo, ci appiccichi la tua stessa firma e dici: "Ah-ah! Io avevo firmato quest'altro, il giudice mi dà ragione!". Questo si chiama _ripudio della firma_, e distrugge il valore legale della crittografia.

Per farti toccare con mano quanto sia assurdo e pericoloso il "Copia-Incolla Letale" (Catastrofe 2), ti ho preparato un simulatore. Mettiti nei panni dell'hacker, sfrutta un Hash debole e ruba la firma di Alice!

---

### 14. Il Prossimo Capitolo: DSA

**Testo Originale:**

> Let us conclude this section showing another standard called **DSA** (digital signature algorithm) that is based on a variation of ElGamal.

Come sempre, il professore non ci lascia con l'amaro in bocca. RSA-FDH è uno standard fantastico, ma c'è un altro gigante nel mondo delle firme digitali: il **DSA**.

E indovina su cosa si basa? Esattamente sulla matematica di **ElGamal** e dei logaritmi discreti che abbiamo studiato nelle lezioni precedenti!

Se hai le prossime diapositive su DSA, mandala pure e iniziamo a smontare il nuovo algoritmo!


---
Chiudiamo il cerchio sulle firme digitali. Come anticipato, dopo aver visto RSA-FDH, il professore ci presenta il suo grande rivale: il **DSA (Digital Signature Algorithm)**.

Questo algoritmo è un pezzo di storia: è stato lo standard ufficiale del governo americano (NIST) ed è basato sulla stessa identica matematica di ElGamal (i logaritmi discreti). Ancora oggi, la sua variante su curve ellittiche (**ECDSA**) è usata ovunque, persino per firmare le transazioni di Bitcoin!

Smontiamo questo capolavoro e scopriamo il suo tallone d'Achille nascosto negli appunti del prof!

### 15. Il Setup: Terreno Condiviso e Chiavi (La Fabbrica)

**Testo Originale:**

> **DSA (Standard)**
> 
> [...] bases its security on the CDLP in $\mathbb{Z}_p^*$.
> 
> Today it is more advisable to use the elliptic curve version of DSA, called ECDSA, which is more efficient [...]
> 
> Domain parameters fixed for everybody: $p \in \mathbb{P}$ a large prime number (~2048 bits), $q \in \mathbb{P}$ (~256 bits) such that $q | p-1$, $g$ an element of order $q$. $H: \{0,1\}^\infty \to \mathbb{Z}_q$ a public hash function.
> 
> Gen: given in input $(p, q, g)$ returns
> 
> - the private key $x \in \{0, 1, \dots, q-1\}$
>     
> - the public key $h := g^x$
>     

**Cosa dice:**

La preparazione è letteralmente identica a ElGamal.

C'è un "terreno di gioco" pubblico formato da $p$, $q$ e dal generatore $g$. C'è il nostro fidato tritacarne, la funzione di Hash $H$.

Tu crei la tua **chiave privata ($x$)** scegliendo un numero a caso. Poi calcoli la tua **chiave pubblica ($h$)** elevando $g$ alla $x$. Chiunque voglia scoprire la tua chiave privata deve risolvere un logaritmo discreto impossibile.

---

### 16. La Firma e l'Avvertimento Letale (La Funzione `Sign`)

**Testo Originale:**

> Sign: on input the private key $x$ and a message $m \in \{0,1\}^\infty$ and an **ephemeral key** $k \in \mathbb{Z}_q^*$ returns the signature $(r, s)$ where
> 
> $\begin{cases} r := g^k \bmod q \\ s := k^{-1}(H(m) + x \cdot r) \bmod q \end{cases}$
> 
> $\hookrightarrow$ **must always be FRESH RANDOMNESS**

**Cosa dice:**

Per firmare un documento $m$, non usi solo la tua chiave privata. Devi "tirare un dado" per generare una chiave usa-e-getta **$k$**. La firma finale è composta da due numeri, $(r, s)$:

1. **La metà pubblica ($r$):** Prendi il generatore e lo elevi al tuo dado $k$.
    
2. **La metà matematica ($s$):** Questa è un'equazione geniale che mescola tutto. Mescola l'impronta digitale del documento ($H(m)$), la tua chiave privata segreta ($x$), la prima metà della firma ($r$), e divide tutto per il dado $k$ (usando l'inverso modulare $k^{-1}$).
    

**L'Avvertimento Letale (Quello in verde):**

Il professore scrive a caratteri cubitali e con un asterisco rosso: **"must always be FRESH RANDOMNESS"**.

Se tu, per pigrizia o per un bug del software, usi lo stesso dado $k$ per firmare _due documenti diversi_, sei morto. La matematica di quell'equazione $s$ collassa: un hacker può prendere le tue due firme, fare una semplice sottrazione, isolare la tua chiave privata $x$ e rubartela per sempre. _(Fun Fact: È esattamente così che un gruppo di hacker bucò la PlayStation 3 anni fa: la Sony usava ECDSA ma si era dimenticata di cambiare il numero $k$ ad ogni firma!)_

---

### 17. La Verifica Magica (La Funzione `Ver` e la Prova)

**Testo Originale:**

> Ver: on input the public key $h$, a message $m \in \{0,1\}^\infty$ and a signature $(r, s)$, outputs
> 
> $1 \iff r = g^{H(m)s^{-1}} h^{r s^{-1}}$
> 
> [...]
> 
> **Proof:**
> 
> $s = k^{-1}(H(m) + x \cdot r) \bmod q \implies$
> 
> $k = H(m)s^{-1} + x \cdot r \cdot s^{-1} \bmod q$.
> 
> Let us check it:
> 
> $g^{H(m)s^{-1}} h^{r s^{-1}} = g^{H(m)s^{-1}} (g^x)^{r s^{-1}} = g^{H(m)s^{-1} + x r s^{-1}} = g^k = r$.

**Cosa dice:**

Chi riceve il tuo documento e la firma $(r, s)$ fa un controllo che sembra stregoneria pura.

Prende l'impronta del documento $H(m)$, la tua chiave pubblica $h$, e le due metà della firma. Li mischia in quella gigantesca formula con gli esponenti. Se il risultato magico torna ad essere esattamente uguale a $r$, la firma è autentica.

**La dimostrazione (L'ultima slide):**

Il professore ci fa vedere perché funziona. Prende l'equazione con cui tu hai creato $s$, e la rigira usando l'algebra di base per isolare il dado $k$:

$$k = \frac{H(m)}{s} + \frac{x \cdot r}{s}$$

Quando il destinatario fa il suo calcolo assurdo ($g^{H(m)s^{-1}} h^{r s^{-1}}$), non sta facendo altro che ricostruire pezzo per pezzo l'esponente di $g$. Poiché $h$ è in realtà $g^x$, l'esponente totale diventa esattamente la formula qui sopra!

Quindi, tutto quel colossale calcolo si riduce semplicemente a **$g^k$**.

E cos'era $g^k$? Era esattamente la primissima cosa che avevi calcolato: **$r$**.

Se i due numeri combaciano, significa inequivocabilmente che solo chi conosceva il segreto $x$ poteva aver incastrato l'equazione $s$ in modo così perfetto.

Per farti vedere come tutti questi pezzi (il messaggio, il dado, la chiave segreta) si annodino in $s$ e poi si sciolgano perfettamente durante la verifica, ti ho preparato un simulatore del DSA. Prova a firmare un messaggio e guarda la matematica fare il suo dovere!


---
Il professore alza ancora l'asticella della paranoia crittografica. Pensavamo che il test IND-CPA fosse il boss finale? Sbagliato.

Entriamo nel regno degli **Attacchi a Testo Cifrato Scelto (CCA - Chosen-Ciphertext Attack)**. Qui il professore formalizza matematicamente i trucchi sporchi che abbiamo usato nei disastri A3 e A5!

Smontiamo questo nuovo "gioco" spietato.
### 18. Il Gioco dell'Oracolo (Definition 4.32)

**Testo Originale:**

> **4.8 Chosen-ciphertext attackers**
> 
> [...] Let us consider the following game between a PPT algorithm $\mathcal{A}$, a challenger $\mathcal{C}$, where $\mathcal{A}$ can consult a decryption oracle $\mathcal{O}$.
> 
> 1. $\mathcal{C}$ runs $Gen(N)$ and obtains $(pk, sk)$
>     
> 2. $\mathcal{C}$ picks a random message $m^* \in M$
>     
> 3. $\mathcal{C}$ computes $c^* := E_{pk}(m^*)$
>     
> 4. $\mathcal{A}$ is given $c^*$
>     
> 5. $\mathcal{A}$ can send to the oracle $\mathcal{O}^{dec}_{sk}$ ciphertexts $c_1, c_2 \dots c_t \in C$
>     
> 6. the oracle $\mathcal{O}$ returns $m_1, m_2 \dots m_t$ to $\mathcal{A}$ such that $E_{pk}(m_i) = c_i$. The oracle **will not answer** if asked about $c^*$.
>     
> 7. $\mathcal{A}$ returns $m \in M$. $\mathcal{A}$ wins the game $\iff m = m^*$.
>     

**Cosa dice:**

Questo è lo scenario in cui l'hacker ($\mathcal{A}$) ha i poteri massimi immaginabili.

Lo Sfidante prende un messaggio segreto $m^*$, lo cripta creando la sfida **$c^*$** e lo dà all'hacker. L'obiettivo dell'hacker è indovinare $m^*$.

Ma questa volta l'hacker ha un'arma illegale: l'**Oracolo di Decifratura ($\mathcal{O}$)**. È un terminale (o un server vulnerabile) che ha la chiave privata e risponde ai comandi dell'hacker.

L'hacker può prendere un miliardo di file cifrati diversi, mandarli all'oracolo, e l'oracolo glieli decripterà tutti restituendogli i testi in chiaro.

C'è una sola ferrea regola (altrimenti il gioco non avrebbe senso): l'oracolo si rifiuterà categoricamente di decriptare il file bersaglio $c^*$.

---

### 19. Perché questo scenario è realistico?

**Testo Originale:**

> This attacker capability is reasonable for many reasons, especially in those PubKc where signing is similar to decrypting.
> 
> [...] Indeed we have already shown that something suspicious happens when the attacker has the possibility to access to some decrypted information (see for example classical misuses A3 or A5).

**Cosa dice e cosa significa:**

Potresti pensare: _"Ma quale idiota mi lascerebbe usare un server che decripta i file per conto mio?"_

Il prof ti risponde ricordandoti gli **Attacchi A3 e A5** visti in precedenza.

In Textbook RSA, firmare un documento equivale matematicamente a decriptarlo (elevi alla chiave privata $d$). Quindi, ogni volta che convinci un server o una persona a "firmare" un file (come un log di sistema, o un file mascherato/accecato), stai letteralmente usando quella persona come un Oracolo di Decifratura!

Quindi sì, il modello CCA descrive perfettamente la realtà delle reti informatiche moderne.

---

### 20. La Condanna Definitiva e la Causa Radicale

**Testo Originale:**

> We say that $(Gen, Enc, Dec)$ is **CCA-secure** [...] if no PPT algorithm can win the above game with non-negligible probability.
> 
> We already know that RSA is not secure w.r.t. IND-CPA. We will now show that (textbook) RSA is **not CCA-secure**.
> 
> [...] These vulnerabilities that we are going to discuss (again) are based on the following (textbook) RSA property, called the **homomorphic property**:

**Cosa dice:**

Un sistema è "CCA-secure" solo se l'hacker perde questa sfida.

Ma sappiamo già come va a finire per il nostro vecchio amico Textbook RSA: non solo era stato disintegrato dal test IND-CPA (perché era deterministico), ma viene polverizzato anche da questo test CCA.

Il professore chiude la pagina svelandoci il vero "bug di sistema" matematico che permette agli hacker di ingannare l'Oracolo: la **Proprietà Omomorfica**.

È quella maledetta caratteristica dell'algebra modulare che ci ha permesso, nell'attacco A3, di "accecare" il file segreto $c^*$ moltiplicandolo per un numero a caso ($k^e$). In questo modo, abbiamo aggirato la regola numero 6 dell'Oracolo (che vieta di inserire $c^*$), perché l'abbiamo truccato facendolo sembrare un altro file ($c \cdot k^e$). L'Oracolo l'ha decriptato, e noi abbiamo diviso il risultato per $k$.

Nella prossima pagina il prof metterà sicuramente in formule questa proprietà omomorfica, chiudendo il cerchio su tutti gli attacchi che abbiamo visto fin dal primo giorno!

---

Ecco la formalizzazione matematica del disastro. Il professore mette finalmente a nudo il "bug" matematico di RSA e ci mostra come l'hacker lo usa per distruggere completamente il gioco CCA.

È la rivincita dell'hacker contro l'Oracolo. Smontiamo questo attacco in 4 mosse!
### 21. Il "Bug" di Sistema: La Proprietà Omomorfica (Proposition 4.33)

**Testo Originale:**

> **Proposition 4.33 (homomorphic property of RSA)**
> 
> Let $(n, e)$ be an RSA public key. The encryption function $E_e: \mathbb{Z}_n \to \mathbb{Z}_n, x \mapsto x^e$ satisfies
> 
> $\forall m_1, m_2 \in \mathbb{Z}_n \quad E_e(m_1 \cdot m_2) = E_e(m_1) \cdot E_e(m_2)$.
> 
> **Proof**
> 
> $E_e(m_1 \cdot m_2) = (m_1 m_2)^e = m_1^e \cdot m_2^e = E_e(m_1) \cdot E_e(m_2)$.

**Cosa dice:**

Questo è il tallone d'Achille del Textbook RSA (lo stesso che permetteva l'attacco A5 sulle firme).

Il prof lo dimostra con un solo passaggio algebrico: **la cifratura di una moltiplicazione è uguale alla moltiplicazione delle cifrature**.

**Cosa significa nella pratica:** Come scrive il prof subito sotto: _"Possiamo determinare la cifratura del prodotto di due messaggi anche senza conoscere i messaggi!"_.

Se io intercetto il tuo stipendio cifrato, non ho bisogno di decriptarlo per raddoppiartelo. Mi basta prendere il file cifrato e moltiplicarlo per la cifratura del numero 2 ($2^e$). Il tuo stipendio sul server diventerà magicamente il doppio, e il server non se ne accorgerà finché non andrà a decriptarlo!

Questa malleabilità dei dati rende **impossibile** garantire la sicurezza CCA.

---

### 22. L'Inganno all'Oracolo (Proposition 4.34)

**Testo Originale:**

> **Proposition 4.34**
> 
> (textbook) RSA is not CCA-secure.
> 
> **Proof**
> 
> Let $c^*$ be the challenge generated at line 4 in the CCA security game. The adversary goal is to find $m^*$ such that $(m^*)^e = c^*$.
> 
> The adversary can proceed as follows (see also A3):
> 
> 1. create a "new ciphertext" $c := 2^e c^*$
>     
> 2. ask the oracle $\mathcal{O}^{dec}_{sk}$ to decrypt $c$
>     
> 3. the oracle, since $c \neq c^*$, returns $m$ such that $m^e = c$
>     
> 4. $\mathcal{A}$ returns $m / 2$.
>     

**Cosa dice:**

Ti ricordi la regola ferrea dell'Oracolo nel gioco CCA? _"Ti decripto tutto, tranne la sfida originale $c^*$.

L'hacker usa la proprietà omomorfica per aggirare questa regola:

1. L'hacker prende la sfida incomprensibile $c^*$. Invece di inviarla all'Oracolo (che la rifiuterebbe), crea un file ibrido $c$: moltiplica la sfida per il numero 2 cifrato ($2^e$).
    
2. Invia questo nuovo file $c$ all'Oracolo.
    
3. L'Oracolo guarda il file $c$. Lo confronta con $c^*$. Visto che i bit sono completamente diversi (perché è stato moltiplicato), l'Oracolo dice: _"Regola rispettata, procedo alla decifratura!"_. L'Oracolo applica la chiave privata e restituisce il testo in chiaro $m$.
    
4. Grazie all'omomorfismo, il testo in chiaro $m$ che l'Oracolo restituisce non è altro che il messaggio segreto $m^*$ moltiplicato per 2. All'hacker basta fare una banale divisione ($m / 2$) per estrarre il segreto pulito.
    

**La Vittoria (Matematica):**

Il prof chiude la prova mostrando i calcoli che fa l'Oracolo dietro le quinte:

$\frac{m}{2} = \frac{c^d}{2} = \frac{(2^e c^*)^d}{2} = \frac{2^{ed} (c^*)^d}{2} = \frac{2 m^*}{2} = m^*$.

Probabilità di vittoria dell'hacker: **100%**. Textbook RSA è annientato.

---

### 23. Il Destino Oscuro di ElGamal (Il Cliffhanger)

**Testo Originale:**

> A result similar to Proposition 4.33 can be proved for the ElGamal PubKC. So, not surprisingly...

Il professore ci lascia con una frase a metà, ma ormai siamo diventati dei crittografi esperti e sappiamo esattamente come finisce.

Ci sta dicendo che **anche ElGamal possiede la Proprietà Omomorfica**.

Questo significa che, sebbene ElGamal fosse un bunker inattaccabile contro l'hacker "passivo" (test IND-CPA), non appena mettiamo l'hacker di fronte a un Oracolo CCA, **anche ElGamal crollerà miseramente**. L'hacker potrà manipolare i file cifrati di ElGamal per ingannare l'Oracolo esattamente come ha appena fatto con RSA.

Per risolvere questo problema finale, la crittografia moderna deve usare schemi ancora più complessi (come _Cramer-Shoup_ o il _Padding OAEP_ di cui parlavamo prima) per distruggere ogni traccia di malleabilità.

Per farti provare la soddisfazione di ingannare l'Oracolo, ho preparato un simulatore dell'Attacco CCA. Fai finta di avere in mano il file top-secret $c^*$, mascherarlo con il trucco del "$\times 2$" e fregare il server!

---
Il professore chiude il capitolo della crittografia a chiave pubblica asimmetrica con l'arma definitiva. Questa è la vera ingegneria crittografica usata oggi su Internet.

Avevamo lasciato in sospeso il destino di ElGamal. La prima slide conferma i nostri timori: cade anche lui. Ma la soluzione a tutto questo caos esiste e si chiama **RSA-OAEP**.

Smontiamo la corazza finale di RSA in 4 passaggi!

### 24. La Caduta di ElGamal (Proposition 4.35)

**Testo Originale:**

> **Proposition 4.35**
> 
> ElGamal PubKc is not CCA-secure.
> 
> **Proof**
> 
> Let $c^* = (c_1, c_2) = (g^k, m^* h^k)$ be the challenge.
> 
> Let $c := (c_1, 2c_2) \neq c^*$ and let $m$ be the decryption provided by the oracle. Then
> 
> $\frac{m}{2} = \frac{2c_2 \cdot c_1^{-x}}{2} = m^* \cdot h^k \cdot (g^k)^{-x} = m^* \dots = m^*$.

**Cosa dice:**

Il prof dimostra che l'Attacco CCA (quello di "accecare" l'Oracolo moltiplicando per 2) funziona benissimo anche su ElGamal! L'hacker prende la seconda parte del file cifrato ($c_2$), la moltiplica per 2, e la manda all'Oracolo. L'Oracolo restituisce un testo in chiaro. L'hacker lo divide per 2, e scopre il segreto.

**Lezione fondamentale:** Aver randomizzato il sistema con il "dado" $k$ ci ha salvato dal test IND-CPA, ma non basta per il CCA. Dobbiamo impedire all'hacker di poter _manipolare_ (malleability) i file cifrati in transito.

---

### 25. L'Armatura Definitiva: RSA-OAEP

**Testo Originale:**

> We are now ready to introduce the current version of (real) RSA, known as **RSA PKCS #1 v.2** or **RSA-OAEP (Optimized Asymmetric Encryption Padding)**.
> 
> RSA-OAEP can be proven to be IND-CCA secure assuming the hardness of the RSA problem.
> 
> The idea is that, if we want to prevent the attack [...] we must add a random padding to the message which
> 
> 1. randomizes the encryption
>     
> 2. allows the CCA decryption oracle to **detect tampering** [...] and to stop offering decryptions (return error).
>     

**Cosa dice:**

Ecco il "Vero" RSA, lo standard PKCS #1 v.2 che tutti i server del mondo usano oggi.

Il trucco è ingegnoso: sacrifichiamo un po' di spazio nel messaggio per infilare un'impalcatura matematica fragilissima attorno al testo in chiaro _prima_ di cifrarlo.

Se l'hacker prova a fare il trucchetto di moltiplicare il file cifrato per 2, l'impalcatura interna si frantuma. Quando l'Oracolo decripta e vede i cocci dell'impalcatura rotta, capisce subito che c'è stato un attacco e **spara un allarme di errore** invece di restituire il testo in chiaro. L'hacker è chiuso fuori per sempre.

---

### 26. Il Frullatore: La Rete di Feistel a 2 Round

**Testo Originale:**

> Let $m \in \{0,1\}^k$, for some $k < N$. Let there $k_1, k_2$ be integers [...] auxiliary bits.
> 
> $N = k + k_1 + k_2$
> 
> [...] RSA-OAEP works as follows:
> 
> 1. given $m$, we compute $m' := m \parallel 00\dots0$ ($k_1$ times)
>     
> 2. we generate a random bit string $r \in \{0,1\}^{k_2}$
>     
> 3. we set $t := m' \oplus G(r)$
>     
> 4. we set $s := r \oplus H(t)$
>     
> 5. we obtain $pad(m) := s \parallel t$
>     
> 6. we encrypt $pad(m)$ computing $c := pad(m)^e \bmod n$.
>     

**Cosa dice e cosa significa:**

Il prof descrive un "frullatore" di bit. Non si dà più il messaggio $m$ in pasto alla chiave pubblica $e$. Si fa questo:

1. **L'Allarme Antifurto ($k_1$):** Attacchiamo alla fine del messaggio una fila esatta di "Zeri" ($00\dots0$). Questi zeri sono i sensori dell'allarme.
    
2. **Il Dado ($r$):** Generiamo una stringa casuale.
    
3. **Il Frullatore (XOR $\oplus$):** Usiamo due funzioni di Hash pubbliche ($G$ e $H$) e l'operazione XOR per incrociare a "X" (Rete di Feistel) i due pezzi.
    
    - Il messaggio mascherato diventa $t$.
        
    - Il dado mascherato diventa $s$.
        
4. **Cifratura finale:** Incolliamo $s$ e $t$ insieme ($pad(m)$) e finalmente applichiamo la vecchia formula RSA (eleviamo alla $e$).
    

---

### 27. Il Taglio della Trappola (La Decifratura e lo Scudo CCA)

**Testo Originale:**

> Decryption then works as follows:
> 
> 1. compute $c^d \bmod n$ and obtain $s \parallel t$
>     
> 2. compute $H(t)$ and from $s := H(t) \oplus r$ we obtain $r$
>     
> 3. compute $G(r)$ and from $t := m' \oplus G(r)$ we obtain $m'$
>     
> 4. **check if the last $k_1$ bits of $m'$ are equal to zero**. If yes, remove them and return $m$; otherwise, return the error symbol $\bot$.
>     

**Cosa dice:**

Questo è il capolavoro finale. Come fa il server (o l'Oracolo) a sventare gli attacchi CCA?

Fa le operazioni al contrario. Applica la sua chiave privata $d$, poi esegue l'algebra XOR all'inverso per districare il messaggio dal dado casuale.

**Lo Step 4 è il momento della verità.** Il computer guarda esattamente nel punto in cui dovrebbero esserci i famosi zeri di controllo ($k_1$).

- Se tu hai mandato il file intatto, gli zeri sono lì, perfetti. Il computer li taglia via e ti dà il messaggio.
    
- Ma se l'hacker ha moltiplicato la scatola chiusa per 2 (come in Proposition 4.34)? Questa moltiplicazione manda l'intera rete di Feistel fuori sincrono. Quando il server usa lo XOR per districare la matassa, spunta fuori spazzatura casuale! Gli zeri non ci sono più!
    
    Il server capisce di essere sotto attacco CCA e stampa il grande simbolo **$\bot$ (ERRORE)**, impedendo all'hacker di ottenere qualsiasi informazione. Scacco matto!
    

Per farti vedere come una semplice riga di "Zeri" e un po' di algebra salvino il sistema bancario mondiale da un Oracolo bucato, ti ho costruito il frullatore OAEP interattivo. Mettiti nei panni dell'hacker, prova ad alterare il pacchetto e guarda scattare l'allarme!

---
Il gran finale del capitolo RSA. E per chiudere in bellezza, il professore fa la mossa più leggendaria e amata da tutti gli studenti di matematica del mondo: salta un'intera dimostrazione scrivendo semplicemente _"è troppo complicata"_!

Ma non ci lascia a mani vuote. Ci regala l'intuizione logica che fa stare in piedi tutto il castello. Smontiamo quest'ultima diapositiva!
### 28. Il Teorema Supremo (Theorem 4.36)

**Testo Originale:**

> **Theorem 4.36 [informal]**
> 
> If RSA is hard w.r.t. GenModulus(N), then RSA-OAEP is IND-CCA secure.
> 
> **Proof**
> 
> too complicated. $\blacksquare$

**Cosa dice:**

Questo è il sigillo di garanzia definitivo. Se assumiamo che il problema base di RSA (fattorizzare numeri giganti o calcolare le radici $e$-esime) sia matematicamente intrattabile, allora l'impalcatura che abbiamo costruito (RSA-OAEP) è **ufficialmente a prova di bomba (IND-CCA secure)**.

Significa che un hacker non solo non può scoprire l'intero messaggio, non solo non può dedurre un singolo bit, ma non può nemmeno usare un server Oracolo per farsi aiutare, perché qualsiasi tentativo di manipolazione farà scattare l'allarme.

La dimostrazione formale richiederebbe decine di pagine di probabilità e teoria della complessità, quindi il prof mette il suo bel quadratino nero ($\blacksquare$) e passa oltre.

---

### 29. Perché Funziona? (L'Intuizione)

**Testo Originale:**

> The idea is (but many details must be considered) that
> 
> $r$ random $\to G(r)$ looks random to $\mathcal{A} \to m$ is masked,
> 
> $s := H(t) \oplus r$ allows to recover, together with $t$, the value of $r$.
> 
> But $(s, t)$ are "protected" by the hardness of the RSA problem.

**Cosa dice:**

Anche senza la dimostrazione, il prof ci fa riassumere mentalmente i ruoli dei pezzi nel frullatore che abbiamo visto prima:

1. **L'Oscuramento:** Scegliamo un dado a caso $r$. Lo passiamo nella funzione $G$ e otteniamo una maschera crittografica. Applichiamo questa maschera al messaggio $m$ (che contiene i nostri famosi zeri di controllo). Ora all'hacker il messaggio sembra rumore bianco.
    
2. **Il Recupero:** Per permettere al destinatario di togliere la maschera, dobbiamo spedirgli anche il dado $r$. Ma non possiamo mandarlo in chiaro! Quindi usiamo la seconda funzione $H$ e lo nascondiamo dentro $s$.
    
3. **La Cassaforte:** A questo punto abbiamo il pacchetto $(s, t)$. Nessuno dei due pezzi ha senso da solo. E cosa facciamo? Li chiudiamo entrambi dentro la cassaforte di puro RSA (elevando alla $e$).
    

L'hacker è fregato due volte: non può sbirciare i numeri perché sono dentro la cassaforte RSA, e non può "scuotere" la cassaforte (proprietà omomorfica) perché appena lo fa, i pezzi $(s, t)$ si disallineano e gli zeri di controllo vengono distrutti.

---

### 30. La Redenzione di ElGamal

**Testo Originale:**

> Also ElGamal could be made IND-CCA secure similarly.

**Cosa dice:**

Una frase minuscola in fondo alla pagina, ma di un'importanza capitale. Avevamo lasciato ElGamal distrutto dall'attacco CCA (la moltiplicazione per 2).

Il prof ci rassicura: **ElGamal non è da buttare**. Esattamente come abbiamo costruito l'impalcatura OAEP per proteggere RSA dalla sua malleabilità, esistono impalcature matematiche simili (nella vita reale, il sistema crittografico di _Cramer-Shoup_) che avvolgono ElGamal, inseriscono controlli di integrità e lo rendono perfettamente IND-CCA secure.

Si chiude qui un capitolo immenso della crittografia! Abbiamo preso algoritmi puri ma fragili e li abbiamo trasformati in standard industriali corazzati.

Hai altre diapositive per iniziare un nuovo argomento, o ci fermiamo a festeggiare questa vittoria matematica?