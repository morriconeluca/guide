## 04 - LSP: Il Principio dei Contratti Comportamentali

Il Liskov Substitution Principle (LSP) è spesso considerato il principio più accademico e strettamente legato all'ereditarietà della programmazione a oggetti. La sua definizione formale è:

> Se S è un sottotipo di T, allora gli oggetti di tipo T in un programma possono essere sostituiti con oggetti di tipo S senza alterare le proprietà desiderabili di quel programma.

Come possiamo applicare un principio che parla di "sottotipi" in un contesto senza classi né ereditarietà? La chiave è astrarre il concetto: non parliamo più di sottotipi, ma di **implementazioni intercambiabili** che rispettano un **contratto comportamentale**.

### Traduzione del Principio

*   **Contesto OOP:** Una classe derivata deve essere sostituibile alla sua classe base senza causare errori.
*   **Contesto Funzionale:** Una funzione (o un modulo) che implementa una certa operazione deve essere sostituibile con un'altra funzione (o modulo) che implementa la stessa operazione, senza che il programma che la utilizza si rompa. Il "contratto" è definito dalla **firma della funzione** (input/output) e dal suo **comportamento atteso**.

In breve, se una funzione sembra un'anatra e si comporta come un'anatra, dovrebbe poter essere usata ovunque sia richiesta un'anatra.

### Esempio: Elaborazione di Liste di Numeri

Immaginiamo di avere una funzione di alto livello che prende una lista di numeri e una funzione "elaboratore" per trasformarla.

**1. Definire il Contratto (la Firma)**

Il nostro contratto è semplice: una funzione che accetta un array di numeri e restituisce un array di numeri.

```typescript
// Il nostro "contratto" o "interfaccia funzionale"
export type ListProcessor = (numbers: number[]) => number[];

// La nostra funzione di alto livello, che dipende dall'astrazione "ListProcessor"
export function processNumbers(numbers: number[], processor: ListProcessor): number[] {
  return processor(numbers);
}
```

**2. Implementazioni Sostituibili (che rispettano l'LSP)**

Ora creiamo diverse funzioni che rispettano questo contratto. Ognuna prende `number[]` e restituisce `number[]`.

```typescript
// file: processors.ts

// Doppia ogni numero
export const doubleEach = (numbers: number[]): number[] => numbers.map(n => n * 2);

// Eleva al quadrato ogni numero
export const squareEach = (numbers: number[]): number[] => numbers.map(n => n * n);

// Filtra solo i numeri pari
export const filterEvens = (numbers: number[]): number[] => numbers.filter(n => n % 2 === 0);
```

Queste tre funzioni sono perfettamente sostituibili l'una con l'altra all'interno di `processNumbers`, perché rispettano il contratto. Il programma non si romperà.

```typescript
const data = [1, 2, 3, 4];

processNumbers(data, doubleEach);  // -> [2, 4, 6, 8]
processNumbers(data, squareEach);  // -> [1, 4, 9, 16]
processNumbers(data, filterEvens); // -> [2, 4]
```

**3. Un'Implementazione che Viola l'LSP**

Ora creiamo una funzione che, pur sembrando utile, viola il contratto di `ListProcessor`.

```typescript
// file: bad-processor.ts (VIOLAZIONE LSP)

// Questa funzione viola il contratto perché restituisce `number`, non `number[]`
export const sumAll = (numbers: number[]): number => {
  return numbers.reduce((sum, n) => sum + n, 0);
};
```

Se provassimo a usare `sumAll` con la nostra funzione `processNumbers`, il sistema si romperebbe. In TypeScript, il compilatore ci avviserebbe immediatamente di un errore di tipo. In un linguaggio a tipizzazione dinamica, otterremmo un errore a runtime.

```typescript
// ERRORE: Il tipo 'number' non è assegnabile al tipo 'number[]'.
// Il nostro programma si aspetta una funzione che restituisca un array, ma ne riceve una che restituisce un numero.
processNumbers(data, sumAll); 
```

L'LSP, in un contesto funzionale, ci impone di essere rigorosi con le firme delle funzioni e con il loro comportamento atteso. Ci spinge a creare astrazioni affidabili, garantendo che le diverse implementazioni di un'astrazione siano veramente intercambiabili, rendendo il nostro sistema più prevedibile e robusto.

---

[< Indietro (OCP: Estensione tramite Composizione e Funzioni di Ordine Superiore)](./03-ocp-estensione-tramite-composizione.md) | [Torna all'Indice](./index.md) | [Avanti (ISP: Interfacce Leggere per le Funzioni) >](./05-isp-interfacce-leggere-per-le-funzioni.md)
