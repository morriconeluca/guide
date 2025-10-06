## 03 - I Livelli e la Regola delle Dipendenze

La Clean Architecture organizza il software in cerchi concentrici, o layer. Ogni cerchio rappresenta un'area diversa del software. L'obiettivo è separare le aree in modo che le regole di business possano essere testate e sviluppate indipendentemente dai dettagli implementativi come il database o l'interfaccia utente.

I quattro cerchi principali sono:

1.  **Entities (Entità)**
2.  **Use Cases (Casi d'Uso)**
3.  **Interface Adapters (Adattatori di Interfaccia)**
4.  **Frameworks & Drivers (Framework e Driver)**

![Clean Architecture](https://blog.cleancoder.com/uncle-bob/images/2012-08-13-the-clean-architecture/CleanArchitecture.jpg)
*(Immagine di Robert C. Martin)*

### La Regola delle Dipendenze

La regola che governa questa architettura è semplice ma fondamentale:

> **Le dipendenze nel codice sorgente possono puntare solo verso l'interno.**

Nulla in un cerchio interno può sapere alcunché di un cerchio esterno. In particolare, il nome di una classe, funzione o variabile definita in un cerchio esterno non deve mai essere menzionato nel codice di un cerchio interno. 

Questo significa che il codice delle regole di business (`Entities` e `Use Cases`) non può dipendere da nessun dettaglio implementativo.

### I Livelli in Dettaglio

#### 1. Entities (Entità)

*   **Cosa sono:** Incapsulano le **regole di business critiche** a livello aziendale. Sono gli oggetti del dominio di più alto livello (es. `Product`, `Order`).
*   **Caratteristiche:** Sono i meno soggetti a cambiare quando qualcosa di esterno cambia. Non sanno nulla di UI, database o framework.
*   **Struttura di Directory:** Solitamente si trovano in una cartella `domain` o `entities`.

    ```plaintext
    /src
      /domain
        - product.entity.ts
        - order.entity.ts
    ```

#### 2. Use Cases (Casi d'Uso)

*   **Cosa sono:** Contengono le **regole di business specifiche dell'applicazione**. Orchestrano il flusso di dati da e verso le entità per realizzare i casi d'uso del sistema (es. `CreateUserUseCase`, `GetProductDetailsUseCase`).
*   **Caratteristiche:** I cambiamenti in questo layer non dovrebbero influenzare le entità. Allo stesso modo, cambiamenti esterni come la UI o il database non dovrebbero influenzare i casi d'uso.
*   **Struttura di Directory:** Spesso in una cartella `application` or `use-cases`.

    ```plaintext
    /src
      /application
        /user
          - create-user.use-case.ts
    ```

#### 3. Interface Adapters (Adattatori di Interfaccia)

*   **Cosa sono:** Questo layer è un insieme di adattatori che convertono i dati dal formato più comodo per i casi d'uso e le entità al formato più comodo per i servizi esterni come il database o il web. Comprende **Controllers**, **Presenters** e **Repositories**.
*   **Caratteristiche:** È il punto in cui i dati vengono trasformati. Ad esempio, un Controller accetta una richiesta HTTP, la converte in un oggetto comprensibile dal Caso d'Uso, e poi converte la risposta del Caso d'Uso in una risposta HTTP.
*   **Struttura di Directory:** Tipicamente in una cartella `infrastructure` o `presentation`.

#### 4. Frameworks & Drivers (Framework e Driver)

*   **Cosa sono:** È il cerchio più esterno. Contiene tutti i dettagli: il framework web (Express, NestJS), l'ORM (Prisma, TypeORM), il driver del database, la libreria UI (React, Vue).
*   **Caratteristiche:** È il livello più volatile e meno stabile. Il codice in questo layer è solo un "dettaglio" che fa da collante.

### La Regola in Pratica

Ecco un esempio di `import` che rispetta la Regola delle Dipendenze. Un caso d'uso (cerchio interno) che utilizza un'entità (cerchio ancora più interno).

```typescript
// File: /src/application/product/add-product.use-case.ts

// L'import punta verso l'interno: `application` dipende da `domain`.
// Questo è VALIDO.
import { Product } from '../../domain/product.entity';

export class AddProductUseCase {
  execute(productData: any): Product {
    // ... logica del caso d'uso
    const product = new Product(productData);
    // ... salva il prodotto usando un repository (tramite interfaccia)
    return product;
  }
}
```

Un `import` da `application` a `infrastructure` sarebbe una violazione diretta della regola.

---

[< Capitolo Precedente: 02 - I Principi SOLID nella Struttura del Codice](./02-i-principi-solid-nella-struttura-del-codice.md)

[> Capitolo Successivo: 04 - Attraversare i Confini con i DTO](./04-attraversare-i-confini-con-i-dto.md)

[< Torna all'Indice](./index.md)
