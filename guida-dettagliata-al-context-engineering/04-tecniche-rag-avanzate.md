# 4. Tecniche RAG Avanzate

Il sistema RAG di base che abbiamo visto nel capitolo precedente è incredibilmente potente, ma non è una soluzione universale. A volte, la qualità dei risultati di ricerca può essere migliorata. Le domande degli utenti possono essere ambigue, i documenti possono contenere terminologia specifica, o i risultati più rilevanti potrebbero non essere i primi a essere recuperati.

Per affrontare queste sfide, sono state sviluppate tecniche RAG avanzate che raffinano il processo di recupero, garantendo che il contesto fornito all'LLM sia il più preciso e pertinente possibile. In questo capitolo, esploreremo tre delle più importanti: Hybrid Search, Re-ranking e Query Transformation.

---

### 1. Hybrid Search: Il Meglio di Due Mondi

La ricerca semantica (basata su vettori) è eccellente per comprendere l'intento e i concetti, ma può fallire nel recuperare termini specifici, acronimi o codici prodotto. D'altra parte, la ricerca tradizionale per parole chiave (keyword search, come l'algoritmo BM25) eccelle in questo, ma non comprende i sinonimi o il contesto.

L'**Hybrid Search** combina queste due metodologie per ottenere risultati superiori.

- **Come Funziona:**

  1.  **Doppia Ricerca:** Quando un utente invia una query, il sistema la esegue in parallelo su due indici: un indice vettoriale per la ricerca semantica e un indice a testo pieno (come Elasticsearch o OpenSearch) per la ricerca per parole chiave.
  2.  **Fusione dei Risultati (Score Fusion):** I risultati di entrambe le ricerche vengono raccolti. Un algoritmo di fusione, come il **Reciprocal Rank Fusion (RRF)**, combina i punteggi di entrambi i sistemi per creare una classifica finale unificata. L'RRF è particolarmente efficace perché valuta i risultati in base alla loro posizione nelle rispettive classifiche, piuttosto che ai loro punteggi assoluti, che possono essere difficili da confrontare.

- **Perché è Utile:** Unisce la precisione della ricerca per parole chiave con la comprensione concettuale della ricerca semantica. Se un utente cerca "laptop per gaming con GPU RTX 4080", l'hybrid search può trovare documenti che parlano di "portatili da gioco" (concetto semantico) e che contengono la sigla esatta "RTX 4080" (parola chiave).

---

### 2. Re-ranking: Un Secondo Paio d'Occhi Intelligente

La prima fase di recupero (sia essa semantica o ibrida) è ottimizzata per la velocità su milioni di documenti. Questo può portare al recupero di un set di risultati abbastanza ampio (es. i primi 100 chunk) che contiene informazioni pertinenti ma anche molto "rumore".

Il **Re-ranking** (o ri-classificazione) introduce un secondo stadio per affinare questi risultati.

- **Come Funziona:**

  1.  **Recupero Iniziale:** Un sistema veloce recupera un ampio set di documenti candidati (es. 100-200).
  2.  **Fase di Re-ranking:** Questi documenti candidati, insieme alla query originale, vengono passati a un modello più sofisticato e computazionalmente più costoso (spesso un modello cross-encoder). Questo modello non si limita a confrontare i vettori, ma analizza la query e il contenuto di ogni singolo documento per calcolare un punteggio di rilevanza molto più accurato.
  3.  **Selezione Finale:** Solo i documenti con il punteggio più alto dopo il re-ranking (es. i primi 3-5) vengono selezionati e inseriti nel contesto finale dell'LLM.

- **Perché è Utile:** Migliora drasticamente la precisione del contesto finale. Filtra i falsi positivi e assicura che i chunk più pertinenti siano prioritari, permettendo all'LLM di generare una risposta più focalizzata e corretta. È un compromesso tra velocità e accuratezza: una ricerca iniziale veloce e una successiva analisi più lenta ma molto più precisa su un set ridotto di candidati.

---

### 3. Query Transformation: Riscrivere la Domanda per Capirla Meglio

Spesso, il modo in cui un utente pone una domanda non è il modo migliore per interrogare una knowledge base. Le domande possono essere troppo brevi, ambigue o usare un linguaggio diverso da quello dei documenti.

La **Query Transformation** utilizza un LLM per "riscrivere" la domanda dell'utente prima che inizi il processo di recupero.

- **Come Funziona:** La query originale viene data in input a un LLM con un prompt specifico che gli chiede di migliorarla. Esistono diverse tecniche:

  1.  **Query Expansion:** Si espande la query aggiungendo sinonimi o termini correlati. (es. "problemi di login" -> "problemi di login, accesso non riuscito, password dimenticata, errore di autenticazione").
  2.  **Decomposizione (Sub-queries):** Una domanda complessa viene scomposta in più domande semplici. (es. "Confronta i vantaggi di RAG e fine-tuning" -> 1. "Quali sono i vantaggi di RAG?" 2. "Quali sono i vantaggi del fine-tuning?"). I risultati di ogni sub-query vengono poi combinati.
  3.  **Step-Back Prompting:** Si genera una domanda più generale per recuperare un contesto di alto livello. (es. "Quali sono le implicazioni della sentenza Rossi vs. Verdi del 2023?" -> "Quali sono i principi legali generali riguardanti le dispute contrattuali?").
  4.  **Hypothetical Document Embeddings (HyDE):** L'LLM genera una risposta "ipotetica" alla domanda. Questa risposta fittizia, essendo un paragrafo ben scritto, ha un embedding molto robusto che viene poi usato per recuperare documenti reali simili a essa.

- **Perché è Utile:** Supera il "vocabulary mismatch" tra l'utente e i documenti. Permette di affrontare domande complesse e migliora significativamente la probabilità di trovare i chunk di informazione più rilevanti.

L'integrazione di queste tecniche trasforma un sistema RAG di base in un'architettura avanzata, robusta e molto più performante, capace di gestire una gamma più ampia di query e domini di conoscenza.

---

[< Indietro (RAG)](./03-retrieval-augmented-generation-rag.md) | [Torna all'Indice](./index.md) | [Avanti (Gestione della Memoria) >](./05-gestione-della-memoria-conversazionale.md)
