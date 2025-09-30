# 6. Estendere le Capacità con Tool Use e Function Calling

Fino ad ora, abbiamo visto come fornire a un LLM conoscenza e memoria. Ma cosa succederebbe se potesse anche _agire_? Se potesse cercare informazioni sul web in tempo reale, inviare un'email, interrogare un database o eseguire un comando nel terminale?

Questa è la promessa del **Tool Use**, noto anche come **Function Calling**. È la tecnologia che trasforma un LLM da un "oracolo passivo" a un "agente attivo", capace di interagire con il mondo esterno per portare a termine compiti complessi. È uno dei pilastri più avanzati e potenti del Context Engineering.

---

### Il Principio di Funzionamento: Delegare l'Azione

È fondamentale capire un punto chiave: **l'LLM non esegue mai direttamente il codice dello strumento**. Per motivi di sicurezza e architettura, il suo ruolo è quello di un "direttore d'orchestra intelligente".

Il processo si svolge in questo modo:

1.  **Definizione degli Strumenti:** Lo sviluppatore fornisce all'LLM una lista di strumenti disponibili. Ogni strumento è descritto in modo dettagliato, includendo:

    - Un nome (es. `cerca_voli`).
    - Una descrizione chiara del suo scopo (es. "Cerca i voli disponibili tra due città in una data specifica").
    - Uno schema dei parametri richiesti, spesso in formato **JSON Schema** (es. `partenza: string`, `destinazione: string`, `data: string`).

2.  **Decisione dell'LLM:** Quando l'utente fa una richiesta, l'LLM analizza il testo e decide se per rispondere è necessario uno degli strumenti a sua disposizione.

3.  **Generazione della Chiamata:** Se decide di usare uno strumento, l'LLM non lo esegue, ma genera un output strutturato (solitamente un oggetto JSON) che contiene il nome della funzione da chiamare e gli argomenti estratti dalla richiesta dell'utente.

4.  **Esecuzione da Parte dell'Applicazione:** L'applicazione che ospita l'LLM (es. un server, un'app, o Gemini CLI stesso) riceve questo oggetto JSON, lo interpreta ed esegue la funzione corrispondente nel proprio ambiente di codice.

5.  **Restituzione del Risultato:** L'output della funzione (es. la lista dei voli trovati, la conferma dell'invio di un'email) viene restituito all'LLM come una nuova informazione nel contesto.

6.  **Generazione della Risposta Finale:** L'LLM utilizza il risultato dello strumento per formulare una risposta finale, completa e contestualizzata per l'utente.

---

### Il Ciclo ReAct: Ragionare e Agire

Questo processo di decisione-azione-osservazione è spesso formalizzato nel framework **ReAct (Reason and Act)**. Invece di generare subito una chiamata a uno strumento, l'LLM esplicita il suo processo di pensiero, creando un ciclo virtuoso:

- **Thought (Pensiero):** L'LLM genera un pensiero interno. _"L'utente vuole sapere il tempo a Roma. Non ho accesso a informazioni in tempo reale. Devo usare lo strumento `get_current_weather`."_
- **Act (Azione):** L'LLM genera la chiamata allo strumento. `get_current_weather(location="Roma, IT")`.
- **Observation (Osservazione):** L'applicazione esegue lo strumento e restituisce il risultato. _"Il tempo a Roma è: 24°C, soleggiato."_
- **Thought (Pensiero):** L'LLM riceve l'osservazione. _"Ho l'informazione richiesta. Ora posso formulare una risposta per l'utente."_
- **Risposta Finale:** L'LLM genera la risposta in linguaggio naturale: "Attualmente a Roma ci sono 24°C e il tempo è soleggiato."

Questo ciclo permette all'LLM di affrontare problemi complessi, concatenare più strumenti e persino correggere i propri errori se un'azione fallisce.

---

### Esempio Pratico: Definire uno Strumento per Gemini CLI

Immaginiamo di voler dare a un assistente come Gemini CLI la capacità di leggere un file. Gemini CLI ha già questo strumento, ma vediamo come potremmo definirlo concettualmente usando JSON Schema.

**Definizione dello Strumento `read_file`:**

```json
{
  "name": "read_file",
  "description": "Legge e restituisce il contenuto di un file specificato tramite il suo percorso assoluto.",
  "parameters": {
    "type": "object",
    "properties": {
      "absolute_path": {
        "type": "string",
        "description": "Il percorso assoluto del file da leggere (es. '/home/user/project/file.txt')."
      }
    },
    "required": ["absolute_path"]
  }
}
```

**Interazione con l'Utente:**

> **Utente:** "Mostrami il contenuto del file `src/config.js`"

\*\*Processo Interno di Gemini CLI (basato su ReAct):

1.  **Thought:** L'utente vuole vedere il contenuto di un file. Il percorso fornito è relativo. Devo prima ottenere il percorso assoluto del progetto e poi usare lo strumento `read_file`.
2.  **Act (interno):** Esegue un'azione interna per ottenere il CWD (Current Working Directory), es. `/Users/luca/mio-progetto`.
3.  **Observation (interna):** Ottiene il percorso `/Users/luca/mio-progetto`.
4.  **Thought:** Ora posso costruire il percorso assoluto: `/Users/luca/mio-progetto/src/config.js`. Posso usare lo strumento `read_file`.
5.  **Act (chiamata allo strumento):** Genera la chiamata: `read_file(absolute_path="/Users/luca/mio-progetto/src/config.js")`.
6.  **Observation:** L'ambiente di Gemini CLI esegue la lettura del file e restituisce il suo contenuto: `"const API_KEY = '...';"`.
7.  **Thought:** Ho il contenuto del file. Posso mostrarlo all'utente.
8.  **Risposta Finale:** "Ecco il contenuto di `src/config.js`:
    ````javascript
    const API_KEY = '...';
    ```"
    ````

Questo esempio dimostra come il Tool Use permetta a un'applicazione AI di interagire in modo sicuro e strutturato con il sistema sottostante, trasformando richieste in linguaggio naturale in azioni concrete. È importante notare che il processo descritto è interno alla CLI e non un comando shell che l'utente digita direttamente.

---

[< Indietro (Gestione della Memoria)](./05-gestione-della-memoria-conversazionale.md) | [Torna all'Indice](./index.md) | [Avanti (Metodologie SDLC) >](./07-prompt-spec-prd-driven-development.md)
