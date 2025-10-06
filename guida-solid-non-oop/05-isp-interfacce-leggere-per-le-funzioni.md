## 05 - ISP: Interfacce Leggere per le Funzioni

L'Interface Segregation Principle (ISP) ci avverte di non creare interfacce "grasse". La sua definizione formale è:

> Nessun client dovrebbe essere costretto a dipendere da metodi che non utilizza.

In un contesto non-OOP, dove non abbiamo interfacce esplicite nel senso classico, il principio si applica alla **firma delle nostre funzioni**. I parametri di una funzione sono la sua "interfaccia". L'ISP ci dice di non costringere una funzione a dipendere da più informazioni di quelle di cui ha strettamente bisogno.

### Traduzione del Principio

*   **Contesto OOP:** Preferire molte interfacce piccole e specifiche per un client piuttosto che una grande e generica.
*   **Contesto Funzionale:** Una funzione non dovrebbe richiedere come parametri strutture dati complesse e "grasse" se poi ne utilizza solo una piccola parte. La sua firma dovrebbe essere il più possibile minimale e specifica.

### Esempio: Inviare un'Email di Benvenuto

Immaginiamo di avere una funzione che invia un'email di benvenuto a un nuovo utente. L'oggetto `User` nel nostro sistema è molto ricco di informazioni.

```typescript
interface User {
  id: string;
  name: string;
  email: string;
  dateOfBirth: Date;
  lastLogin: Date;
  isActive: boolean;
  // ...e altre 20 proprietà
}
```

#### Violazione dell'ISP

Una prima implementazione, apparentemente innocua, potrebbe far dipendere la funzione di invio email dall'intero oggetto `User`.

```typescript
// file: email-service.ts (VIOLAZIONE ISP)

import { User } from './user-model';

// Questa funzione dipende dall'intera struttura `User`...
export function sendWelcomeEmail(user: User): void {
  const subject = 'Benvenuto!';
  // ...ma usa solo le proprietà `email` e `name`.
  const body = `Ciao ${user.name}, grazie per esserti registrato!`;

  console.log(`Invio email a ${user.email} con oggetto: ${subject}`);
  // ...logica di invio email...
}
```

Qual è il problema? 
1.  **Accoppiamento Inutile:** La funzione `sendWelcomeEmail` è ora accoppiata all'intera definizione dell'oggetto `User`. Qualsiasi modifica alla struttura di `User` (anche a campi non usati da questa funzione, come `lastLogin`) potrebbe potenzialmente richiedere una ricompilazione o una nuova validazione di questo modulo.
2.  **Riusabilità Ridotta:** E se volessimo inviare un'email di benvenuto a qualcuno che non è un `User` completo, ma di cui abbiamo solo nome ed email? Non potremmo riutilizzare questa funzione senza prima costruire un oggetto `User` fittizio.
3.  **Test più Complessi:** Per testare questa funzione, dobbiamo creare un intero oggetto `User` mock, completo di tutte le sue proprietà, anche quelle che non ci interessano.

### Applicazione dell'ISP

La soluzione è rendere l'"interfaccia" della funzione più piccola e mirata. La funzione dovrebbe richiedere solo i dati di cui ha strettamente bisogno.

```typescript
// file: email-service.ts (CONFORME A ISP)

// L'interfaccia della funzione è ora minimale e specifica.
export function sendWelcomeEmail(name: string, email: string): void {
  const subject = 'Benvenuto!';
  const body = `Ciao ${name}, grazie per esserti registrato!`;

  console.log(`Invio email a ${email} con oggetto: ${subject}`);
  // ...logica di invio email...
}
```

In alternativa, si può usare una piccola struttura dati dedicata (un DTO, in pratica):

```typescript
interface WelcomeEmailRecipient {
  name: string;
  email: string;
}

export function sendWelcomeEmail(recipient: WelcomeEmailRecipient): void {
  // ...logica identica a prima...
}
```

I vantaggi sono immediati:

1.  **Disaccoppiamento:** La funzione non sa più nulla dell'oggetto `User`. È completamente disaccoppiata.
2.  **Massima Riusabilità:** Ora possiamo usare questa funzione per inviare un benvenuto a chiunque, purché si forniscano un nome e un'email.
3.  **Test Semplificati:** Testare la funzione è banale, basta passare due semplici stringhe.

Applicando l'ISP, abbiamo reso la nostra funzione più robusta, riutilizzabile e facile da manutenere, semplicemente riducendo la superficie della sua "interfaccia" (i suoi parametri).

---

[< Capitolo Precedente: 04 - LSP: Il Principio dei Contratti Comportamentali](./04-lsp-il-principio-dei-contratti-comportamentali.md)

[> Capitolo Successivo: 06 - DIP: Invertire le Dipendenze con le Funzioni](./06-dip-invertire-le-dipendenze-con-le-funzioni.md)

[< Torna all'Indice](./index.md)
