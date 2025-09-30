# 7. Prompt, Spec e PRD Driven Development

Il Context Engineering non si limita a migliorare le capacità di un'applicazione AI; sta anche trasformando radicalmente il modo in cui costruiamo il software. Stanno emergendo nuove metodologie di sviluppo che mettono l'interazione con l'LLM al centro del processo creativo. Invece di scrivere codice, gli sviluppatori si concentrano sempre più sulla creazione del contesto giusto per guidare l'IA.

In questo capitolo esploreremo tre di queste metodologie "driven": Prompt Driven Development (PDD), Spec Driven Development (SDD) e PRD Driven Development (PRDDD).

---

### Prompt Driven Development (PDD)

Il PDD è l'approccio più diretto e flessibile. Eleva il prompt da una semplice richiesta a un vero e proprio **artefatto di sviluppo**. L'idea è che un prompt ben scritto, versionato e mantenuto, diventi la sorgente di verità per un pezzo di codice.

- **Concetto Chiave:** Invece di modificare direttamente il codice, si modifica il prompt e si rigenera il codice. Questo costringe a mantenere l'intento e la documentazione (il prompt stesso) sempre allineati con l'implementazione.

- **Workflow Esempio (con Gemini CLI):**

  1.  **Definizione (Prompt):** Lo sviluppatore crea un prompt dettagliato in un file, ad esempio `crea_hook_debounce.prompt`:
      > "Agisci come un esperto di React. Crea un custom hook `useDebounce` in TypeScript. L'hook deve accettare `value` e `delay`. Deve restituire il valore solo dopo che `delay` millisecondi sono passati senza che `value` sia cambiato. Includi TSDoc e un esempio di utilizzo."
  2.  **Generazione (CLI):** Lo sviluppatore esegue un comando che passa il prompt a Gemini CLI:
      ```bash
      gemini ai generate -p crea_hook_debounce.prompt > src/hooks/useDebounce.ts
      ```
  3.  **Verifica e Test:** Lo sviluppatore ispeziona il codice generato e, se necessario, usa un altro prompt per generare i test.
  4.  **Iterazione:** Se serve una modifica (es. aggiungere un valore iniziale), lo sviluppatore non tocca il file `.ts`, ma aggiorna il file `.prompt` e riesegue il comando di generazione.

- **Vantaggi:** Ottimo per la prototipazione rapida, per moduli autocontenuti e per mantenere la documentazione (il prompt) sincronizzata con il codice.

---

### Spec Driven Development (SDD)

L'SDD è un approccio più strutturato e rigoroso. Invece di un prompt in linguaggio naturale, la fonte di verità è una **specifica formale e leggibile dalla macchina**. Questa specifica agisce come un contratto che descrive il comportamento atteso del software.

- **Concetto Chiave:** La specifica (spesso un file YAML o JSON) definisce input, output, invarianti e casi limite. Un agente AI (come Gemini CLI) usa questa specifica come contesto principale per generare, testare e validare il codice.

- **Workflow Esempio (con Gemini CLI e Spec-Kit):**

  1.  **Definizione (Specifica):** Lo sviluppatore scrive una specifica usando un formato come quello proposto da `github/spec-kit`. Ad esempio, `api_validator.spec.yaml`:
      ```yaml
      name: apiValidator
      description: Una funzione che valida un oggetto contro uno schema.
      inputs:
        - name: data
          type: object
          description: L'oggetto da validare.
        - name: schema
          type: object
          description: Lo schema di validazione.
      output:
        type: boolean
        description: True se l'oggetto è valido, altrimenti False.
      examples:
        - description: Caso di successo
          inputs:
            data: { user: 'test' }
            schema: { properties: { user: { type: 'string' } } }
          output: true
      ```
  2.  **Generazione (CLI):** Gemini CLI viene invocato con la specifica come contesto principale:
      ```bash
      gemini ai generate --spec api_validator.spec.yaml > src/validators/apiValidator.ts
      ```
  3.  **Test Automatico:** L'agente AI può usare la sezione `examples` della specifica per generare automaticamente unit test che verificano la correttezza dell'implementazione rispetto al contratto.

- **Vantaggi:** Garantisce maggiore robustezza e qualità del codice. La specifica funge da documentazione vivente e permette una validazione automatica, riducendo gli errori e le ambiguità.

---

### PRD Driven Development (PRDDD)

Questo approccio opera al livello più alto di astrazione. La fonte di verità non è un prompt tecnico o una specifica di funzione, ma l'intero **Product Requirements Document (PRD)**, il documento che descrive le finalità di business, il target di utenti, le user story e i requisiti di una feature o di un prodotto.

- **Concetto Chiave:** L'LLM agisce come un partner strategico che, a partire dal PRD, aiuta a orchestrare l'intero ciclo di sviluppo.

- **Workflow Esempio (con Gemini CLI come assistente):**

  1.  **Contesto (PRD):** Un Product Manager scrive un PRD per una nuova feature, "Salvataggio Automatico". Il documento viene salvato in un percorso accessibile (es. `docs/prd-autosave.md`).
  2.  **Pianificazione (CLI):** Uno sviluppatore usa Gemini CLI fornendo il PRD come contesto:
      ```bash
      gemini ai "Leggi il file docs/prd-autosave.md e proponi un piano di implementazione tecnico. Suddividi il lavoro in task, identifica i componenti React da creare e le modifiche necessarie al backend."
      ```
  3.  **Generazione Guidata:** L'LLM produce un piano dettagliato. Lo sviluppatore può quindi usare questo piano per guidare le fasi successive, generando i singoli componenti o le funzioni API con prompt più specifici, sempre mantenendo il PRD come contesto di riferimento per assicurare l'allineamento con gli obiettivi di business.

- **Vantaggi:** Assicura che lo sviluppo tecnico rimanga strettamente allineato agli obiettivi di prodotto. Facilita la collaborazione tra team tecnici e non tecnici, usando il PRD come linguaggio comune. L'LLM può aiutare a identificare dipendenze, rischi e requisiti non esplicitati nel documento.

Queste metodologie non si escludono a vicenda. Un team potrebbe usare il PRDDD per la pianificazione di alto livello, l'SDD per definire i contratti delle API e dei moduli critici, e il PDD per la generazione rapida di componenti UI o utility, sfruttando la potenza del Context Engineering in ogni fase del ciclo di vita del software.

---

[< Indietro (Tool Use)](./06-tool-use-e-function-calling.md) | [Torna all'Indice](./index.md) | [Avanti (Metodologie Avanzate SDLC) >](./08-metodologie-e-strumenti-avanzati-sdlc.md)
