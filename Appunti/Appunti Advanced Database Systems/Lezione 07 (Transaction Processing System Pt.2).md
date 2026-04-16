## 1. Importanza del Controllo della Concorrenza

- **Necessità della Concorrenza:** Nei sistemi reali (come banche o sistemi di prenotazione), l'esecuzione sequenziale delle transazioni è impraticabile. È necessario gestire decine o centinaia di transazioni al secondo simultaneamente.
    
- **Modello di Riferimento:** Il sistema modella le operazioni come azioni di input/output (lettura `r` e scrittura `w`) su oggetti astratti come $x, y, z$.
    
- **Obiettivo dello Scheduler:** È il componente che accetta, rifiuta o riordina le operazioni per evitare anomalie causate dall'esecuzione concorrente.
    

---

## 2. Le Anomalie della Concorrenza

L'esecuzione concorrente non regolata può portare a cinque stati incoerenti del database:

- **Update Loss (Perdita di Aggiornamento):** Due transazioni leggono lo stesso dato e lo aggiornano; l'ultima scrittura sovrascrive la prima, perdendo l'incremento di una transazione. (Conflitto Scrittura-Scrittura $W-W$ ).
    
- **Dirty Read (Lettura Sporca):** Una transazione $T_2$ legge un valore modificato da $T_1$ che non ha ancora effettuato il commit. Se $T_1$ fallisce (abort), $T_2$ ha lavorato su uno stato intermedio mai confermato che potrebbe aver già comunicato all'esterno. (Conflitto $R-W$ o $W-W$ con abort ).
    
- **Inconsistent Read (Lettura Inconsistente):** Una transazione legge due volte lo stesso oggetto $x$, ma tra le due letture un'altra transazione modifica $x$ e fa il commit, portando la prima transazione a vedere due valori diversi. (Conflitto $R-W$ ).
    
- **Ghost Update (Aggiornamento Fantasma):** Una transazione $T_1$ legge più oggetti legati da un vincolo di integrità (es. $y+z=1000$). Se un'altra transazione sposta valori tra $y$ e $z$ durante la scansione di $T_1$, quest'ultima vedrà un'apparente violazione del vincolo (es. somma = 1100). (Conflitto $R-W$ ).
    
- **Ghost Insert (Inserimento Fantasma):** Una transazione esegue un calcolo aggregato (es. media stipendi) su un insieme di dati, mentre un'altra transazione inserisce un nuovo record coerente con i criteri di ricerca, alterando il risultato finale. (Conflitto $R-W$ su dati "nuovi" ).
    

---

## 3. Schedule e Serializzabilità

- **Schedule:** Una sequenza cronologica di operazioni di I/O di transazioni concorrenti.
    
- **Ipotesi della Commit-Projection:** Spesso, per semplicità teorica, si considerano solo le transazioni che vanno a buon fine (commit), ignorando quelle annullate.
    
- **Schedule Seriale:** Le transazioni sono eseguite una dopo l'altra, senza sovrapposizioni.
    
- **Schedule Serializzabile:** Uno schedule che produce lo stesso risultato di uno schedule seriale eseguito sulle medesime transazioni.
    

---

## 4. Classi di Serializzabilità: VSR e CSR

Per identificare gli schedule corretti, si definiscono due nozioni di equivalenza:

### View-Serializability (VSR)

Due schedule sono **equivalenti per vista** ($\approx_V$) se presentano:

1. Le stesse relazioni di **read-from**: ogni operazione di lettura legge il valore prodotto dalla medesima operazione di scrittura in entrambi gli schedule.
    
2. Le stesse **scritture finali**: l'ultima operazione di scrittura su ogni oggetto è effettuata dalla stessa transazione in entrambi.
    

- **Limite:** Determinare se uno schedule è VSR è un problema **NP-completo**, quindi non utilizzabile nella pratica.
    

### Conflict-Serializability (CSR)

Si basa sul concetto di **conflitto**: due azioni $a_i$ e $a_j$ sono in conflitto se operano sullo stesso oggetto e almeno una è una scrittura. Esistono conflitti $R-W, W-R$ e $W-W$.

- Due schedule sono **equivalenti per conflitto** ($\approx_C$) se hanno le stesse operazioni e ogni coppia di operazioni in conflitto appare nello stesso ordine in entrambi.
    
- **Grafo dei Conflitti:** Uno schedule è CSR se e solo se il suo grafo dei conflitti (nodi = transazioni, archi = conflitti orientati) è **aciclico**.
    

---

## 5. Relazione tra le Classi e Pratica nei DBMS

- **Gerarchia:** Ogni schedule CSR è anche VSR, ma esistono schedule VSR che non sono CSR (il VSR è una classe più ampia). Gli schedule seriali sono un sottoinsieme di entrambi.
    
- **La Concorrenza Reale:** Nella pratica, i DBMS non costruiscono il grafo per ogni operazione poiché sarebbe troppo lento e lo scheduler deve operare in modo **incrementale**.
    
- I sistemi reali utilizzano tecniche (come il _Locking_) per garantire a priori la **conflict-serializzabilità** senza dover verificare cicli nel grafo o assumere l'ipotesi della commit-projection.