# 4. Le Tecniche Avanzate

Oltre le basi, esistono tecniche avanzate che sbloccano la vera potenza dei modelli linguistici, permettendo loro di affrontare compiti multi-stadio, aumentare l'affidabilità e persino interagire con strumenti esterni.

---

### 1. Self-Consistency (Auto-Coerenza)

Questa tecnica è un'evoluzione diretta della Chain-of-Thought (CoT) e mira a migliorare drasticamente l'affidabilità delle risposte, specialmente in problemi di logica e matematica.

- **Cos'è:** Invece di generare una sola catena di pensiero, si chiede al modello di generarne diverse e indipendenti per lo stesso problema. La risposta finale viene scelta tramite un "voto di maggioranza" tra le conclusioni delle diverse catene.
- **Come Funziona:** Si basa sul principio che, sebbene un modello possa fare un errore casuale in un singolo ragionamento, è meno probabile che faccia lo stesso errore in molti ragionamenti diversi. Le risposte corrette tendono a convergere sulla stessa conclusione attraverso percorsi logici differenti.
- **Esempio Pratico (Problema Matematico):**
  > **Prompt:**
  >
  > ```
  > Domanda: In un bar ci sono 10 tavoli. La metà dei tavoli ha 4 sedie ciascuno. L'altra metà ha solo 2 sedie ciascuno. Quante sedie ci sono in totale?
  >
  > Risolvi questo problema in tre modi diversi, ragionando passo dopo passo per ogni metodo. Infine, indica la risposta che ritieni più affidabile.
  > ```
  >
  > **Output (probabile):**
  >
  > ```
  > **Metodo 1:**
  > 1. Metà dei tavoli sono 10 / 2 = 5 tavoli.
  > 2. I primi 5 tavoli hanno 5 * 4 = 20 sedie.
  > 3. I restanti 5 tavoli hanno 5 * 2 = 10 sedie.
  > 4. Totale sedie: 20 + 10 = 30.
  >
  > **Metodo 2:**
  > 1. Calcolo le sedie per tipo di tavolo. Tavoli grandi: 5 tavoli * 4 sedie/tavolo = 20 sedie.
  > 2. Tavoli piccoli: 5 tavoli * 2 sedie/tavolo = 10 sedie.
  > 3. Somma totale: 20 + 10 = 30.
  >
  > **Metodo 3 (errato):**
  > 1. Ci sono 10 tavoli.
  > 2. Il numero medio di sedie è (4+2)/2 = 3 sedie per tavolo.
  > 3. Totale sedie: 10 tavoli * 3 sedie/tavolo = 30.
  >
  > **Conclusione:**
  > Tutti e tre i metodi, sebbene uno usi una media, portano alla stessa risposta. La risposta più affidabile è 30.
  > ```
- **Quando Usarla:** Per compiti critici dove l'accuratezza è fondamentale (es. analisi quantitative, problemi logici complessi).

---

### 2. Generated Knowledge Prompting (Prompt con Conoscenza Generata)

Questa tecnica aiuta il modello a "fare i compiti" prima di rispondere, migliorando la profondità e l'accuratezza delle risposte su argomenti complessi o di nicchia.

- **Cos'è:** Si istruisce il modello a generare prima una serie di fatti o informazioni chiave sull'argomento della domanda, e solo dopo a usare queste informazioni per costruire la risposta finale.
- **Come Funziona:** Forza il modello a recuperare e strutturare la sua conoscenza latente su un argomento prima di tentare di rispondere. Questo "riscaldamento" riduce il rischio di allucinazioni (informazioni inventate) e porta a risposte più complete.
- **Esempio Pratico (Domanda su un argomento di nicchia):**
  > **Prompt:**
  >
  > ```
  > Domanda: Quali sono le implicazioni dell'uso della tecnologia CRISPR-Cas9 per le malattie genetiche umane?
  >
  > Prima di rispondere, genera una lista di 4-5 fatti chiave sulla tecnologia CRISPR-Cas9, includendo cos'è e come funziona. Dopodiché, usa questi fatti per strutturare la tua risposta alla domanda principale.
  > ```
- **Quando Usarla:** Quando si trattano argomenti molto specifici, tecnici o scientifici, per assicurarsi che la risposta sia ben fondata e non superficiale.

---

### 3. Prompt Chaining (Concatenazione di Prompt)

Questa è una strategia da vero "ingegnere del software": scomporre un problema grande e complesso in una sequenza di problemi più piccoli e gestibili.

- **Cos'è:** Invece di un unico, enorme prompt, si crea una catena di prompt più brevi. L'output di un prompt viene utilizzato come input per il prompt successivo, creando un workflow.
- **Come Funziona:** Permette di guidare il modello passo dopo passo, controllando la qualità di ogni fase intermedia. È come una catena di montaggio: ogni prompt ha un solo compito specifico, e il risultato finale è la somma di tutti i passaggi ben eseguiti.
- **Esempio Pratico (Creazione di un articolo):**
  1.  **Prompt 1 (Brainstorming):** `Genera 5 idee per un titolo di un post sul blog che parli dei benefici del lavoro da remoto per le aziende.`
  2.  **Prompt 2 (Scelta e Outline):** `Usa il titolo "Oltre il Risparmio: Come il Lavoro da Remoto Aumenta la Produttività e la Resilienza Aziendale". Crea un outline dettagliato per un articolo con questo titolo.`
  3.  **Prompt 3 (Scrittura Introduzione):** `Scrivi un'introduzione accattivante per l'articolo, basandoti sull'outline generato.`
  4.  **Prompt 4 (Scrittura Corpo):** `Scrivi il primo paragrafo del corpo del testo, espandendo il primo punto dell'outline.`
  5.  _(e così via...)_
- **Quando Usarla:** Per qualsiasi compito complesso che può essere scomposto in fasi logiche: scrivere codice, redigere documenti lunghi, pianificare un progetto, analizzare dati in più passaggi.

---

### 4. ReAct (Reason and Act - Ragiona e Agisci)

ReAct è una delle tecniche più avanzate e trasforma il modello da un semplice generatore di testo a un agente in grado di agire. Questa è la tecnica che permette al modello, ad esempio, di eseguire ricerche web o comandi.

- **Cos'è:** Il modello alterna cicli di **Ragionamento** (Thought) e **Azione** (Act). L'azione è tipicamente l'uso di uno strumento esterno (es. una ricerca web, una calcolatrice, un'API). Il risultato dell'azione (Observation) viene usato nel ciclo di ragionamento successivo.
- **Come Funziona:** Il ciclo `Thought -> Act -> Observation` permette al modello di superare i limiti della sua conoscenza statica. Può cercare informazioni aggiornate, eseguire calcoli precisi o interagire con altri sistemi per ottenere i dati necessari a rispondere.
- **Esempio Pratico (Pianificazione di un viaggio):**
  > **Prompt Utente:** `Qual è il modo più economico per andare da Roma a Milano venerdì prossimo e dove posso alloggiare vicino al Duomo spendendo meno di 150€?`
  >
  > **Processo ReAct (semplificato):**
  >
  > 1.  **Thought:** Devo trovare il trasporto più economico e un hotel. Inizio dal trasporto. Mi serve la data di "venerdì prossimo".
  > 2.  **Action:** `get_current_date()` -> **Observation:** `26-09-2025`. Venerdì prossimo è il 3 Ottobre 2025.
  > 3.  **Thought:** Ora cerco i trasporti per quella data.
  > 4.  **Action:** `search_transport(from="Roma", to="Milano", date="2025-10-03")` -> **Observation:** `[Treno: 79€, Aereo: 120€, Bus: 29€]`
  > 5.  **Thought:** Il bus è il più economico. Ora cerco l'hotel.
  > 6.  **Action:** `search_hotel(city="Milano", area="Duomo", max_price="150", checkin="2025-10-03")` -> **Observation:** `[Hotel Milano, 135€, ...]`
  > 7.  **Thought:** Ho tutte le informazioni. Posso formulare la risposta finale.
  > 8.  **Risposta Finale:** "Il modo più economico è il bus a 29€. Per l'alloggio, l'Hotel Milano vicino al Duomo costa 135€ a notte."
- **Quando Usarla:** Quando la risposta richiede informazioni aggiornate, calcoli precisi o l'interazione con sistemi esterni (database, API, file system).

---

[< Indietro (Chain-of-Thought)](./03-chain-of-thought.md) | [Torna all'Indice](./index.md) | [Avanti (Processo Iterativo) >](./05-processo-iterativo.md)
