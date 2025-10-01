# 8. Metodologie e Strumenti Avanzati nello SDLC

Le metodologie "X-Driven Development" che abbiamo visto nel capitolo precedente introducono un nuovo modo di pensare allo sviluppo. Tuttavia, per applicarle in modo scalabile e affidabile, servono framework e strumenti che portino rigore e struttura. Questi strumenti trasformano i documenti di testo (prompt, specifiche) in artefatti eseguibili che possono guidare un agente AI.

In questo capitolo, analizzeremo due approcci avanzati che stanno definendo il futuro dello sviluppo software assistito dall'IA: il **BMAD Method** e **GitHub Spec-Kit**.

---

### BMAD Method: Orchestrare un Team di Agenti AI

Il **BMAD (Breakthrough Method for Agile AI-Driven Development)** è un framework agile e strutturato per la pianificazione e lo sviluppo di progetti basati sull'IA. Si basa sulla simulazione di un team di sviluppo agile composto da diversi **agenti AI specializzati**, ognuno con un ruolo, contesto e obiettivi specifici.

- **Concetto Chiave:** Il BMAD assegna ruoli distinti (Product Manager, Architetto, Sviluppatore, QA, Scrum Master, Product Owner) a diversi agenti AI. Questi agenti collaborano in un flusso di lavoro orchestrato, dove ogni agente riceve solo le informazioni pertinenti per il suo ruolo, sfruttando un Context Engineering sofisticato.

- **Workflow Dettagliato:**

  1.  **Fase di Pianificazione (Greenfield):**

      - Un agente "Product Manager" analizza i requisiti e genera un Product Requirements Document (PRD) strutturato (con FRs, NFRs, epiche e storie).
      - Un agente "Architetto" definisce l'architettura del sistema basandosi sul PRD.
      - Un agente "QA" può fornire input sulla strategia di test per aree ad alto rischio.
      - Un agente "Product Owner" valida i documenti e assicura l'allineamento.
      - La pianificazione si conclude con la suddivisione dei documenti in epiche e storie.

  2.  **Fase di Sviluppo (IDE):**
      - Un agente "Scrum Master" gestisce il flusso di lavoro.
      - Per storie ad alto rischio, l'agente "QA" valuta i rischi (`*risk`) e progetta la strategia di test (`*design`).
      - L'agente "Sviluppatore" implementa il codice e i test per i task assegnati.
      - L'agente "QA" esegue controlli intermedi (`*trace`, `*nfr`) e una revisione completa (`*review`), decidendo sul "Quality Gate".

- **Agenti Speciali:** Il BMAD include agenti con ruoli specifici come `BMad-Master` (agente "tuttofare"), `BMad-Orchestrator` (per piattaforme web) e `Test Architect (Quinn)` (l'agente QA, esperto in strategia di test e quality gates, con comandi come `*risk`, `*design`, `*trace`, `*nfr`, `*review`, `*gate`).

- **Integrazione e Configurazione:** Il BMAD si integra con strumenti come OpenCode e OpenAI Codex. Utilizza file di configurazione come `technical-preferences.md` (per raccomandazioni tecniche) e `core-config.yaml` (per definire file da caricare nel contesto degli agenti).

- **Vantaggi:** La specializzazione degli agenti e il workflow strutturato permettono di ottenere risultati di qualità superiore, un processo più controllato e una migliore gestione del rischio rispetto all'affidarsi a un singolo LLM generico. Il BMAD fornisce una struttura robusta per gestire la complessità e orchestrare la collaborazione tra diverse istanze di LLM.

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
