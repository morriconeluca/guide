## 04 - Attraversare i Confini con i DTO

La Regola delle Dipendenze è chiara: il codice interno non può dipendere da quello esterno. Ma allora, come fa un caso d'uso a salvare dati in un database? La risposta è nel **Principio di Inversione delle Dipendenze (DIP)**, uno dei pilastri dei principi SOLID.

Il trucco consiste nell'invertire la direzione della dipendenza usando le interfacce.

### Il Flusso di Controllo vs. La Dipendenza del Codice

Immaginiamo un flusso di controllo standard:

1.  Un **Controller** riceve una richiesta HTTP.
2.  Il **Controller** invoca un **Caso d'Uso**.
3.  Il **Caso d'Uso** esegue la sua logica.
4.  Il **Caso d'Uso** ha bisogno di salvare un'entità nel **Database**.

Senza inversione delle dipendenze, il Caso d'Uso dovrebbe importare e usare una classe del layer del Database, violando la regola. 

`Caso d'Uso` -> `Database` ( **ERRATO!** La dipendenza va verso l'esterno).

### Invertire la Dipendenza con le Interfacce

Per risolvere il problema, il layer interno (il Caso d'Uso) definisce un "contratto": un'interfaccia che descrive l'operazione di cui ha bisogno (es. `IUserRepository`).

1.  Il **Caso d'Uso** (cerchio interno) definisce un'interfaccia `IUserRepository` con un metodo `save(user: User)`. 
2.  Il **Caso d'Uso** dipende solo da questa interfaccia, che appartiene al suo stesso livello o a un livello più interno.
3.  Il **Repository** nel layer del Database (cerchio esterno) *implementa* l'interfaccia `IUserRepository`.

In questo modo, la dipendenza del codice sorgente punta dall'esterno verso l'interno, opponendosi al flusso di controllo.

`Caso d'Uso` <- `Database` ( **CORRETTO!** Il layer del database ora dipende dall'interfaccia definita nel layer dell'applicazione).

#### Esempio di Struttura

```plaintext
/src
  /application             # Layer dei Casi d'Uso (interno)
    /user
      - create-user.use-case.ts
      - i-user.repository.ts   # <-- L'INTERFACCIA (contratto) è definita qui!

  /infrastructure          # Layer del Database (esterno)
    /persistence
      - postgres.user.repository.ts # <-- L'IMPLEMENTAZIONE è qui!
```

Il file `postgres.user.repository.ts` conterrà una riga simile a:

```typescript
import { IUserRepository } from '../../application/user/i-user.repository';

export class PostgresUserRepository implements IUserRepository {
  save(user: User): void {
    // ...logica specifica di PostgreSQL per salvare l'utente...
  }
}
```

### Il Ruolo dei Data Transfer Objects (DTO)

Quando i dati attraversano i confini tra i layer, non dovrebbero essere passati nella loro forma "grezza" o legati a un modello di un framework specifico (es. un modello di un ORM).

Per questo si usano i **Data Transfer Objects (DTO)**: oggetti semplici e senza logica il cui unico scopo è trasferire dati.

*   **Input DTO:** Un Controller riceve una richiesta HTTP, la mappa in un `CreateUserDTO` e passa questo DTO al Caso d'Uso. Il Caso d'Uso non sa nulla di HTTP, riceve solo i dati di cui ha bisogno in una forma pulita.
*   **Output DTO:** Un Caso d'Uso restituisce i dati tramite un `UserDTO`. Un Presenter prenderà questo DTO e lo mapperà in una risposta HTTP (es. JSON) o in un ViewModel per una UI.

L'uso dei DTO garantisce che i dati che attraversano i confini siano strutture semplici e disaccoppiate, prevenendo che dettagli implementativi di un layer "trapelino" in un altro.

---

[< Capitolo Precedente: 03 - I Livelli e la Regola delle Dipendenze](./03-i-livelli-e-la-regola-delle-dipendenze.md)

[> Capitolo Successivo: 05 - L'Architettura Urla il suo Scopo](./05-l-architettura-urla-il-suo-scopo.md)

[< Torna all'Indice](./index.md)
