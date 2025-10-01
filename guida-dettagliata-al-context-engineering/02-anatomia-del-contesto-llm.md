# 2. Anatomia del Contesto di un LLM

Per praticare efficacemente il Context Engineering, è essenziale capire cosa "vede" un LLM in un dato momento. L'insieme di tutte le informazioni fornite al modello prima che generi una risposta è chiamato **contesto**. Questo contesto non è un blocco monolitico di testo, ma un assemblaggio strutturato di diversi componenti, ognuno con un ruolo specifico.

Possiamo pensare alla finestra di contesto di un LLM come alla sua **memoria di lavoro (working memory)**: volatile, di dimensioni finite, ma incredibilmente potente se gestita correttamente.

In questo capitolo, sezioneremo i componenti principali che formano questa memoria di lavoro.

---

### I 5 Componenti Chiave del Contesto

Un contesto ben strutturato è tipicamente composto da una combinazione dei seguenti elementi, spesso assemblati in un unico grande blocco di testo prima di essere inviati al modello.

#### 1. Istruzioni di Sistema (System Prompt)

È la direttiva di più alto livello. Definisce il ruolo, la personalità, i vincoli e l'obiettivo generale dell'IA per l'intera durata dell'interazione. Viene posta all'inizio del contesto e agisce come una "costituzione" che il modello deve seguire.

- **Scopo:** Impostare il comportamento di base e il tono.
- **Esempio (per un chatbot di supporto):**
  > "Sei un assistente di supporto tecnico per il prodotto 'SyncMaster Pro'. Il tuo obiettivo è aiutare gli utenti a risolvere i loro problemi in modo chiaro, amichevole e conciso. Non devi mai suggerire prodotti della concorrenza. Rispondi sempre in italiano."

#### 2. Cronologia della Conversazione (Conversation History)

Questo componente permette all'IA di essere "stateful", ovvero di ricordare cosa è stato detto in precedenza nella stessa sessione. Senza di essa, ogni turno di conversazione sarebbe isolato e l'IA non potrebbe seguire un dialogo coerente.

- **Scopo:** Mantenere la coerenza e il filo del discorso.
- **Esempio (struttura tipica):**
  ```
  Human: Non riesco a sincronizzare i miei file.
  AI: Capisco. Ricevi un messaggio di errore specifico?
  Human: Sì, dice "Errore di autenticazione".
  ```

#### 3. Dati Recuperati (Retrieved Data - RAG)

Questo è il cuore del Context Engineering. Poiché la conoscenza dell'LLM è statica, questo componente serve a iniettare dinamicamente informazioni esterne e pertinenti nel contesto. Questi dati vengono solitamente recuperati da una knowledge base (documentazione, appunti, etc.) tramite tecniche di **Retrieval-Augmented Generation (RAG)**.

- **Scopo:** Ancorare ("grounding") la risposta a fatti reali, aggiornati o specifici del dominio, riducendo le allucinazioni.
- **Esempio (recuperato da una documentazione tecnica):**
  > "**Documento 3, Sezione 4.1:** Per risolvere l''Errore di autenticazione' in SyncMaster Pro, l'utente deve navigare in Impostazioni > Account e cliccare su 'Rinnova Token'. Questo forza una nuova autenticazione con il server."

#### 4. Strumenti Disponibili (Tool Use / Function Calling)

Per superare il limite della conoscenza statica e della passività, possiamo dare all'LLM accesso a strumenti esterni (API, funzioni, calcolatrici). Questo componente del contesto non contiene dati, ma **definizioni** degli strumenti che il modello può decidere di utilizzare. L'LLM, basandosi su queste definizioni e sull'input dell'utente, **genera una chiamata allo strumento** (es. in formato JSON). È poi l'applicazione esterna (o l'agente AI che ospita l'LLM, come la Gemini CLI) a **eseguire effettivamente lo strumento** e a restituire il risultato all'LLM come nuova informazione nel contesto.

- **Scopo:** Permettere all'IA di compiere azioni, ottenere informazioni in tempo reale o eseguire calcoli complessi.
- **Esempio (definizione di uno strumento in formato JSON Schema):**
  ```json
  {
    "name": "get_current_weather",
    "description": "Ottiene le condizioni meteo attuali per una data località",
    "parameters": {
      "type": "object",
      "properties": {
        "location": {
          "type": "string",
          "description": "La città e la provincia, es. Milano, MI"
        }
      },
      "required": ["location"]
    }
  }
  ```

#### 5. Input dell'Utente (User Input / Query)

Infine, c'è la domanda o l'istruzione più recente dell'utente. È il componente che innesca la generazione della risposta. Viene tipicamente posto alla fine del contesto assemblato.

- **Scopo:** Fornire il task specifico che l'LLM deve eseguire in quel momento.
- **Esempio:**
  > "Ok, ho capito. Ma prima di farlo, potresti dirmi che tempo fa a Roma?"

---

### La Finestra di Contesto: Qualità e Rilevanza vs. Quantità

Tutti questi componenti vengono combinati e inseriti nella finestra di contesto del modello. Tuttavia, questa finestra ha una dimensione finita. L'errore più comune è pensare che "più contesto è meglio".

La ricerca e la pratica dimostrano il contrario. Un contesto troppo lungo e pieno di informazioni irrilevanti (rumore) può:

- **Confondere il modello:** Un fenomeno noto come "lost-in-the-middle" mostra che gli LLM tendono a prestare più attenzione alle informazioni all'inizio e alla fine del contesto, ignorando quelle nel mezzo.
- **Aumentare latenza e costi:** Più token vengono processati, più lenta e costosa è la risposta.
- **Degradare le performance:** Un contesto "gonfio" può portare a risposte meno precise.

L'obiettivo del Context Engineering non è riempire la finestra di contesto, ma **ottimizzarla**, assicurandosi che contenga solo le informazioni più rilevanti e di alta qualità necessarie per il task specifico. È un continuo bilanciamento tra fornire abbastanza informazioni per una risposta accurata e rimuovere tutto ciò che è superfluo.

---

[< Indietro (Introduzione)](./01-introduzione-al-context-engineering.md) | [Torna all'Indice](./index.md) | [Avanti (RAG) >](./03-retrieval-augmented-generation-rag.md)
