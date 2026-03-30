## 1. Fase di Raccolta 

Non puoi studiare bene se le fonti sono disordinate. Dobbiamo "digitalizzare" la conoscenza:

- **Video-to-Text:** Se hai i video originali, non perdere tempo a trascriverli a mano. Puoi caricarli direttamente su **Gemini** (se il file non è troppo pesante) o usare tool come **Whisper (OpenAI)** per una trascrizione impeccabile.
    
    - _Consiglio:_ Chiedi a Gemini: _"Riassumi questa trascrizione mantenendo i termini tecnici e creando una lista di concetti chiave."_
        
- **Correlazione Excel-Libro:** Prendi le pagine del libro indicate dal prof e, se hai il PDF, estrai solo quelle sezioni. Caricarle "sfuse" aiuterà l'IA a non perdersi in capitoli inutili.
    

---

## 2. Fase di Elaborazione: NotebookLM 

**NotebookLM** è lo strumento definitivo per questo compito perché lavora in "closed loop" (non inventa cose, usa solo le tue fonti).

1. Crea un Notebook per ogni macro-argomento (es. "Distributed Systems", "Parallel Computing").
    
2. Carica come fonti: **Slide del prof**, **Trascrizione della lezione** e **Pagine del libro**.
    
3. **Cosa chiedere:** _"Metti a confronto la spiegazione della slide X con l'approfondimento a pagina Y del libro. Crea una sintesi che spieghi il concetto come se dovessi applicarlo a un esercizio d'esame."_
    

---

## 3. Fase di Sintesi: Gemini

Mentre NotebookLM organizza i dati, usa **Gemini** per la parte attiva e il "Problem Solving" richiesto dall'esame:

- **Creazione degli Schemi Decisionali:** Dato che l'esame è open-book, non ti servono riassunti discorsivi, ma **tabelle di consultazione rapida**.
    
- **Esempio di Prompt:** _"Basandoti su questo argomento, crea una tabella comparativa tra la Tecnologia A e la Tecnologia B, evidenziando i Trade-off (Pro/Contro) e i casi d'uso tipici. Formatta in modo che sia leggibile a colpo d'occhio durante lo scritto."_
    

---

