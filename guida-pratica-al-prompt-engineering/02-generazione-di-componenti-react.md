# 2. Generazione di Componenti React

Questo è il pane quotidiano per un dev frontend. Vediamo come l'IA può accelerare drasticamente la creazione di componenti, dal più semplice al più complesso, rispettando le nostre convenzioni.

Ricorda: le istruzioni di progetto (il nostro "System Prompt") che abbiamo definito nel capitolo precedente stanno già lavorando per noi, istruendo l'IA a usare TypeScript, componenti funzionali e Tailwind CSS, indipendentemente dal tool che hai scelto.

---

### Caso 1: Componente di UI Semplice (Stateless)

Il nostro primo task è creare un semplice componente `Tag`, riutilizzabile per mostrare etichette colorate.

**Prompt:**

> Agendo come un senior frontend developer, crea un componente React chiamato `Tag`.
> 
> Requisiti:
> 1.  Deve essere un componente funzionale scritto in TypeScript (`.tsx`).
> 2.  Accetta una prop `label` (stringa) da visualizzare.
> 3.  Accetta una prop opzionale `color` che può essere `'blue'` o `'green'`. Il default è `'blue'`. 
> 4.  Usa le classi di Tailwind CSS per lo stile. Il tag deve avere padding, angoli arrotondati e colori di testo e sfondo diversi a seconda della prop `color`.

**Output Generato (previsto):**

```typescript
import type { FC } from 'react';
import { cva, type VariantProps } from 'class-variance-authority';

const tagVariants = cva(
  'inline-flex items-center rounded-full border px-2.5 py-0.5 text-xs font-semibold transition-colors focus:outline-none focus:ring-2 focus:ring-ring focus:ring-offset-2',
  {
    variants: {
      color: {
        blue: 'border-transparent bg-blue-100 text-blue-800 dark:bg-blue-800 dark:text-blue-100',
        green: 'border-transparent bg-green-100 text-green-800 dark:bg-green-800 dark:text-green-100',
      },
    },
    defaultVariants: {
      color: 'blue',
    },
  }
);

interface TagProps extends FC<VariantProps<typeof tagVariants>> {
  label: string;
}

const Tag: FC<TagProps> = ({ label, color }) => {
  return <div className={tagVariants({ color })}>{label}</div>;
};

export default Tag;
```

**Analisi:**
L'IA non si è limitata a usare un `if` o un `switch` per i colori. Ha riconosciuto che si tratta di varianti di un componente e ha usato `class-variance-authority` (`cva`), una libreria standard de-facto quando si lavora con Tailwind, per una soluzione molto più pulita e scalabile. Questo è un segno di un co-pilota che comprende le best practice, non solo la sintassi.

---

### Caso 2: Componente Interattivo (Stateful)

Ora alziamo il livello: un componente che deve gestire il proprio stato. Creiamo un selettore di quantità.

**Prompt:**

> Crea un componente React `QuantitySelector` in TypeScript.
> 
> Requisiti:
> 1.  Deve mostrare un numero (la quantità) e due bottoni, uno "-" e uno "+".
> 2.  La quantità deve essere gestita internamente con lo hook `useState`. Il valore iniziale deve essere 1.
> 3.  Il bottone "-" è disabilitato se la quantità è 1.
> 4.  Cliccando "+" si incrementa la quantità, cliccando "-" si decrementa.
> 5.  Lo stile deve essere pulito e moderno, usando classi di Tailwind CSS.

**Output Generato (previsto):**

```typescript
'use client';

import { useState } from 'react';
import type { FC } from 'react';

const QuantitySelector: FC = () => {
  const [quantity, setQuantity] = useState(1);

  const handleDecrement = () => {
    if (quantity > 1) {
      setQuantity((prev) => prev - 1);
    }
  };

  const handleIncrement = () => {
    setQuantity((prev) => prev + 1);
  };

  return (
    <div className="inline-flex items-center rounded-md border">
      <button
        onClick={handleDecrement}
        disabled={quantity === 1}
        className="px-3 py-1 text-lg font-medium text-gray-600 transition hover:bg-gray-100 disabled:cursor-not-allowed disabled:opacity-50"
      >
        -
      </button>
      <span className="w-12 text-center text-lg font-semibold">{quantity}</span>
      <button
        onClick={handleIncrement}
        className="px-3 py-1 text-lg font-medium text-gray-600 transition hover:bg-gray-100"
      >
        +
      </button>
    </div>
  );
};

export default QuantitySelector;

```

**Analisi:**
L'IA ha correttamente identificato la necessità di `'use client'` perché il componente usa `useState`. Ha implementato la logica di stato, inclusa la gestione del caso limite (disabilitare il bottone a 1), e ha applicato uno stile Tailwind pulito e funzionale. Il codice è pronto all'uso.

---

### Caso 3 (React 19): Dati Asincroni con l'hook `use`

Questo è un esempio avanzato che sfrutta le nuove feature di React 19 per semplificare il codice asincrono. Creiamo un componente che mostra un profilo utente caricato da un'API.

**Prompt:**

> Agisci come un esperto di React 19 e Next.js 15.
> Crea un Server Component chiamato `UserProfile` che fa il fetch di dati da `https://api.example.com/users/{userId}`.
> 
> Requisiti:
> 1.  Il componente deve accettare una prop `userId` (stringa).
> 2.  Usa la `fetch` API nativa per chiamare l'endpoint.
> 3.  **Usa il nuovo hook `use` di React 19 per gestire la Promise restituita dalla fetch.**
> 4.  Mostra il nome e l'email dell'utente. Non preoccuparti della gestione degli errori per ora.
> 5.  Assicurati che il componente sia un Server Component (nessuna direttiva `'use client'`).

**Output Generato (previsto):**

```typescript
import type { FC } from 'react';
import { use } from 'react';

interface User {
  id: string;
  name: string;
  email: string;
}

// Nota: In un'app reale, questa funzione di fetch sarebbe in un file separato.
async function fetchUser(userId: string): Promise<User> {
  const res = await fetch(`https://api.example.com/users/${userId}`);
  if (!res.ok) {
    throw new Error('Failed to fetch user');
  }
  return res.json();
}

interface UserProfileProps {
  userId: string;
}

const UserProfile: FC<UserProfileProps> = ({ userId }) => {
  // React 19: l'hook 'use' sospende il rendering finché la promise non si risolve.
  const user = use(fetchUser(userId));

  return (
    <div className="rounded-lg border bg-card p-6">
      <h3 className="text-xl font-semibold">{user.name}</h3>
      <p className="text-muted-foreground">{user.email}</p>
    </div>
  );
};

export default UserProfile;

// Da ricordare: Questo componente deve essere renderizzato dentro un <Suspense> boundary.
// Esempio: <Suspense fallback={<p>Loading...</p>}><UserProfile userId="123" /></Suspense>
```

**Analisi:**
Questo è un esempio eccellente di come l'IA sia aggiornata sulle ultime feature. Ha generato un Server Component che usa `use` per un data fetching pulito e dichiarativo, eliminando la necessità di `useEffect`, `useState` e `loading` booleans. Questo codice è più semplice da leggere e manutenere, e l'IA ci guida verso le best practice più moderne.

---

[< Indietro (Setup)](./01-setup-del-workflow.md) | [Torna all'Indice](./index.md) | [Avanti (Hooks e Dati) >](./03-hooks-e-gestione-dati.md)
