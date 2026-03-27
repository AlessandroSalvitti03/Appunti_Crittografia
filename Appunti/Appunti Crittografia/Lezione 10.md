## 1. Il Ripasso

La lavagna di sinistra e una parte di quella centrale servono solo a rinfrescare la memoria sulla lezione precedente:

- Ribadisce che "hard" significa che per ogni avversario PPT (_Probabilistic Polynomial-Time_, cioè un hacker con un computer normale), la probabilità di vincere è trascurabile.
    
- Sulla seconda lavagna riassume il "Gioco 1" (il **DLog Game**): Il Challenger ti dà $h$, e l'Avversario deve trovare l'esponente segreto $x$ tale che $g^x = h$.
    

## 2. La Nuova Sfida: CDHP

Sulla destra della seconda lavagna e nei tuoi appunti manoscritti, il prof introduce il **Gioco 2**. Lo chiama **Computational Diffie-Hellman Problem (CDHP)**.

Perché introduce un nuovo gioco? Perché nella realtà, quando Alice e Bob comunicano, l'hacker non sta solo cercando di risolvere un logaritmo a caso. L'hacker sta intercettando uno scambio di chiavi ben preciso!

Leggiamo la **Definition 3.6** dai tuoi appunti, che trasforma l'intercettazione di un hacker in un gioco a turni tra il Challenger (l'arbitro) e l'Avversario (l'hacker).

**La preparazione (Il mondo di gioco):**

- **Step 1:** Il Challenger stabilisce le regole pubbliche. Sceglie un gruppo ciclico $G$, la sua grandezza $n$, e un generatore $g$.
    

**Le mosse del Challenger (Simula Alice e Bob):**

- **Step 2:** Il Challenger sceglie due numeri segreti a caso, $a$ e $b$ (che sarebbero le password private di Alice e Bob).
    
- **Step 3:** Il Challenger calcola $g^a$ e $g^b$ (che sono i lucchetti pubblici che Alice e Bob si scambiano su internet).
    

**Il turno dell'Hacker:**

- **Step 4:** L'Avversario riceve in pasto tutte le informazioni pubbliche: $(G, n, g, g^a, g^b)$. Sta letteralmente intercettando la conversazione pubblica.
    
- **Step 5:** L'Avversario fa i suoi calcoli e "sputa fuori" la sua risposta finale, che chiamiamo $S$. _(Nota: sugli appunti sembra scritto $1 \le S \le n-1$, ma concettualmente $S$ è il presunto segreto condiviso che vive nel gruppo)._
    

**La condizione di Vittoria:**

- **Step 6:** Il Challenger controlla la risposta. L'Avversario prende $1$ (Vince!) se la sua risposta $S$ è esattamente uguale a **$g^{ab}$** (il segreto condiviso finale di Diffie-Hellman). Altrimenti prende $0$ (Perde!).
    

---

## La differenza cruciale tra i due giochi

Il professore ti sta facendo notare una sfumatura fondamentale della sicurezza informatica mettendo i due giochi a confronto sulla lavagna:

- **Nel DLog (Logaritmo Discreto):** All'hacker viene chiesto di trovare la password originale (l'esponente).
    
- **Nel CDHP (Diffie-Hellman):** All'hacker **NON** viene chiesto di scoprire le password originali $a$ o $b$. Gli viene solo chiesto di calcolare il risultato finale $g^{ab}$.
    

Se l'hacker fosse capace di risolvere il primo gioco (DLog), vincerebbe automaticamente anche il secondo! Basterebbe usare il DLog per scoprire $a$ partendo da $g^a$, e poi elevare $g^b$ alla $a$.

Ma la domanda da un milione di dollari (letteralmente, nella ricerca crittografica) è: **è possibile per un hacker vincere il CDHP senza dover per forza risolvere il difficilissimo DLog?** Cioè, esiste una "scorciatoia" per trovare $g^{ab}$ incrociando $g^a$ e $g^b$ senza passare per gli esponenti?

Fino a prova contraria, assumiamo di no, ed è per questo che le tue chat su WhatsApp sono sicure!