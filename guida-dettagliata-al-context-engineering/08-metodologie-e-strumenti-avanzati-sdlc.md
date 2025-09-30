# 8. Metodologie e Strumenti Avanzati nello SDLC

Le metodologie "X-Driven Development" che abbiamo visto nel capitolo precedente introducono un nuovo modo di pensare allo sviluppo. Tuttavia, per applicarle in modo scalabile e affidabile, servono framework e strumenti che portino rigore e struttura. Questi strumenti trasformano i documenti di testo (prompt, specifiche) in artefatti eseguibili che possono guidare un agente AI.

In questo capitolo, analizzeremo due approcci avanzati che stanno definendo il futuro dello sviluppo software assistito dall'IA: il **BMAD Method** e **GitHub Spec-Kit**.

---

### BMAD Method: Orchestrare un Team di Agenti AI

Il **BMAD (Breakthrough Method for Agile AI-Driven Development)** è un framework che struttura lo sviluppo AI-driven simulando un team di sviluppo agile composto da diversi **agenti AI specializzati**.

- **Concetto Chiave:** Invece di usare un unico LLM generico, il BMAD assegna ruoli specifici (Product Manager, Architect, Developer, QA, etc.) a diversi agenti AI. Ogni agente ha un proprio contesto, istruzioni e obiettivi, e collabora con gli altri in un flusso di lavoro orchestrato. Questo approccio si basa su un Context Engineering molto sofisticato, dove ogni agente riceve solo le informazioni pertinenti per il suo ruolo.

- **Workflow Esempio (con Gemini CLI come motore degli agenti):**

  1.  **Pianificazione con Agenti:** Un utente (umano) avvia il processo. L'agente "Product Manager" viene invocato per analizzare i requisiti di alto livello e generare un PRD strutturato. Successivamente, l'agente "Architect" prende in input il PRD per definire l'architettura tecnica, le tecnologie da usare e i contratti delle API.
  2.  **Esecuzione Guidata:** Il piano dell'architetto viene passato all'agente "Developer". Questo agente non scrive tutto il codice in una volta, ma affronta un task alla volta, generando codice che rispetta l'architettura definita.
  3.  **Validazione Continua:** L'agente "QA" riceve il codice generato e, basandosi sui requisiti del PRD e sulle specifiche tecniche, genera e esegue unit test e test di integrazione per validare il lavoro dell'agente Developer.

- **Vantaggi:** La specializzazione degli agenti permette di ottenere risultati di qualità superiore. Il processo è più controllato e meno prono a errori rispetto all'affidarsi a un singolo prompt onnicomprensivo. Il BMAD fornisce una struttura per gestire la complessità e orchestrare la collaborazione tra diverse istanze di LLM.

---

### GitHub Spec-Kit: La Specifica come Contratto Eseguibile

**Spec-Kit** è un toolkit open-source di GitHub che formalizza la metodologia Spec Driven Development (SDD). Il suo scopo è trasformare la specifica da un documento statico a un **artefatto vivente ed eseguibile** che guida l'intero ciclo di vita dello sviluppo.

- **Concetto Chiave:** La specifica diventa la fonte unica e inequivocabile di verità. È un contratto leggibile sia dagli umani che dalle macchine (gli agenti AI). Il toolkit fornisce una struttura a fasi e una CLI per gestire questo processo in modo rigoroso.

- **Struttura e Fasi di Spec-Kit:**

  1.  **`constitution.md`:** Il file più importante. Qui il team definisce i principi non negoziabili del progetto: standard di qualità, convenzioni di codice, stack tecnologico, requisiti di testing. Questo file diventa parte del contesto di ogni richiesta all'IA, agendo da "guardrail".
  2.  **Fase `specify`:** A partire da una richiesta in linguaggio naturale, un agente AI genera una specifica dettagliata (`spec.md`) che include user journey, obiettivi e criteri di accettazione.
  3.  **Fase `plan`:** La specifica viene tradotta in un piano tecnico (`plan.md`), definendo architettura, modelli dati e contratti API.
  4.  **Fase `tasks`:** Il piano viene scomposto in task granulari e testabili.
  5.  **Fase `implement`:** Un agente AI (come Gemini CLI) esegue i task uno per uno, generando il codice.

- **Workflow Esempio (con Gemini CLI):**

  1.  Lo sviluppatore definisce la `constitution.md` con le regole del progetto.
  2.  Esegue il comando `spec-kit specify "Voglio un'API per la gestione di una lista di task"`.
  3.  L'agente AI, guidato dalla costituzione, genera la `spec.md`. Lo sviluppatore la rivede e la approva.
  4.  Lo sviluppatore esegue `spec-kit plan`. L'agente AI genera il piano tecnico, magari definendo un'API REST con specifici endpoint.
  5.  Infine, con `spec-kit tasks`, il lavoro viene suddiviso. A questo punto, Gemini CLI può essere invocato per implementare ogni singolo task (es. "Implementa l'endpoint `POST /tasks` come definito nel piano e nella specifica"), avendo come contesto tutti gli artefatti generati in precedenza.

- **Vantaggi:** Introduce un rigore ingegneristico nello sviluppo AI-driven. Riduce l'ambiguità, migliora la qualità e la coerenza del codice generato e garantisce che l'implementazione finale sia sempre allineata con le specifiche iniziali.

L'integrazione di metodologie formali come BMAD e strumenti come Spec-Kit rappresenta il passaggio dal Context Engineering come "tecnica" al Context Engineering come "disciplina ingegneristica", fondamentale per costruire software complesso e affidabile con l'aiuto dell'intelligenza artificiale.

---

[< Indietro (Metodologie SDLC)](./07-prompt-spec-prd-driven-development.md) | [Torna all'Indice](./index.md) | [Avanti (Architetture) >](./09-architetture-per-la-gestione-del-contesto.md)
