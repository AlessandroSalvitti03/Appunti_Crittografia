
# Appunti: Principi e Applicazioni degli Schemi di Firma Digitale

## 1. Cos'è una Firma Digitale?

Una firma digitale è un meccanismo crittografico che permette a un firmatario di allegare un marchio di autenticità non falsificabile a un dato. Funziona in analogia a una firma cartacea, ma digitalmente: chiunque possieda la chiave pubblica può verificarla, ma solo chi detiene la chiave privata può generarla.

Uno schema di firma digitale è composto da una tripla di algoritmi efficienti $(KeyGen, Sign, Verify)$:

- **$KeyGen(\beta) \to (sk, pk)$**: Algoritmo probabilistico che, dato un parametro di sicurezza, genera la chiave privata (segreta) $sk$ e la chiave pubblica $pk$.
- **$Sign(sk, m) \to \sigma$**: Algoritmo probabilistico che genera una firma $\sigma$ per il messaggio $m$.
- **$Verify(pk, m, \sigma) \to {0, 1}$**: Algoritmo deterministico che restituisce 1 (accetta) se la firma è valida, 0 (rifiuta) altrimenti.

Le tre proprietà fondamentali richieste sono:

1. **Correttezza**: Una firma generata onestamente su un messaggio viene sempre verificata con successo ($Verify(pk, m, Sign(sk, m)) = 1$).
2. **Infalsificabilità**: Nessun avversario senza la chiave privata può produrre una firma valida.
3. **Non ripudiabilità**: Il firmatario non può negare di aver prodotto una firma valida.

## 2. Modello di Sicurezza: EUF-CMA

Lo standard di sicurezza per le firme digitali è l'**EUF-CMA** (_Existential Unforgeability under Chosen-Message Attack_). Questo modello simula una minaccia realistica in cui l'avversario ha accesso a un "oracolo di firma" ($O_{Sign}$), ovvero può richiedere e ottenere firme valide su messaggi a sua scelta senza mai conoscere la chiave privata $sk$.

**Il gioco EUF-CMA** si svolge tra un Challenger e un Avversario:

1. **Setup**: Il Challenger genera $(sk, pk)$ e invia $pk$ all'avversario.
2. **Fase di Query**: L'avversario interroga l'oracolo su polinomialmente molti messaggi $m_1, \dots, m_q$, ottenendo le firme $\sigma_i$.
3. **Falsificazione (Forgery)**: L'avversario vince se riesce a produrre una coppia $(m^*, \sigma^*)$ valida tale che $m^*$ **non** faccia parte dei messaggi precedentemente richiesti all'oracolo ($m^* \notin {m_1, \dots, m_q}$). Lo schema è sicuro se la probabilità di vittoria dell'avversario è trascurabile.

## 3. Il primo tentativo: Firme RSA e i suoi problemi

### 3.1 RSA "Textbook"

Un approccio ingenuo è usare direttamente la funzione trapdoor di RSA.

- **Sign**: $\sigma := m^d \pmod n$.
- **Verify**: Accetta se $\sigma^e \equiv m \pmod n$.

Questo approccio garantisce la correttezza, ma **fallisce catastroficamente la sicurezza EUF-CMA** a causa di due attacchi:

- **Attacco 1 (Falsificazione Esistenziale)**: L'avversario sceglie una firma arbitraria $\sigma^*$ a caso, calcola $m^* := (\sigma^*)^e \pmod n$ e propone la coppia $(m^*, \sigma^*)$. Questa verifica sempre, senza bisogno di interrogare l'oracolo.
- **Attacco 2 (Falsificazione Omomorfica)**: Sfruttando la struttura moltiplicativa di RSA, l'avversario chiede le firme $\sigma_1$ su $m_1$ e $\sigma_2$ su $m_2$. Moltiplicandole ottiene $\sigma_1 \cdot \sigma_2 \pmod n$, che risulta essere una firma valida per il messaggio $m_1 \cdot m_2$ (mai richiesto all'oracolo).

### 3.2 La soluzione: RSA-FDH (Full Domain Hash)

Entrambi gli attacchi sfruttano il fatto che il messaggio $m$ viene inserito direttamente nella funzione RSA. La soluzione è applicare una funzione hash crittografica $H$ prima di firmare: $\sigma := H(m)^d \pmod n$.

Perché l'hash funziona:

- **Resistenza alla preimmagine**: Blocca l'Attacco 1. L'avversario dovrebbe trovare un $m^*$ tale che $H(m^*) = (\sigma^*)^e$, ovvero dovrebbe invertire l'hash, operazione considerata infattibile.
- **Resistenza alle collisioni**: Blocca l'Attacco 2. L'avversario avrebbe bisogno che $H(m_1) \cdot H(m_2) \equiv H(m_1 m_2)$, ma un hash sicuro distrugge qualsiasi struttura algebrica moltiplicativa.
- **Efficienza**: Permette di firmare messaggi di lunghezza arbitraria comprimendoli in un valore di dimensione fissa.
* **Resistenza alla seconda preimmagine:** Previene il riutilizzo della firma. Anche se un avversario possiede una firma valida $\sigma$ su un messaggio $m$, non può trovare un messaggio diverso $m' \neq m$ per cui la stessa firma sia valida, poiché ciò richiederebbe di trovare un $m'$ tale che $H(m') = H(m)$, rompendo la resistenza alla seconda preimmagine.

**Sistemi Legacy e Vulnerabilità:** Molti sistemi legacy utilizzano ancora il padding $PKCS\#1$ v1.5, che precede lo standard PSS e non possiede una dimostrazione di sicurezza "tight". Questo standard obsoleto è stato la causa di diversi gravi attacchi pratici, in particolare la famiglia di attacchi noti come _Bleichenbacher padding oracle attacks_.

_Nota pratica:_ Nella realtà, RSA-FDH non è usato perché richiederebbe hash enormi (pari al modulo RSA, 2048-4096 bit). Lo standard di fatto è **RSA-PSS**, che usa un _salt_ randomico e funzioni di espansione per adattarsi a hash standard come SHA-256.

## 4. Un nuovo paradigma: Protocolli di Identificazione e Protocolli Sigma

Un approccio più modulare per creare firme digitali consiste nel partire da un **protocollo di identificazione interattivo** e renderlo non interattivo tramite una funzione hash.

**Challenge:** Il Verifier campiona una sfida casuale $c \leftarrow \mathcal{CH}$, dove $\mathcal{CH}$ è lo spazio delle sfide (Challenge Space), e la invia al Prover

Un protocollo di identificazione interattivo a tre mosse funziona così:

1. **Commitment**: Il Prover (che conosce la $sk$) invia un impegno $R$ al Verifier.
2. **Challenge**: Il Verifier invia una sfida casuale $c$.
3. **Response**: Il Prover invia una risposta $s$ calcolata partendo da $sk$, $R$ e $c$. Il Verifier controlla se la risposta è corretta.

**Definizione di Protocollo Sigma ($\Sigma$)**: Un protocollo a tre mosse si dice "Sigma" se soddisfa tre proprietà:

1. **Completezza** (Correttezza per prover onesti).
2. **Special Soundness**: Date due esecuzioni valide con lo _stesso_ commitment $R$ ma sfide _diverse_ ($c \neq c'$), è possibile estrarre la chiave privata $sk$.
3. **Honest-Verifier Zero-Knowledge (HVZK)**: Un Verifier onesto non apprende assolutamente nulla sulla chiave privata $sk$ dall'interazione.

## 5. Lo Schema di Firma di Schnorr
Prima di analizzare il protocollo interattivo, definiamo lo schema di firma di Schnorr, basato sulla difficoltà del logaritmo discreto in un gruppo ciclico $\mathbb{G}$ di ordine primo $q$, con generatore $g$. A differenza di RSA-FDH, l'ordine $q$ (es. 256 bit per le curve ellittiche) combacia perfettamente con l'output di funzioni hash standard come SHA-256.

Lo schema è così definito:
* **KeyGen:** Campiona (sceglire un numero a caso) $x \leftarrow \mathbb{Z}_q$ e imposta $h := g^x \in \mathbb{G}$. Output $sk := x$ e $pk := (g, h)$. (x la chiave privata e h chiave pubica x sicura grazie al logaritmo discreto)
- **Sign(sk, m):** 
1. Campiona $r \leftarrow \mathbb{Z}_q$ e calcola il commitmeent $R := g^r$. 
2. Calcola la challenge $c := H(R \parallel m) \in \mathbb{Z}_q$. 3. Calcola la risposta $s := r + c \cdot x \pmod q$. 4. Output $\sigma := (R, s)$. 
* **Verify(pk, m, $\sigma$):** Parsa $\sigma = (R, s)$. Accetta se e solo se $g^s = R \cdot h^c$, dove $c := H(R \parallel m)$.

**Dimostrazione di Correttezza:** Per qualsiasi firma generata onestamente, l'equazione di verifica è sempre soddisfatta:

$$g^s = g^{r+cx} = g^r \cdot (g^x)^c = R \cdot h^c$$

## 5.1 Il Protocollo di Identificazione di Schnorr

Si basa sulla difficoltà del logaritmo discreto in un gruppo ciclico $\mathbb{G}$ di ordine primo $q$, con generatore $g$.

- **Chiavi**: $sk = x \in \mathbb{Z}_q$, $pk = h = g^x$.

**Flusso del protocollo**:

1. **Commitment**: Prover sceglie un $r$ casuale e invia $R := g^r$.
2. **Challenge**: Verifier invia $c$ casuale in $\mathbb{Z}_q$.
3. **Response**: Prover invia $s := r + c \cdot x \pmod q$.
4. **Verifica**: Verifier accetta se $g^s = R \cdot h^c$.

### 5.1.1 Dimostrazione: Honest-Verifier Zero-Knowledge (Teorema 6.1)

_Affermazione:_ La distribuzione di un trascritto reale $(R, c, s)$ è identica a quella di un simulatore che non conosce la chiave privata $x$. **Dimostrazione del Simulatore:**

1. Il simulatore prende in input solo la chiave pubblica $h$.
2. Sceglie valori completamente casuali per la risposta $s$ e per la sfida $c$ in $\mathbb{Z}_q$.
3. "Forza" la validità calcolando a ritroso il commitment: $R := g^s \cdot h^{-c}$.
4. Sostituendo nell'equazione di verifica: $R \cdot h^c = (g^s \cdot h^{-c}) \cdot h^c = g^s$. Il trascritto generato è valido.

Questo dimostra che la conoscenza di $x$ non aggiunge alcuna informazione che non potesse essere già ricavata dalla semplice chiave pubblica $h$. Questa simulazione funziona solo perché il simulatore, a differenza del Verifier reale, può scegliere $c$ _prima_ di determinare $R$.

### 5.1.2 Dimostrazione: Special Soundness (Lemma 5.1)

_Affermazione:_ Avendo due trascritti validi con lo stesso $R$ ma diverse sfide ($c \neq c'$), si ricava la chiave privata $x$. **Dimostrazione:** Siano i due trascritti validi $(R, c, s)$ e $(R, c', s')$. Essendo entrambi validi valgono le relazioni: $g^s = R \cdot h^c$ $g^{s'} = R \cdot h^{c'}$ Dividendo la prima per la seconda: $g^{s-s'} = h^{c-c'} = (g^x)^{c-c'} = g^{x(c-c')}$ Essendo negli esponenti (modulo $q$ primo), si ottiene: $s-s' \equiv x(c-c') \pmod q$ Poiché $c \neq c'$, la differenza $(c-c')$ è invertibile, permettendo di estrarre $x$: **$x = (s - s') \cdot (c - c')^{-1} \pmod q$**.

## 6. La Trasformata di Fiat-Shamir

Il protocollo interattivo diventa uno schema di firma digitale sostituendo il Verifier con una funzione hash. Invece di aspettare la sfida $c$ dal Verifier, il firmatario la calcola da solo eseguendo l'hash sul commitment $R$ unito al messaggio $m$: **$c := H(R \parallel m)$**.

Questa geniale intuizione definisce la trasformata di Fiat-Shamir: **Qualsiasi Protocollo Sigma può essere convertito in uno schema di firma digitale sicuro (EUF-CMA) nel modello a oracolo random**. Se si applica Fiat-Shamir al protocollo di identificazione di Schnorr, si ottengono le famose **Firme di Schnorr**. L'intuizione della sicurezza sta nel fatto che chi falsifica la firma, fissando $R^*$, dovrebbe indovinare preventivamente l'output dell'hash, oppure sconfiggere la Special Soundness riuscendo a rispondere a multiple sfide per lo stesso $R^*$ (che, come dimostrato, equivale a risolvere il logaritmo discreto).

## 7. Il Protocollo Guillou-Quisquater (GQ)

Per mostrare la generalità dei Protocolli Sigma, il documento presenta il protocollo GQ, che applica gli stessi principi della Special Soundness e HVZK all'ambiente aritmetico di RSA, anziché ai logaritmi discreti.

- **Setup**: Modulo RSA $n=pq$, esponente primo $e$ coprimo con $\phi(n)$. $sk = u \in \mathbb{Z}_n^*$, $pk = v = u^e \pmod n$.
- **Commitment**: $R := r^e \pmod n$.
- **Challenge**: $c \in {0, \dots, e-1}$.
- **Response**: $s := r \cdot u^c \pmod n$.
- **Verifica**: $s^e \equiv R \cdot v^c \pmod n$.

**Dimostrazione Special Soundness in GQ:** Dati $(R, c, s)$ e $(R, c', s')$ validi. $s^e \equiv R \cdot v^c$ e $(s')^e \equiv R \cdot v^{c'}$. Dividendo: $(s/s')^e \equiv v^{c-c'} \pmod n$. Poiché $e$ è primo e $0 < |c-c'| < e$, si ha $\gcd(c-c', e) = 1$. Tramite l'identità di Bézout, troviamo $\alpha, \beta$  interi tali che $\alpha(c-c') + \beta e = 1$. Costruiamo il valore estrattore $w := (s/s')^\alpha \cdot v^\beta \pmod n$. Elevando alla $e$: $w^e = (s/s')^{e\alpha} \cdot v^{e\beta} \equiv v^{\alpha(c-c')} \cdot v^{\beta e} = v^{\alpha(c-c')+\beta e} = v^1 = v \pmod n$. Poiché in $\mathbb{Z}_n^*$ l'elevazione a $e$ è una biiezione, c'è un'unica radice e-esima, che è $u$. L'attaccante ha estratto la chiave privata: **$u \equiv w \pmod n$**.

## 8. DSA (Digital Signature Algorithm): Perché NON è un Protocollo Sigma

Standardizzato dal NIST nel 1994 e basato sui logaritmi discreti (come Schnorr), DSA presenta una differenza critica nel modo in cui calcola la challenge. Nella firma DSA, il valore $c$ è calcolato unicamente sul messaggio: **$c := H(m)$** anziché su $R$ e $m$ ($H(R \parallel m)$).

**Conseguenze Teoriche (Fallimento HVZK):** Un simulatore non può più generare $c$ casualmente e adattare $R$, perché $c$ è vincolato a $H(m)$, indipendentemente da $R$. L'equazione di verifica di DSA lega rigidamente $s, r_1$ (il residuo di $R$) e $c$. Senza conoscere la chiave privata $x$, non è possibile far bilanciare i conti per far tornare una simulazione indistinguibile dal reale. Di conseguenza, **DSA non possiede la proprietà HVZK e non è un protocollo Sigma**.

**Conseguenze Pratiche:** A causa di questa architettura, a differenza di Schnorr, per DSA non esiste una dimostrazione di sicurezza "tight" (stringente) nel modello standard che lo riduca al logaritmo discreto. Non esiste un'estrazione algebrica pulita in caso di collisioni. Queste lacune teoriche e i gravi fallimenti causati dai riutilizzi o dai _bias_ dei valori di $r$, hanno portato all'abbandono di DSA a favore di alternative strutturalmente equivalenti a Schnorr, come EdDSA (Ed25519).