# 5. Integrazione con Next.js 15 (Server-Centric)

Next.js ha rivoluzionato il modo in cui pensiamo il codice React, spostando gran parte del lavoro sul server. Questo non solo migliora le performance, ma apre nuove possibilità per l'interazione con l'IA. In questo capitolo, vedremo come generare codice per le tre feature server-centric più importanti di Next.js 15.

---

### Caso 1: Creare un Server Component per il Data Fetching

I Server Components sono la modalità di default in Next.js. Possono essere `async` e fare data-fetching direttamente, senza `useEffect` o `useState`. Questo li rende incredibilmente snelli e performanti.

**Prompt:**

> Agisci come un esperto di Next.js 15.
> Crea un Server Component chiamato `RepoList` che fa il fetch dei repository più popolari di un utente GitHub.
>
> Requisiti:
>
> 1.  Il componente deve essere `async` e usare la `fetch` API.
> 2.  Deve chiamare l'endpoint `https://api.github.com/users/vercel/repos`.
> 3.  Deve renderizzare una lista non ordinata (`<ul>`) dei nomi dei repository.
> 4.  Includi una gestione base del loading con `<Suspense>` come commento guida.

**Output Generato (previsto):**

```tsx
// app/components/RepoList.tsx
import type { FC } from 'react';

interface Repo {
  id: number;
  name: string;
  html_url: string;
}

async function getRepos(): Promise<Repo[]> {
  const res = await fetch('https://api.github.com/users/vercel/repos', {
    // Next.js estende fetch per il caching
    next: { revalidate: 3600 }, // Cache per 1 ora
  });
  if (!res.ok) {
    throw new Error('Failed to fetch repos');
  }
  return res.json();
}

const RepoList: FC = async () => {
  const repos = await getRepos();

  return (
    <div className="rounded-lg border p-4">
      <h3 className="mb-4 text-lg font-semibold">Vercel Repositories</h3>
      <ul className="list-disc space-y-2 pl-5">
        {repos.slice(0, 10).map((repo) => (
          <li key={repo.id}>
            <a
              href={repo.html_url}
              target="_blank"
              rel="noopener noreferrer"
              className="text-blue-500 hover:underline"
            >
              {repo.name}
            </a>
          </li>
        ))}
      </ul>
    </div>
  );
};

export default RepoList;

/*
  Come usare questo componente in una pagina:
  import { Suspense } from 'react';
  import RepoList from '@/app/components/RepoList';

  export default function Page() {
    return (
      <div>
        <h1>My Page</h1>
        <Suspense fallback={<p>Loading repos...</p>}>
          <RepoList />
        </Suspense>
      </div>
    );
  }
*/
```

**Analisi:**
L'IA ha creato un perfetto Server Component. Ha usato `async/await` direttamente nella funzione del componente, ha aggiunto la configurazione di caching di Next.js alla `fetch` e ha persino incluso un commento che spiega come usare il componente con `<Suspense>`, dimostrando una comprensione profonda dell'ecosistema.

---

### Caso 2: Gestire Mutazioni con le Server Actions

Le Server Actions sono funzioni asincrone che girano sul server ma possono essere chiamate direttamente da componenti client, ideali per le mutazioni di dati (es. form). Eliminano la necessità di creare endpoint API per semplici operazioni.

**Prompt:**

> Crea una Server Action per aggiungere un'email a una newsletter.
>
> Requisiti:
>
> 1.  Crea un file `app/newsletter/actions.ts`.
> 2.  La funzione `subscribeToNewsletter` deve accettare un oggetto `FormData`.
> 3.  Deve estrarre l'email, simulare un salvataggio nel database e restituire un messaggio di successo o errore.
> 4.  Mostra anche un semplice componente form (`NewsletterForm.tsx`) che usa questa action.

**Output Generato (previsto):**

```typescript
// app/newsletter/actions.ts
'use server';

import { z } from 'zod';

export async function subscribeToNewsletter(
  prevState: any,
  formData: FormData
) {
  const schema = z.object({ email: z.string().email() });
  const parsed = schema.safeParse({ email: formData.get('email') });

  if (!parsed.success) {
    return { message: 'Indirizzo email non valido.' };
  }

  console.log(`Salvataggio di ${parsed.data.email} nel database...`);
  // Simula chiamata al DB
  await new Promise((res) => setTimeout(res, 1000));

  return { message: `Grazie per l\'iscrizione, ${parsed.data.email}!` };
}

// app/newsletter/NewsletterForm.tsx
('use client');

import { useFormState, useFormStatus } from 'react-dom';
import { subscribeToNewsletter } from './actions';

function SubmitButton() {
  const { pending } = useFormStatus();
  return (
    <button type="submit" disabled={pending}>
      {pending ? 'Invio...' : 'Iscriviti'}
    </button>
  );
}

export function NewsletterForm() {
  const [state, formAction] = useFormState(subscribeToNewsletter, {
    message: '',
  });

  return (
    <form action={formAction}>
      <input type="email" name="email" placeholder="tua@email.com" required />
      <SubmitButton />
      {state?.message && <p>{state.message}</p>}
    </form>
  );
}
```

**Analisi:**
Questo è un pattern avanzato che l'IA ha gestito perfettamente. Ha creato la Server Action con la direttiva `'use server'`. Ha usato `zod` per la validazione (una best practice). Ha creato un Client Component (`NewsletterForm`) che usa gli hook `useFormState` e `useFormStatus` (introdotti in React 18 e finalizzati in React 19) per una gestione dichiarativa dello stato del form, separando completamente la UI dalla logica server.

---

### Caso 3: Generare un Endpoint API (Route Handler)

Quando hai bisogno di un vero endpoint API (es. per essere consumato da un'app mobile o da un altro servizio), i Route Handlers sono la soluzione.

**Prompt:**

> Crea un Route Handler in Next.js 15 per cercare dei prodotti.
>
> Requisiti:
>
> 1.  Il file deve essere in `app/api/search/route.ts`.
> 2.  Deve gestire le richieste `GET`.
> 3.  Deve leggere un parametro di ricerca `q` dall'URL (es. `/api/search?q=scarpe`).
> 4.  Deve restituire un array di oggetti prodotto in formato JSON.

**Output Generato (previsto):**

```typescript
// app/api/search/route.ts
import { NextResponse, type NextRequest } from 'next/server';

const allProducts = [
  { id: 1, name: 'Scarpe da Corsa' },
  { id: 2, name: 'Scarpe da Trekking' },
  { id: 3, name: 'Zaino da Montagna' },
];

export async function GET(request: NextRequest) {
  const searchParams = request.nextUrl.searchParams;
  const query = searchParams.get('q');

  if (!query) {
    return NextResponse.json(
      { error: 'Il parametro di ricerca q è obbligatorio' },
      { status: 400 }
    );
  }

  const filteredProducts = allProducts.filter((product) =>
    product.name.toLowerCase().includes(query.toLowerCase())
  );

  return NextResponse.json(filteredProducts);
}
```

**Analisi:**
L'IA ha generato un Route Handler perfetto e idiomatico per Next.js 15. Ha usato correttamente `NextRequest` per leggere i parametri dell'URL e `NextResponse` per inviare una risposta JSON, includendo anche la gestione dell'errore 400 se il parametro è mancante. Questo tipo di boilerplate è un task perfetto da delegare all'IA per risparmiare tempo.

---

[< Indietro (Styling)](./04-styling-e-animazioni.md) | [Torna all'Indice](./index.md) | [Avanti (Testing) >](./06-testing-e-debugging.md)
