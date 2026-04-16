## 1. Fasi della Progettazione di un Database

La progettazione di un database segue un flusso rigoroso per passare dai requisiti al sistema finale.

- La progettazione passa attraverso tre stadi sequenziali: la progettazione concettuale (che produce lo Schema E-R), la progettazione logica (Schema Logico) e la progettazione fisica (Schema Fisico).
- Per poter tradurre lo schema E-R iniziale nello schema logico a tabelle, è obbligatorio passare attraverso una fase intermedia chiamata "ristrutturazione dello schema E-R".
- Le attività di ristrutturazione comprendono: l'analisi delle ridondanze, l'eliminazione delle generalizzazioni (come le gerarchie), il partizionamento o la fusione di entità e relazioni, e la selezione finale degli identificatori primari.

---

## 2. Esercizio 1: Schema E-R per una Clinica Veterinaria

**Obiettivo del sistema:** Registrare i dati dei pazienti animali, dei proprietari e tenere traccia dello storico delle visite e delle procedure effettuate.

**Entità e Attributi:**

- **Owner (Proprietario):** Identificato univocamente dal Codice Fiscale, include Nome e Cognome.
    
- **Animal (Animale):** Identificato da un ID univoco, con Nome, Data di nascita e Genere.
    
- **Type (Tipo) e Breed (Razza):** Permettono di classificare l'animale specificandone la specie (es. cane, gatto) e la razza esatta.
    
- **Visit (Visita) e Procedure (Procedura):** La visita ha un ID, una Data e uno spazio per le Note; la procedura è identificata dal suo Nome (es. chirurgia, vaccinazione).
    

**Relazioni Principali (Cardinalità):**

- **Owner - Animal (1:N):** Un singolo proprietario può possedere uno o più animali.
    
- **Breed - Animal (1:N):** Molteplici animali possono appartenere alla medesima razza.
    
- **Type - Breed (1:N):** Un tipo generico include diverse razze, ma ogni singola razza appartiene esclusivamente a un tipo.
    
- **Animal - Visit (1:N):** Lo stesso animale può essere visitato più volte nel tempo.
    
- **Visit - Procedure (1:N):** Durante un'unica visita clinica è possibile eseguire molteplici procedure mediche.
    

---

## 3. Esercizio 2: Schema E-R per un Reparto Ospedaliero

**Contesto:** Gestione dei dati per pazienti, ricoveri ospedalieri, medici e visite.

- I pazienti sono identificati dal codice fiscale e memorizzano nome, cognome e data di nascita.
    
- I medici presentano una matricola (ID), cognome, nome e data di laurea.
    
- Le visite, identificate univocamente dalla combinazione di paziente, data e ora, collegano i medici visitanti, le malattie diagnosticate (con ID e nome) e i farmaci prescritti.
    
- Per i farmaci prescritti, è necessario registrare anche il dosaggio sull'associazione, oltre agli attributi dell'entità farmaco che sono ID, nome e costo.
    

**Generalizzazione dell'entità Ricovero (Hospitalization):** Ogni ricovero è identificato dalla data di inizio per lo specifico paziente ed è supervisionato da un medico curante. Viene suddiviso in due sotto-entità:

- **Completed (Completati):** Memorizzano la data di dimissione/fine e la motivazione (es. dimissione, trasferimento).
    
- **Ongoing (In corso):** Memorizzano i contatti (una stringa) di un parente di riferimento in caso di necessità.
    

---

## 4. Passaggio al Modello Logico (Caso Clinica Veterinaria)

Dopo aver completato le fasi di ristrutturazione dello schema concettuale, si applicano le regole standard di traduzione per ottenere lo schema a tabelle.

**Regole di traduzione standard:**

- Per ogni entità si definisce una relazione (tabella) con lo stesso nome, mantenendo come colonne gli attributi originali e usando l'identificatore come chiave primaria.
    
- Per ogni associazione si definisce una tabella che raggruppa gli attributi dell'associazione stessa più gli identificatori delle entità coinvolte, i quali insieme formano la chiave della nuova tabella.
    

**Risultato - Schema Logico della Clinica Veterinaria:**

- `OWNER (CF, LastName, FirstName)`
    
- `TYPE (Type)`
    
- `BREED (Breed, Type)`
    
- `ANIMAL (AnimalID, Name, BirthDate, Gender, Breed, Owner)`
    
- `VISIT (VisitID, VisitDate, Notes, Animal)`
    
- `PROCEDURE (Procedure Type)`
    
- `VISIT_PROCEDURES (VisitID, Procedure)`
    

---

## 5. Esercizio Assegnato (Homework): Vendita all'ingrosso di piante

L'obiettivo è creare lo schema E-R per un database che deve gestire la vendita all'ingrosso, modellando le seguenti regole aziendali:

- **Piante:** Sono suddivise in specie, di cui si conosce il nome latino, il nome comune, un codice univoco, l'informazione se sono da interno o giardino, e se sono esotiche o meno. Le piante possono essere sempreverdi o da fiore; per quest'ultime vanno registrati tutti i colori dei fiori disponibili per la specie.
    
- **Clienti:** Identificati da un codice cliente, si dividono in due categorie (Generalizzazione): Privati (di cui si memorizza codice fiscale, nome, indirizzo) e Rivenditori (di cui si memorizza la Partita IVA, il nome e l'indirizzo del negozio).
    
- **Fornitori:** Sono identificati da un codice e presentano nome, codice fiscale (o P.IVA) e indirizzo.
    
- **Regola di fornitura:** Un fornitore può fornire svariate specie di piante, ma tutte le piante appartenenti alla medesima specie devono essere acquistate sempre da un unico fornitore.
    
- **Acquisti e Prezzi:** Il sistema deve tracciare tutti gli acquisti effettuati dai clienti, registrando per ogni acquisto la data, la quantità e la specie richiesta. Inoltre, deve essere mantenuto uno storico (listino prezzi) per tracciare la variazione di prezzo di ogni specie di pianta nel corso del tempo.