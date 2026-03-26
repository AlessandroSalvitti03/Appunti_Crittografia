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