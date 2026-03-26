Questa seconda parte della lezione sul **Transaction Processing System (TPS)** approfondisce le regole tecniche per la gestione del log, le diverse strategie di aggiornamento del database e le procedure di ripristino (restart) in seguito a un guasto.

---

## 1. L'Irrevocabilità del Commit

Il destino di una transazione è deciso nel momento esatto in cui il record di **commit** viene scritto nel log in modo sincrono tramite una operazione di _force_.

- **Prima del commit:** Qualsiasi guasto porta all'annullamento (**undo**) di tutte le azioni per ripristinare lo stato originale.
    
- **Dopo il commit:** Lo stato finale deve essere garantito e, se necessario, ricostruito tramite una operazione di **redo**.
    
- Al contrario del commit, il record di **abort** può essere scritto in modo asincrono.
    

---

## 2. Regole Fondamentali del Log

Per garantire la riproducibilità delle operazioni, il sistema segue due regole ferree:

1. **Write-Ahead-Log (WAL):** La parte "Before-State" (BS) dei record deve essere scritta nel log _prima_ di eseguire l'operazione corrispondente nel database fisico. Questa regola permette l'**undo**.
    
2. **Commit-Precedence:** La parte "After-State" (AS) dei record deve essere scritta nel log _prima_ di completare il commit. Questa regola permette il **redo**.
    

---

## 3. Strategie di Aggiornamento (Writing Modes)

Esistono diversi modi in cui il DBMS può decidere di scrivere i dati dal buffer al disco fisso:

|**Modalità**|**Descrizione**|**Requisiti di Recovery**|
|---|---|---|
|**Immediate**|Il DB può contenere valori "After-State" di transazioni non ancora terminate.|Richiede **Undo**, ma non il Redo.|
|**Deferred**|Il DB non contiene mai valori di transazioni non confermate.|Richiede **Redo**, l'Undo è superfluo.|
|**Mixed**|La scrittura può avvenire sia in modo immediato che differito per ottimizzare le prestazioni.|Richiede sia **Undo** che **Redo**.|

---

## 4. Tipologie di Guasto e Procedure di Restart

Il sistema reagisce diversamente a seconda della gravità del malfunzionamento:

## Guasti "Soft" (Sistema/Software)

Causati da errori di programma, crash di sistema o cali di tensione.

- **Effetto:** La memoria principale (RAM) va perduta, ma la memoria secondaria (disco) rimane integra.
    
- **Soluzione:** **Warm Restart**.
    

## Guasti "Hard" (Hardware/Dispositivi)

Causati da danni fisici ai dischi di memoria secondaria.

- **Effetto:** Anche i dati su disco vanno perduti.
    
- **Soluzione:** **Cold Restart**. È necessario ripartire dall'ultimo **backup (dump)**, rieseguire le operazioni del log fino al momento del guasto e poi procedere con un warm restart.
    

---

## 5. Il Processo di Warm Restart

L'obiettivo è classificare le transazioni in base al loro stato al momento del crash e agire di conseguenza:

1. **Ricerca:** Si risale il log all'indietro fino all'ultimo **checkpoint**.
    
2. **Classificazione:** Si creano due insiemi: **UNDO** (transazioni attive al checkpoint o iniziate dopo, ma mai terminate) e **REDO** (transazioni che hanno effettuato il commit).
    
3. **Fase di UNDO:** Si scorre il log all'indietro annullando le azioni delle transazioni nell'insieme UNDO.
    
4. **Fase di REDO:** Si scorre il log in avanti ripetendo tutte le azioni delle transazioni nell'insieme REDO.

![[Pasted image 20260326120939.png|697]]


Ecco cosa succede se avviene un crash in punti diversi delle linee temporali:

## 1. Caso (a): Aggiornamento Immediato (Immediate Update)

In questa modalità, il database fisico viene aggiornato _prima_ del commit.

- **Crash prima del record "C"**: Poiché le operazioni di scrittura nel database ($w(x)$ e $w(y)$) sono già avvenute, il sistema deve eseguire un **UNDO**. Usando i valori "Before State" (BS) salvati nel log, il DBMS riporta gli oggetti $X$ e $Y$ al loro stato originale per garantire l'atomicità.
    
- **Crash dopo il record "C"**: La transazione è considerata completata con successo. Anche se avviene un crash un istante dopo, i dati sono già nel database. Tuttavia, per durabilità, il sistema potrebbe eseguire un **REDO** se il buffer non fosse stato ancora svuotato completamente sul disco.
    

## 2. Caso (b): Aggiornamento Differito (Deferred Update)

Qui il database viene aggiornato solo _dopo_ che il commit è stato registrato nel log.

- **Crash prima del record "C"**: Non occorre fare nulla (**Nothing**). Poiché nessuna operazione di scrittura ($w$) ha ancora toccato il database fisico, non ci sono modifiche da annullare.
    
- **Crash dopo il record "C" ma prima di $w(x)$ o $w(y)$**: Questo è il caso tipico che richiede un **REDO**. Il sistema vede che la transazione è "impegnata" (commit presente), ma i dati fisici non sono stati ancora scritti; quindi usa i valori "After State" (AS) del log per scrivere $X$ e $Y$ nel database.
    

## 3. Caso (c): Aggiornamento Misto (Mixed Update)

Questa è una via di mezzo usata per ottimizzare le prestazioni.

- **Crash tra $w(x)$ e il record "C"**: Il sistema deve eseguire l'**UNDO** solo per la parte di dati già scritta ($X$), mentre per $Y$ non serve fare nulla perché non era ancora stato toccato nel database.
    
- **Crash dopo il record "C" ma prima di $w(y)$**: Il sistema deve eseguire il **REDO** per l'operazione $w(y)$ che non è ancora avvenuta, assicurandosi che le modifiche di una transazione confermata non vadano perse.
___________________________________________________________________

## Riepilogo Regole d'Oro

Indipendentemente dalla linea temporale, il DBMS rispetta sempre queste due regole critiche:

1. **WAL (Write-Ahead-Log)**: Il record di log ($U$) deve sempre precedere la scrittura nel DB ($w$) per permettere l'UNDO.
    
2. **Commit-Precedence**: Tutti i record di log ($AS$) devono essere scritti prima di considerare valido il Commit, per permettere il REDO.



![[Pasted image 20260326123203.png]]
## La Sequenza del Log (Esempio delle Slide)

Immaginiamo che il Log contenga le seguenti operazioni cronologiche prima del crash:

1. $B(T1), B(T2)$ (Inizio transazioni)
2. $U(T2, ...), I(T1, ...)$ (Operazioni sui dati)
3. $B(T3), C(T1)$ (**Commit di T1**)
4. $B(T4), U(T3, ...), U(T4, ...)$
5. **$CK(T2, T3, T4)$ (Punto di Checkpoint)**: registra che $T2, T3$ e $T4$ sono attive.
6. $C(T4)$ (**Commit di T4**)
7. $B(T5), U(T3, ...), U(T5, ...), D(T3, ...)$
8. $A(T3)$ (**Abort di T3**)
9. $C(T5)$ (**Commit di T5**)
10. $I(T2, ...)$ (Ultima operazione di T2 prima del Crash)
11. **CRASH**.

---

## Fase 1: Ricerca dell'ultimo Checkpoint

Il sistema ripercorre il Log a ritroso partendo dalla fine per trovare l'ultimo checkpoint registrato. In questo caso, trova $CK(T2, T3, T4)$. Grazie al checkpoint, sappiamo che al momento della sua registrazione le transazioni $T2, T3$ e $T4$ erano in corso.

## Fase 2: Costruzione degli insiemi UNDO e REDO

Il sistema analizza il log dal checkpoint in avanti per classificare le transazioni:

- **Inizializzazione**: L'insieme **UNDO** contiene inizialmente le transazioni attive al checkpoint $\{T2, T3, T4\}$. Il **REDO** è vuoto.
    
- **Scansione in avanti**:
    
    - $C(T4)$: $T4$ ha completato il commit, quindi viene spostata da UNDO a **REDO**.
        
    - $B(T5)$: $T5$ inizia, viene aggiunta a **UNDO**.
        
    - $A(T3)$: $T3$ ha fallito (abort), quindi rimane in **UNDO** per essere annullata.
        
    - $C(T5)$: $T5$ ha completato il commit, quindi viene spostata da UNDO a **REDO**.
        
- **Risultato finale**:
    
    - **$UNDO = \{T2, T3\}$**: transazioni mai confermate o fallite.
        
    - **$REDO = \{T4, T5\}$**: transazioni confermate dopo il checkpoint.
        
    - Nota: T1 viene ignorata perché il commit è avvenuto prima del checkpoint, quindi i suoi dati sono già sicuri su disco.
        

---

## Fase 3: Esecuzione dell'UNDO (All'indietro)

Il sistema ripercorre il log all'indietro fino alla prima azione della transazione più vecchia in UNDO. Per ogni operazione di $T2$ e $T3$, scrive nel database il valore precedente (**Before State - BS**):

1. **Undo $I(T2, O6)$**: cancella l'oggetto $O6$ inserito.
    
2. **Undo $D(T3, O5)$**: ripristina $O5$ con il valore $B7$.
    
3. **Undo $U(T3, O3)$**: ripristina $O3$ con il valore $B5$.
    
4. **Undo $U(T3, O2)$**: ripristina $O2$ con il valore $B3$.
    
5. **Undo $U(T2, O1)$**: ripristina $O1$ con il valore $B1$.
    

## Fase 4: Esecuzione del REDO (In avanti)

Infine, il sistema ripercorre il log in avanti e ripete tutte le azioni delle transazioni in REDO per assicurarsi che siano scritte permanentemente. Scrive nel database il valore finale (**After State - AS**):

1. **Redo $U(T4, O3)$**: imposta $O3$ al valore $A4$.
    
2. **Redo $U(T5, O4)$**: imposta $O4$ al valore $A6$.
    

Al termine di queste fasi, il database è di nuovo in uno stato **coerente**.


![[Pasted image 20260326123351.png]]