## 08 - Organizzare i Test

Uno dei benefici più significativi di una Clean Architecture è la testabilità. Separando le regole di business dai dettagli implementativi, possiamo testare il cuore della nostra applicazione in modo rapido, affidabile e isolato. La struttura delle directory dei test dovrebbe rispecchiare e supportare questa separazione.

### La Piramide dei Test in una Clean Architecture

La classica piramide dei test si adatta perfettamente a questo tipo di architettura:

*   **Unit Test (molti e veloci):** Testano le singole unità di codice in isolamento. Nella Clean Architecture, questo significa testare le entità e i casi d'uso senza alcuna dipendenza esterna (no database, no API).
*   **Integration Test (un po' meno, più lenti):** Testano come più unità lavorano insieme. Un esempio classico è testare l'integrazione tra i casi d'uso e l'implementazione concreta di un repository per verificare che la comunicazione con il database funzioni come previsto.
*   **End-to-End (E2E) Test (pochi e lenti):** Testano l'intero flusso dell'applicazione, dalla richiesta HTTP fino alla risposta, simulando il comportamento di un utente reale.

### Struttura della Directory dei Test

Una buona pratica è creare una directory `/tests` (o `__tests__`) alla radice del progetto, che a sua volta rispecchi la struttura della directory `/src`. Questo rende immediato trovare i test relativi a un modulo specifico.

```plaintext
/my-project
  /src
    /posts
      /domain
        - post.entity.ts
      /application
        - create-post.use-case.ts
    /users
      ...

  /tests
    /unit
      /posts
        /domain
          - post.entity.spec.ts
        /application
          - create-post.use-case.spec.ts
      /users
        ...

    /integration
      /posts
        - posts.repository.integration.spec.ts
      /users
        ...

    /e2e
      - posts.e2e.spec.ts
      - users.e2e.spec.ts
```

### Dove e Cosa Testare

#### Unit Test (`/tests/unit`)

*   **Cosa testare:**
    *   **Entità (`/domain`):** Si testano le regole di business critiche. Ad esempio, in un'entità `Order`, si verifica che non si possa aggiungere un prodotto con quantità negativa. Questi test sono puri, senza mock.
    *   **Casi d'Uso (`/application`):** Si testa la logica applicativa. Qui si usano i **mock** per le dipendenze esterne. Il repository del database viene sostituito con un mock in memoria che simula il suo comportamento. Questo permette di testare la logica del caso d'uso in completo isolamento.

*   **Esempio (`create-post.use-case.spec.ts`):**
    *   Si crea un mock di `IPostRepository`.
    *   Si istanzia `CreatePostUseCase` passando il repository mockato.
    *   Si esegue il caso d'uso.
    *   Si verifica che il metodo `save` del repository mockato sia stato chiamato esattamente una volta con i dati corretti.

#### Integration Test (`/tests/integration`)

*   **Cosa testare:** L'interazione tra i layer. L'obiettivo è verificare che i "contratti" (interfacce) siano implementati correttamente e che la comunicazione funzioni.
*   **Esempio (`posts.repository.integration.spec.ts`):**
    *   Si usa un vero database di test (spesso un'istanza Docker o un database in memoria come SQLite).
    *   Si istanzia la vera implementazione del repository (es. `PostgresPostRepository`).
    *   Si eseguono operazioni di salvataggio, ricerca, aggiornamento e cancellazione.
    *   Si verifica che i dati vengano effettivamente e correttamente scritti e letti dal database di test.

#### End-to-End Test (`/tests/e2e`)

*   **Cosa testare:** L'intero stack applicativo. Questi test non conoscono i dettagli interni dell'architettura.
*   **Esempio (`posts.e2e.spec.ts`):**
    *   Si avvia l'intera applicazione.
    *   Si usa uno strumento come Supertest (per le API) o Cypress/Playwright (per le UI) per inviare una richiesta HTTP all'endpoint `POST /posts`.
    *   Si verifica che la risposta HTTP abbia il codice di stato corretto (es. 201 Created) e che il body della risposta contenga il post creato.
    *   Opzionalmente, si può inviare una successiva richiesta `GET /posts/:id` per confermare che il dato sia stato persistito.

Questa struttura di test, allineata alla Clean Architecture, garantisce una copertura completa e affidabile, permettendo di modificare e far evolvere il software con fiducia.

---

[< Capitolo Precedente: 07 - I Dettagli: Database, Web e Framework](./07-i-dettagli-database-web-framework.md)

[> Capitolo Successivo: 09 - Conclusione e Repository di Esempio](./09-conclusione-e-repository-di-esempio.md)

[< Torna all'Indice](./index.md)
