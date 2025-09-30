# 5. Dare una Memoria all'IA: Gestione della Memoria Conversazionale

Un'interazione umana naturale si basa sulla memoria. Ricordiamo cosa è stato detto pochi istanti fa e colleghiamo le conversazioni attuali a esperienze passate. Per un LLM, che è intrinsecamente "senza stato" (stateless), questa capacità non è automatica. Ogni interazione, in isolamento, è un nuovo inizio.

La gestione della memoria conversazionale è la branca del Context Engineering che si occupa di dare a un LLM la capacità di ricordare, creando conversazioni coerenti, contestualizzate e personalizzate. In questo capitolo, distingueremo tra memoria a breve e a lungo termine, analizzando le tecniche per implementarle.

---

### Memoria a Breve Termine: Ricordare la Conversazione Attuale

La memoria a breve termine si occupa di mantenere la coerenza all'interno di una singola sessione di conversazione. L'obiettivo è gestire la cronologia del dialogo in modo che non superi la finestra di contesto dell'LLM, pur conservando le informazioni più importanti.

#### Tecnica 1: Sliding Window (Finestra Scorrevole)

È l'approccio più semplice. Mantiene in memoria solo le ultime `K` interazioni (turni di conversazione).

- **Come Funziona:** Man mano che la conversazione procede, i messaggi più vecchi vengono scartati per far posto a quelli nuovi, come una finestra che scorre lungo la cronologia.
- **Vantaggi:** Semplice da implementare e previene in modo affidabile l'overflow del contesto.
- **Svantaggi:** Informazioni importanti dette all'inizio della conversazione vengono perse irrimediabilmente, anche se fossero cruciali per la domanda attuale.

#### Tecnica 2: Summarization (Riassunto)

Invece di scartare i vecchi messaggi, questa tecnica utilizza un LLM per riassumerli.

- **Come Funziona:** Esistono due varianti principali:
  1.  **Riassunto Progressivo:** L'LLM aggiorna continuamente un riassunto della conversazione dopo ogni interazione.
  2.  **Riassunto a Buffer:** Si tiene una finestra delle ultime `K` interazioni in forma completa (come nella Sliding Window). Quando un messaggio sta per essere scartato, viene invece riassunto e aggiunto a un riassunto generale della conversazione passata. Il contesto finale sarà composto dal riassunto e dalle ultime `K` interazioni.
- **Vantaggi:** Permette di conservare l'essenza di conversazioni molto lunghe in un numero ridotto di token.
- **Svantaggi:** Il processo di riassunto può introdurre latenza e costi aggiuntivi. Inoltre, dettagli specifici e sfumature potrebbero andare persi nel riassunto.

---

### Memoria a Lungo Termine: Ricordare tra le Sessioni

La memoria a lungo termine è ciò che permette a un'applicazione AI di essere veramente personalizzata. Consente all'IA di ricordare informazioni, preferenze e interazioni passate attraverso sessioni diverse, giorni o addirittura mesi dopo.

L'architettura per la memoria a lungo termine sfrutta le stesse tecnologie del RAG: **embeddings e vector database**.

- **Come Funziona:**

  1.  **Estrazione e Archiviazione:** Durante una conversazione, un processo in background (spesso un altro LLM) identifica informazioni importanti da ricordare a lungo termine. Queste possono essere:
      - **Fatti Espliciti:** "Il mio nome è Luca."
      - **Preferenze Utente:** "Preferisco risposte concise e in formato di elenco puntato."
      - **Interazioni Passate:** Il riassunto di una conversazione su un problema tecnico risolto la settimana prima.
  2.  **Creazione degli Embeddings:** Queste informazioni vengono trasformate in vettori semantici (embeddings).
  3.  **Archiviazione nel Vector Store:** I vettori vengono salvati in un vector database, spesso associati a un `user_id` specifico per distinguere le memorie di utenti diversi.

- **Recupero e Utilizzo:**

  1.  **Arricchimento del Contesto:** All'inizio di una nuova conversazione (o a ogni turno), la query corrente dell'utente viene usata per interrogare il vector database della memoria a lungo termine.
  2.  **Recupero della Memoria Rilevante:** Il sistema recupera i ricordi (fatti, preferenze) semanticamente più rilevanti per la conversazione attuale.
  3.  **Iniezione nel Contesto:** Questi ricordi recuperati vengono inseriti nel contesto dell'LLM, proprio come i dati recuperati da un RAG. Il prompt finale potrebbe assomigliare a questo:
      > **Istruzioni di Sistema:** "Sei un assistente AI. Rispondi all'utente in modo conciso e con elenchi puntati."
      > **Memoria Recuperata:** "[L'utente si chiama Luca. In una conversazione precedente, ha avuto problemi con la sincronizzazione dei file.]"
      > **Cronologia Recente:** "..."
      > **Domanda Utente:** "Ho di nuovo quel problema."

- **Vantaggi:**
  - **Personalizzazione Profonda:** L'IA può chiamare l'utente per nome, ricordare le sue preferenze e il contesto di interazioni passate.
  - **Coerenza nel Tempo:** Evita che l'utente debba ripetere le stesse informazioni in ogni sessione.
  - **Scalabilità:** Un vector database può gestire i ricordi di milioni di utenti in modo efficiente.

La combinazione di una gestione intelligente della memoria a breve termine e di un'architettura robusta per la memoria a lungo termine è ciò che eleva un chatbot da un semplice risponditore di domande a un vero e proprio assistente personalizzato e consapevole.

---

[< Indietro (Tecniche RAG Avanzate)](./04-tecniche-rag-avanzate.md) | [Torna all'Indice](./index.md) | [Avanti (Tool Use) >](./06-tool-use-e-function-calling.md)
