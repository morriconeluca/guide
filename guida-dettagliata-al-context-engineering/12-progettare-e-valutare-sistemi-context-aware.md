# 12. Progettare e Valutare Sistemi Context-Aware

Abbiamo esplorato i componenti, le tecniche e le architetture dei sistemi di Context Engineering. Ma come si progetta un sistema del genere da zero? E, una volta costruito, come facciamo a sapere se funziona bene? La natura probabilistica degli LLM rende il testing e la valutazione più complessi rispetto al software tradizionale.

In questo capitolo, delineeremo un processo di progettazione end-to-end e introdurremo le metriche fondamentali per valutare in modo oggettivo le performance dei nostri sistemi.

---

### Progettare un Sistema Context-Aware: Un Processo Iterativo

Lo sviluppo di un'applicazione basata su LLM è un processo intrinsecamente iterativo. Ecco un approccio metodologico:

1.  **Definizione del Problema e del Contesto:**

    - Qual è il compito principale che l'IA deve svolgere?
    - Quali informazioni (contesto) sono necessarie per svolgere questo compito? (Es. documenti tecnici, cronologia degli ordini di un utente, policy aziendali).

2.  **Prototipo Iniziale (Baseline):**

    - Inizia nel modo più semplice possibile. Spesso, questo significa costruire un sistema RAG di base che recupera informazioni da un piccolo set di documenti.
    - L'obiettivo è avere un primo prototipo funzionante per capire i limiti e le sfide principali.

3.  **Costruzione della Pipeline di Dati:**

    - Sviluppa pipeline robuste per l'ingestione, la pulizia, il "chunking" e la creazione degli embedding dei dati che alimenteranno il tuo sistema RAG.

4.  **Integrazione e Orchestrazione:**

    - Sviluppa il servizio di orchestrazione che implementa la logica principale (es. il ciclo ReAct), integrando i vari componenti: RAG, memoria, strumenti.

5.  **Valutazione e Iterazione:**
    - Valuta sistematicamente il prototipo usando le metriche che vedremo di seguito. I risultati della valutazione guideranno l'iterazione successiva: forse il "chunking" non è ottimale, forse serve una tecnica RAG più avanzata come il re-ranking, o forse il prompt di sistema deve essere migliorato.

---

### Valutare un Sistema RAG: Le Metriche Chiave

Valutare un sistema RAG significa misurare la qualità di due cose distinte: la fase di **recupero (retrieval)** e la fase di **generazione (generation)**. Una risposta scadente può derivare da un contesto recuperato male o da un cattivo utilizzo di un buon contesto da parte dell'LLM.

Ecco le quattro metriche fondamentali, spesso utilizzate da framework di valutazione come **RAGAs (Retrieval-Augmented Generation Assessment)**.

#### Metriche per il Recupero

1.  **Context Precision (Precisione del Contesto):**

    - **Domanda a cui risponde:** "Le informazioni recuperate sono pertinenti e prive di rumore?"
    - **Come si misura:** Si analizza il set di documenti recuperati e si calcola il rapporto tra documenti rilevanti e il numero totale di documenti recuperati. Un punteggio basso indica che il sistema sta recuperando molto "rumore" (informazioni irrilevanti) che può confondere l'LLM.

2.  **Context Recall (Richiamo del Contesto):**
    - **Domanda a cui risponde:** "Abbiamo recuperato _tutte_ le informazioni necessarie per rispondere alla domanda?"
    - **Come si misura:** Questa metrica confronta il testo nei documenti recuperati con una "risposta di riferimento" (ground truth) e verifica se tutte le informazioni necessarie per formulare quella risposta erano presenti nel contesto recuperato. Un punteggio basso indica che il sistema di recupero non sta trovando dei pezzi di informazione cruciali.

#### Metriche per la Generazione

3.  **Faithfulness (Fedeltà):**

    - **Domanda a cui risponde:** "La risposta generata si basa _esclusivamente_ sulle informazioni fornite nel contesto?"
    - **Come si misura:** Si analizza la risposta generata frase per frase e si verifica se ogni affermazione può essere supportata dal contesto recuperato. Questa metrica è fondamentale per misurare e ridurre le **allucinazioni**. Un punteggio basso significa che l'LLM sta inventando informazioni o sta usando la sua conoscenza interna invece di quella fornita.

4.  **Answer Relevance (Rilevanza della Risposta):**
    - **Domanda a cui risponde:** "La risposta generata risponde effettivamente alla domanda originale dell'utente?"
    - **Come si misura:** Si valuta la pertinenza della risposta rispetto alla domanda iniziale. A volte un'IA può dare una risposta fattualmente corretta e fedele al contesto, ma che non risponde direttamente a ciò che l'utente ha chiesto. Un punteggio basso qui indica che l'LLM potrebbe aver frainteso l'intento dell'utente.

---

### Test e Debugging

Oltre alle metriche quantitative, il debugging di sistemi complessi richiede un approccio qualitativo:

- **Ispezione degli Artefatti:** La causa di un errore spesso non è nell'output finale, ma in un passaggio intermedio. È cruciale poter ispezionare ogni artefatto del processo: la query trasformata, i chunk recuperati, il prompt finale inviato all'LLM. Strumenti come LangSmith sono progettati per questa "osservabilità".
- **Creazione di un "Golden Set":** Identifica un set di domande e risposte di alta qualità ("golden set") che rappresentano i casi d'uso più importanti. Esegui regolarmente il tuo sistema contro questo set per monitorare le regressioni ogni volta che apporti una modifica.
- **Test Avversari:** Prova attivamente a "rompere" il sistema con domande ambigue, fuori contesto o che tentano di aggirare le sue istruzioni (prompt injection).

Un approccio sistematico alla progettazione e una valutazione rigorosa basata su metriche chiare sono essenziali per trasformare un prototipo interessante in un'applicazione AI affidabile e pronta per la produzione.

---

[< Indietro (Model Context Protocol)](./11-model-context-protocol-mcp.md) | [Torna all'Indice](./index.md) | [Avanti (Conclusione e Futuro) >](./13-conclusione-e-tendenze-future.md)
