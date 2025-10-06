## 06 - DIP: Invertire le Dipendenze con le Funzioni

Il Dependency Inversion Principle (DIP) è il collante che tiene insieme le architetture pulite e disaccoppiate. La sua definizione formale ha due parti:

> A. I moduli di alto livello non dovrebbero dipendere dai moduli di basso livello. Entrambi dovrebbero dipendere da astrazioni.
> B. Le astrazioni non dovrebbero dipendere dai dettagli. I dettagli dovrebbero dipendere dalle astrazioni.

Questo principio può sembrare complesso, ma l'idea di fondo è semplice: il codice che contiene la logica di business importante (alto livello) non deve essere accoppiato ai dettagli implementativi (basso livello) come il database, il file system o le API di rete. La soluzione è, ancora una volta, l'uso di astrazioni.

### Traduzione del Principio

*   **Contesto OOP:** Le classi di alto livello dipendono da interfacce, non da classi concrete. Le classi di basso livello implementano queste interfacce. La dipendenza viene "iniettata" tramite un costruttore o un metodo.
*   **Contesto Funzionale:** Le funzioni di alto livello dipendono da "contratti funzionali" (firme di funzioni), non da funzioni concrete. Le funzioni di basso livello (i dettagli) vengono passate come argomenti (dependency injection) alle funzioni di alto livello.

### Esempio: Una Logica di Business con Logging

Immaginiamo una funzione di alto livello che esegue un'importante operazione di business e che ha bisogno di registrare (loggare) i suoi progressi.

#### Violazione del DIP

Un approccio diretto e sbagliato accoppia la logica di business direttamente a un'implementazione specifica di logging (es. un logger che scrive su console).

```typescript
// file: file-logger.ts (MODULO DI BASSO LIVELLO)

import * as fs from 'fs';

// Dettaglio implementativo: scrive su un file
export function logToFile(message: string): void {
  fs.appendFileSync('log.txt', `${new Date().toISOString()}: ${message}\n`);
}

// file: process-order.use-case.ts (MODULO DI ALTO LIVELLO - VIOLAZIONE DIP)

import { logToFile } from './file-logger'; // <-- DIPENDENZA DIRETTA DAL DETTAGLIO!

export function processOrder(orderId: string): void {
  // Logica di business di alto livello
  logToFile(`Inizio elaborazione ordine ${orderId}...
`);

  // ... elabora l'ordine ...

  logToFile(`Ordine ${orderId} elaborato con successo.
`);
}
```

Questa architettura è rigida e difficile da testare. 
1.  **Difficile da Testare:** Come si fa a testare `processOrder` senza scrivere effettivamente su un file? Il test diventa un test di integrazione, lento e dipendente dal file system.
2.  **Difficile da Cambiare:** E se domani volessimo loggare su un servizio cloud come Datadog invece che su un file? Dovremmo modificare il codice di `process-order.use-case.ts`, il nostro modulo di alto livello.

### Applicazione del DIP

Invertiamo la dipendenza. Il modulo di alto livello non deve più conoscere i dettagli, ma solo un'astrazione.

**1. Definire l'Astrazione (il Contratto Funzionale)**

L'astrazione è semplicemente la firma della funzione di cui abbiamo bisogno.

```typescript
// file: logger.contract.ts (ASTRAZIONE)

export type LogFunction = (message: string) => void;
```

**2. Modificare il Modulo di Alto Livello per Dipendere dall'Astrazione**

La nostra funzione di alto livello ora accetta la funzione di logging come argomento. Non sa (e non le interessa) cosa faccia quella funzione, sa solo che può chiamarla.

```typescript
// file: process-order.use-case.ts (MODULO DI ALTO LIVELLO - CONFORME A DIP)

import { LogFunction } from './logger.contract';

// La dipendenza è "iniettata" come argomento
export function processOrder(orderId: string, log: LogFunction): void {
  // Logica di business di alto livello
  log(`Inizio elaborazione ordine ${orderId}...
`);

  // ... elabora l'ordine ...

  log(`Ordine ${orderId} elaborato con successo.
`);
}
```

**3. Creare Implementazioni Concrete (Dettagli)**

I moduli di basso livello ora dipendono dall'astrazione, implementando la firma richiesta.

```typescript
// file: concrete-loggers.ts (MODULI DI BASSO LIVELLO)

import * as fs from 'fs';
import { LogFunction } from './logger.contract'; // <-- Il dettaglio dipende dall'astrazione

export const fileLogger: LogFunction = (message) => {
  fs.appendFileSync('log.txt', `${new Date().toISOString()}: ${message}\n`);
};

export const consoleLogger: LogFunction = (message) => {
  console.log(message);
};
```

**4. Assemblare l'Applicazione (il "Main")**

Il punto di ingresso della nostra applicazione (spesso chiamato `main.ts` o `index.ts`) è l'unico luogo che conosce sia le astrazioni che le implementazioni concrete. Il suo compito è "assemblare" i pezzi.

```typescript
// file: main.ts

import { processOrder } from './process-order.use-case';
import { fileLogger, consoleLogger } from './concrete-loggers';

// In produzione, usiamo il fileLogger
processOrder('123', fileLogger);

// In un altro contesto, potremmo usare il consoleLogger
processOrder('456', consoleLogger);
```

Nei test, la situazione diventa banale:

```typescript
// file: process-order.use-case.spec.ts

const mockLogger = jest.fn(); // Un logger finto
processOrder('abc', mockLogger);

expect(mockLogger).toHaveBeenCalledWith('Inizio elaborazione ordine abc...
');
```

Abbiamo disaccoppiato la nostra logica di business dai dettagli, ottenendo un sistema flessibile, testabile e veramente modulare. Questo è il cuore del Dependency Inversion Principle.

---

[< Capitolo Precedente: 05 - ISP: Interfacce Leggere per le Funzioni](./05-isp-interfacce-leggere-per-le-funzioni.md)

[> Capitolo Successivo: 07 - Conclusione: SOLID come Filosofia di Design](./07-conclusione-solid-come-filosofia.md)

[< Torna all'Indice](./index.md)
