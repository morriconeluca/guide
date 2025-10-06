## 06 - Esempio: Struttura per Funzionalità (Feature-based)

Nei capitoli precedenti abbiamo stabilito che un'architettura dovrebbe "urlare" il suo dominio e che i principi SOLID e la Regola delle Dipendenze ci guidano nell'organizzare il codice. Ora mettiamo tutto insieme in un approccio pratico e moderno: la **struttura per funzionalità** (nota anche come *feature-based*, *package-by-feature* o *vertical slicing*).

L'idea è semplice: invece di raggruppare il codice per tipo tecnico (es. una cartella con tutti i controller, una con tutti i servizi), lo raggruppiamo per area di business o funzionalità.

### Perché la Struttura per Funzionalità?

*   **Alta Coesione:** Tutto ciò che riguarda una singola funzionalità (es. la gestione degli utenti) si trova in un unico posto. Questo rende il codice più facile da trovare, capire e modificare.
*   **Basso Accoppiamento:** Le funzionalità sono isolate l'una dall'altra. Modificare la gestione degli ordini ha un impatto minimo, o nullo, sulla gestione dei prodotti.
*   **Scalabilità del Team:** Team diversi possono lavorare su funzionalità diverse in parallelo con meno conflitti.
*   **Riflette il Dominio:** Questa struttura "urla" naturalmente il suo scopo, come discusso nel capitolo precedente.

### Esempio Pratico: Un Semplice Blog

Immaginiamo di dover costruire un'applicazione per un blog. Le funzionalità principali potrebbero essere la gestione dei post (`posts`) e la gestione degli utenti (`users`).

Ecco come potrebbe apparire una struttura di directory *feature-based*:

```plaintext
/src

  // --- FUNZIONALITÀ: GESTIONE POST ---
  /posts
    /domain
      - post.entity.ts
      - i-post.repository.ts      # Interfaccia definita dal dominio
    /application
      - create-post.use-case.ts
      - get-post.use-case.ts
    /infrastructure
      - post.repository.ts        # Implementazione concreta del repository
      - post.dto.ts               # DTO per questa funzionalità
    /presentation
      - posts.controller.ts       # Controller/Endpoint per i post

  // --- FUNZIONALITÀ: GESTIONE UTENTI ---
  /users
    /domain
      - user.entity.ts
      - i-user.repository.ts
    /application
      - create-user.use-case.ts
      - get-user.use-case.ts
    /infrastructure
      - user.repository.ts
      - user.dto.ts
    /presentation
      - users.controller.ts

  // --- CODICE CONDIVISO ---
  /shared
    /domain
      - entity.ts                 # Classe base per le entità (opzionale)
    /infrastructure
      - api-errors.ts             # Gestione errori comuni
      - database.ts               # Configurazione del DB
    /presentation
      - middleware.ts             # Middleware comuni (es. auth)

```

### Analisi della Struttura

*   **Vertical Slices:** Ogni cartella di primo livello (`posts`, `users`) è una "fetta verticale" dell'applicazione. Contiene tutto il necessario per far funzionare quella specifica area di business, dal controller al database.

*   **Mini-Architetture:** Ogni cartella di funzionalità contiene al suo interno la propria mini-architettura a layer (`domain`, `application`, `infrastructure`, `presentation`). La Regola delle Dipendenze si applica all'interno di ogni fetta: `presentation` può dipendere da `application`, che a sua volta può dipendere da `domain`, ma mai il contrario.

*   **Comunicazione tra Funzionalità:** Se la funzionalità `posts` avesse bisogno di informazioni su un utente, non dovrebbe mai accedere direttamente al repository di `users`. Dovrebbe invece passare attraverso il "contratto pubblico" della funzionalità `users`, ovvero uno dei suoi casi d'uso (es. `GetUserUseCase`). Questo mantiene le funzionalità disaccoppiate.

*   **Codice Condiviso (`/shared`):** Esisterà sempre del codice che deve essere condiviso tra più funzionalità. Questo codice va inserito in una cartella `shared` (o `common`, `core`). È fondamentale che il codice in `shared` sia molto stabile e astratto. Se inizia a contenere logica di business specifica, è un segnale che quella logica dovrebbe probabilmente vivere all'interno di una funzionalità dedicata.

Questo approccio è potente perché organizza il codice in base a come il business pensa e a come il software evolve, rendendo la codebase un riflesso diretto del dominio che modella.

---

[< Indietro (L'Architettura Urla il suo Scopo)](./05-l-architettura-urla-il-suo-scopo.md) | [Torna all'Indice](./index.md) | [Avanti (I Dettagli: Database, Web e Framework) >](./07-i-dettagli-database-web-framework.md)
