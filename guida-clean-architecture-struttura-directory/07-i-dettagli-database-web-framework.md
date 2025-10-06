## 07 - I Dettagli: Database, Web e Framework

Uno dei mantra della Clean Architecture è: "Il database è un dettaglio, il web è un dettaglio, il framework è un dettaglio". Questo non significa che non siano importanti, ma che sono dettagli *implementativi* e non devono definire l'architettura del sistema. Le regole di business devono rimanere pure e agnostiche rispetto a questi elementi.

In una struttura di directory ben organizzata, questa separazione è fisica e visibile. Vediamo dove finisce il codice di questi "dettagli".

### Il Database è un Dettaglio

Le entità e i casi d'uso non devono sapere se viene usato PostgreSQL, MongoDB o un semplice file JSON. Questa decisione può essere posticipata e dovrebbe essere facile da cambiare.

**Dove va il codice:**

Il codice specifico del database risiede nel layer `infrastructure`. Questo include:

*   **Implementazioni dei Repository:** La classe `PostgresUserRepository` che implementa l'interfaccia `IUserRepository` definita nel dominio.
*   **Modelli dell'ORM:** Se usi un ORM come Prisma o TypeORM, gli schemi o i modelli specifici dell'ORM appartengono a `infrastructure`. Le tue `Entities` di dominio non dovrebbero mai essere le stesse classi dei modelli dell'ORM. Esisterà sempre uno strato di mappatura tra l'entità di dominio e il modello di persistenza.
*   **File di Migrazione:** Le migrazioni dello schema del database.
*   **Configurazione della Connessione:** Il codice che inizializza la connessione al database.

**Esempio di struttura:**

```plaintext
/src
  /users
    /domain
      - user.entity.ts
      - i-user.repository.ts       # Astrazione
    /infrastructure
      /persistence
        - user.schema.ts           # Schema specifico dell'ORM (es. Prisma)
        - user.repository.ts       # Implementazione che usa lo schema dell'ORM
        - user.mapper.ts           # Codice per mappare tra User (dominio) e User (schema ORM)
  /shared
    /infrastructure
      - database.ts                # Configurazione generale del client del database
```

### Il Web è un Dettaglio

L'applicazione potrebbe essere esposta tramite un'API REST, GraphQL, un'interfaccia a riga di comando (CLI) o una GUI desktop. I casi d'uso non dovrebbero essere influenzati da questa scelta.

**Dove va il codice:**

Il codice relativo al web risiede nel layer `presentation`.

*   **Controllers/Endpoints:** Le funzioni che gestiscono le richieste HTTP, estraggono i dati dalla richiesta (body, params, query) e invocano i casi d'uso.
*   **Rotte:** La definizione delle rotte dell'API (es. `router.get('/users/:id', ...)`) appartiene a questo layer.
*   **Serializzazione:** La trasformazione dei dati di risposta (spesso DTO) in JSON.

**Esempio di struttura:**

```plaintext
/src
  /users
    /presentation
      - users.controller.ts        # Logica del controller
      - users.routes.ts          # Definizione delle rotte per gli utenti

  /shared
    /presentation
      - server.ts                  # Creazione e configurazione del server Express/Fastify
      - main.ts                    # Punto di avvio dell'applicazione che monta le rotte
```

### Il Framework è un Dettaglio

Il framework (sia esso per il web come Express, o per la dependency injection come NestJS) è lo strumento che lega insieme i pezzi, ma non è l'architettura stessa.

**Dove va il codice:**

Il codice specifico del framework è confinato ai layer più esterni.

*   **Dependency Injection Container:** La configurazione del container DI, dove si registrano le interfacce e le loro implementazioni concrete, è un dettaglio infrastrutturale. Spesso si trova nel punto di avvio dell'applicazione (`main.ts`).
*   **Decorators e Funzioni Specifiche:** L'uso di decorators come `@Controller()` o `@Injectable()` di NestJS è limitato ai file nei layer `presentation` e `infrastructure`.
*   **Middleware del Framework:** Middleware per l'autenticazione, il logging o la gestione degli errori specifici di un framework web appartengono a `presentation`.

Trattando questi elementi come dettagli, si ottiene un'architettura flessibile, in cui aggiornare un ORM, cambiare framework web o aggiungere una nuova interfaccia (es. una CLI) diventano compiti gestibili che non mettono a rischio il cuore della nostra applicazione: le regole di business.

---

[< Indietro (Esempio: Struttura per Funzionalità (Feature-based))](./06-esempio-struttura-per-funzionalita-feature-based.md) | [Torna all'Indice](./index.md) | [Avanti (Organizzare i Test) >](./08-organizzare-i-test.md)
