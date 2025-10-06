## 03 - OCP: Estensione tramite Composizione e Funzioni di Ordine Superiore

L'Open-Closed Principle (OCP) è forse il più importante principio di design per creare software flessibile e manutenibile. Esso afferma:

> Un'entità software (modulo, funzione, ecc.) dovrebbe essere aperta all'estensione, ma chiusa alla modifica.

In altre parole, dovremmo essere in grado di aggiungere nuove funzionalità a un sistema senza dover cambiare il codice sorgente esistente, già testato e funzionante. In OOP, questo si ottiene spesso con l'ereditarietà e le classi astratte. In un contesto funzionale, abbiamo uno strumento ancora più potente e flessibile: le **funzioni di ordine superiore** (Higher-Order Functions).

### Traduzione del Principio

*   **Contesto OOP:** Si crea una classe base astratta (chiusa alla modifica) e si estende il comportamento creando nuove sottoclassi (aperta all'estensione).
*   **Contesto Funzionale:** Si crea una funzione "orchestratore" stabile (chiusa alla modifica) che accetta altre funzioni come argomenti per definire il suo comportamento specifico. Il sistema viene esteso fornendo nuove funzioni (aperta all'estensione).

### Esempio: Applicare Sconti a un Prodotto

Immaginiamo di dover calcolare il prezzo finale di un prodotto applicando diversi tipi di sconti (es. sconto stagionale, sconto per nuovi clienti, ecc.).

#### Violazione dell'OCP

Un approccio basato su una serie di `if/else` o `switch` viola palesemente l'OCP. Ogni volta che si aggiunge un nuovo tipo di sconto, si è costretti a **modificare** la funzione `applyDiscount`.

```typescript
// file: discount-calculator.ts (VIOLAZIONE OCP)

interface Product {
  name: string;
  price: number;
}

type DiscountType = 'seasonal' | 'new_customer' | 'vip';

export function applyDiscount(product: Product, discountType: DiscountType): number {
  // Il codice è APERTO alla modifica. Male!
  switch (discountType) {
    case 'seasonal':
      return product.price * 0.9; // 10% di sconto
    case 'new_customer':
      return product.price * 0.85; // 15% di sconto
    case 'vip':
      return product.price * 0.8; // 20% di sconto
    // Se aggiungiamo un nuovo sconto 'black_friday', dobbiamo aggiungere un nuovo 'case'.
    default:
      return product.price;
  }
}
```

Questa funzione non è chiusa. È un punto fragile del nostro sistema, destinato a crescere e a diventare una fonte di bug.

### Applicazione dell'OCP

Per rispettare l'OCP, creiamo una funzione orchestratore che accetta la logica di sconto come argomento. La logica specifica diventa una dipendenza che viene "iniettata" dall'esterno.

**1. Definire il "Contratto" della Funzione**

Definiamo la firma che ogni nostra funzione di sconto dovrà rispettare. In TypeScript, usiamo un tipo.

```typescript
// file: discount-strategies.ts

interface Product { 
  name: string;
  price: number;
}

// Questo è il nostro "contratto" funzionale
export type DiscountStrategy = (price: number) => number;

// Creiamo le nostre strategie come funzioni pure e indipendenti
export const seasonalDiscount: DiscountStrategy = (price) => price * 0.9;
export const newCustomerDiscount: DiscountStrategy = (price) => price * 0.85;
export const vipDiscount: DiscountStrategy = (price) => price * 0.8;
```

**2. Creare la Funzione di Ordine Superiore (Stabile)**

Questa funzione è semplice, stabile e **chiusa alla modifica**. Il suo unico compito è applicare una strategia di sconto a un prezzo.

```typescript
// file: price-calculator.ts

import { DiscountStrategy } from './discount-strategies';

interface Product { 
  name: string;
  price: number;
}

// Questa funzione è CHIUSA alla modifica
export function calculateFinalPrice(product: Product, strategy: DiscountStrategy): number {
  return strategy(product.price);
}
```

**3. Estendere il Comportamento**

Ora il nostro sistema è **aperto all'estensione**. Per aggiungere un nuovo tipo di sconto, non tocchiamo né `price-calculator.ts` né le strategie esistenti. Creiamo semplicemente una nuova funzione.

```typescript
// file: new-discounts.ts

import { DiscountStrategy } from './discount-strategies';

// Nuova funzionalità, nessuna modifica al codice esistente!
export const blackFridayDiscount: DiscountStrategy = (price) => price * 0.7;
```

L'utilizzo diventa:

```typescript
import { calculateFinalPrice } from './price-calculator';
import { seasonalDiscount } from './discount-strategies';
import { blackFridayDiscount } from './new-discounts';

const product = { name: 'Libro', price: 100 };

const seasonalPrice = calculateFinalPrice(product, seasonalDiscount); // 90
const blackFridayPrice = calculateFinalPrice(product, blackFridayDiscount); // 70
```

Abbiamo aggiunto una nuova logica di business senza modificare una sola riga di codice esistente e testato. Questo è il potere dell'OCP in un contesto funzionale.

---

[< Indietro (SRP: Responsabilità a Livello di Modulo)](./02-srp-responsabilita-a-livello-di-modulo.md) | [Torna all'Indice](./index.md) | [Avanti (LSP: Il Principio dei Contratti Comportamentali) >](./04-lsp-il-principio-dei-contratti-comportamentali.md)
