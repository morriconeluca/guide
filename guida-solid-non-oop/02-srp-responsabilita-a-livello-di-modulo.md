## 02 - SRP: Responsabilità a Livello di Modulo

Il Single Responsibility Principle (SRP) afferma che un modulo software dovrebbe avere **una e una sola ragione per cambiare**. Nel mondo OOP, questo si applica alle classi. In un contesto non-OOP, lo applichiamo a **moduli** (file) e **funzioni**.

La "ragione per cambiare" è spesso legata a un "attore": un requisito di business, un sistema esterno, uno stakeholder. Se un modulo serve più attori, le loro esigenze contrastanti lo renderanno fragile e difficile da manutenere.

### Traduzione del Principio

*   **Contesto OOP:** Una classe dovrebbe avere un solo scopo.
*   **Contesto Funzionale/Procedurale:** Una funzione dovrebbe fare una sola cosa. Un modulo (file) dovrebbe raggruppare funzioni che servono un unico scopo o attore di alto livello.

### Esempio: Calcolo e Formattazione di un Report

Immaginiamo di dover generare un report finanziario. I requisiti sono due:
1.  **Attore Contabilità:** Calcolare il totale delle vendite per una lista di transazioni.
2.  **Attore Utente Finale:** Formattare questo totale in una stringa da visualizzare in una pagina web (es. "Totale: €1,234.56").

#### Violazione dell'SRP

Un approccio ingenuo potrebbe mettere tutto in un'unica funzione all'interno di un unico modulo:

```typescript
// file: report.ts (VIOLAZIONE SRP)

interface Transaction {
  id: string;
  amount: number;
}

export function generateSalesReport(transactions: Transaction[]): string {
  // 1. Responsabilità: Calcolo (logica di business)
  const total = transactions.reduce((sum, tx) => sum + tx.amount, 0);

  // 2. Responsabilità: Formattazione (logica di presentazione)
  const formattedTotal = new Intl.NumberFormat('it-IT', {
    style: 'currency',
    currency: 'EUR',
  }).format(total);

  return `Totale Vendite: ${formattedTotal}`;
}
```

Questo modulo ha **due ragioni per cambiare**: 
1.  La logica di calcolo potrebbe cambiare (es. escludere le transazioni rimborsate).
2.  La logica di formattazione potrebbe cambiare (es. cambiare valuta, formato del testo).

Un cambiamento richiesto dall'attore "Contabilità" potrebbe rompere la visualizzazione per l'"Utente Finale", e viceversa.

### Applicazione dell'SRP

Separiamo le responsabilità in moduli distinti, ognuno dedicato a un singolo attore.

**1. Modulo per la Logica di Business (Attore: Contabilità)**

Questo modulo si occupa solo del calcolo. Non sa nulla di come i dati verranno presentati.

```typescript
// file: sales-calculator.ts

interface Transaction {
  id: string;
  amount: number;
}

export function calculateTotalSales(transactions: Transaction[]): number {
  return transactions.reduce((sum, tx) => sum + tx.amount, 0);
}
```

**2. Modulo per la Logica di Presentazione (Attore: Utente Finale)**

Questo modulo si occupa solo della formattazione. Riceve un numero e lo trasforma in una stringa.

```typescript
// file: report-formatter.ts

export function formatSalesReport(total: number): string {
  const formattedTotal = new Intl.NumberFormat('it-IT', {
    style: 'currency',
    currency: 'EUR',
  }).format(total);

  return `Totale Vendite: ${formattedTotal}`;
}
```

**3. Modulo Coordinatore**

Infine, un terzo modulo può orchestrare la chiamata agli altri due.

```typescript
// file: sales-report.ts

import { calculateTotalSales } from './sales-calculator';
import { formatSalesReport } from './report-formatter';

export function generateFormattedSalesReport(transactions: Transaction[]): string {
  const total = calculateTotalSales(transactions);
  const formattedReport = formatSalesReport(total);
  return formattedReport;
}
```

Ora, se la logica di calcolo cambia, modifichiamo solo `sales-calculator.ts`. Se cambia la formattazione, modifichiamo solo `report-formatter.ts`. I moduli sono disaccoppiati, più facili da testare e riutilizzare.

---

[< Capitolo Precedente: 01 - Introduzione: SOLID Oltre l'OOP](./01-introduzione-solid-oltre-oop.md)

[> Capitolo Successivo: 03 - OCP: Estensione tramite Composizione e Funzioni di Ordine Superiore](./03-ocp-estensione-tramite-composizione.md)

[< Torna all'Indice](./index.md)
