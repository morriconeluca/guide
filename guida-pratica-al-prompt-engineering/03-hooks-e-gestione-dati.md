# 3. Hooks Custom e Gestione Dati

Un componente non è solo UI, è anche logica. In questo capitolo, vedremo come usare l'IA per estrarre e gestire la logica di business e di data-fetching, creando codice più pulito, manutenibile e riutilizzabile. Ci concentreremo su tre task da senior developer: creare un custom hook, fare refactoring di "prop drilling" e integrare una libreria di data-fetching.

---

### Caso 1: Creazione di un Custom Hook

I custom hooks sono il modo migliore in React per estrarre e riutilizzare logica stateful. Chiediamo all'IA di crearne uno molto comune: `useMediaQuery`, per reagire ai cambi di viewport.

**Prompt:**

> Agisci come un senior React developer.
> Crea un custom hook chiamato `useMediaQuery` in un file `useMediaQuery.ts`.
>
> Requisiti:
>
> 1.  L'hook deve accettare una stringa con una media query CSS (es. `'(min-width: 768px)'`).
> 2.  Deve restituire un booleano che indica se la query è attualmente soddisfatta.
> 3.  Deve funzionare correttamente anche in un ambiente di Server-Side Rendering (SSR) come Next.js, senza errori.
> 4.  Deve aggiornare il suo stato ogni volta che la viewport cambia.
> 5.  Deve gestire correttamente la pulizia degli event listener per evitare memory leak.

**Output Generato (previsto):**

```typescript
// hooks/useMediaQuery.ts
'use client';

import { useState, useEffect } from 'react';

export function useMediaQuery(query: string): boolean {
  const [matches, setMatches] = useState(false);

  useEffect(() => {
    // window non è definito sul server, quindi eseguiamo questo codice solo sul client
    const mediaQueryList = window.matchMedia(query);

    const handleChange = (event: MediaQueryListEvent) => {
      setMatches(event.matches);
    };

    // Imposta lo stato iniziale
    handleChange(mediaQueryList);

    // Aggiungi il listener
    mediaQueryList.addEventListener('change', handleChange);

    // Funzione di cleanup per rimuovere il listener
    return () => {
      mediaQueryList.removeEventListener('change', handleChange);
    };
  }, [query]);

  return matches;
}
```

**Analisi: Due Errori in Uno**

A prima vista, l'hook sembra quasi corretto. Tuttavia, un occhio esperto noterà che questo codice nasconde non uno, ma ben due problemi.

1.  **L'Errore di Tipo (Logica):** Il primo, più evidente, è un errore di tipo. La funzione `handleChange` si aspetta un `MediaQueryListEvent`, ma viene chiamata con `handleChange(mediaQueryList)`, che è di tipo `MediaQueryList`. È un errore logico che un compilatore TypeScript segnalerebbe e che dimostra una comprensione superficiale dell'API `matchMedia`.

2.  **L'Errore Nascosto (Hydration):** Il secondo è più sottile e specifico per l'ambiente SSR. Anche correggendo l'errore di tipo, l'approccio di base rimane fallace.
    - **Sul Server**: `useState(false)` imposta il valore a `false`. Il server genera l'HTML con questo presupposto.
    - **Sul Client**: `useEffect` viene eseguito dopo il primo render. Se la media query dell'utente è `true`, `setMatches(true)` viene chiamato, causando un secondo render immediato.
    - **Discrepanza**: React confronta l'HTML del server (basato su `false`) con il render del client (basato su `true`) e rileva una discrepanza, generando il famoso warning di "hydration mismatch" in console.

L'IA ha prodotto un codice che non solo contiene un errore di logica, ma fallisce anche nel requisito più complesso: essere veramente compatibile con l'SSR.

### Come Guidare l'IA alla Correzione Definitiva

Per ottenere una soluzione veramente robusta, dobbiamo essere più specifici e guidare l'IA verso le API moderne di React, pensate proprio per questi scenari.

**Prompt di Revisione (Avanzato):**

> Agisci come un esperto di React 19 e Next.js 15. Il seguente hook `useMediaQuery` causa un errore di tipo e un "hydration mismatch".
>
> Refattorizzalo usando l'hook `useSyncExternalStore` per creare una soluzione robusta, compatibile con il concurrent rendering e che non produca warning. Spiega brevemente perché `useSyncExternalStore` è lo strumento corretto per questo caso.

Questo prompt non chiede solo di "correggere", ma indica lo strumento da usare (`useSyncExternalStore`), dimostrando una conoscenza più profonda e costringendo l'IA a generare il codice migliore possibile.

### Il Codice Corretto e Moderno

Ecco la soluzione che un senior developer implementerebbe oggi, usando le API più recenti di React.

```typescript
// hooks/useMediaQuery.ts - VERAMENTE CORRETTO
'use client';

import { useSyncExternalStore } from 'react';

// Funzione che si sottoscrive ai cambiamenti dell'API del browser.
// Esegue il `callback` ricevuto ogni volta che l'evento 'change' viene emesso.
function subscribe(callback: () => void) {
  window.addEventListener('change', callback);
  return () => {
    window.removeEventListener('change', callback);
  };
}

// Funzione che legge il valore attuale "dallo store" (la nostra media query).
// Viene usata sul client per ottenere il valore reale in modo sicuro.
function getSnapshot(query: string): boolean {
  return window.matchMedia(query).matches;
}

// Funzione che restituisce il valore da usare durante il Server-Side Rendering.
// Poiché il server non conosce la viewport, restituiamo un default sicuro.
function getServerSnapshot(): boolean {
  return false;
}

export function useMediaQuery(query: string): boolean {
  // useSyncExternalStore è l'hook di React 18 per sottoscrivere a fonti esterne
  // in modo sicuro per l'SSR e il concurrent rendering.
  // Prende 3 argomenti:
  // 1. subscribe: come iscriversi e disiscriversi agli eventi.
  // 2. getSnapshot: come leggere il valore attuale sul client.
  // 3. getServerSnapshot: come leggere il valore iniziale sul server.
  const matches = useSyncExternalStore(
    subscribe,
    () => getSnapshot(query),
    getServerSnapshot
  );

  return matches;
}
```

**Analisi Finale:**
Questo caso è emblematico. Dimostra che l'interazione con l'IA non è un processo a senso unico. Il primo output, sebbene apparentemente funzionale, era difettoso a più livelli. La nostra analisi critica (l'errore di tipo) e la conoscenza di problemi più profondi (l'hydration) ci hanno permesso di creare un prompt di revisione mirato.

Il risultato finale, basato su `useSyncExternalStore`, non è solo una "correzione". È una soluzione di livello superiore: più semplice, più dichiarativa e costruita usando gli strumenti che React stesso fornisce per risolvere questa esatta classe di problemi. Questo è il vero valore aggiunto di un developer senior nel workflow con l'IA: non solo generare codice, ma guidare l'IA verso l'eccellenza architetturale.

---

### Caso 2: Refactoring di "Prop Drilling"

Il "prop drilling" (passare props attraverso molti livelli di componenti) è un noto anti-pattern in React. Vediamo come l'IA può aiutarci a risolverlo con la Context API.

**Prompt:**

> Agisci come un esperto di design pattern in React.
> Ho un problema di "prop drilling". Ecco il codice:
>
> ```tsx
> // GrandChild.tsx
> const GrandChild = ({ theme }) => <p>Il tema è {theme}</p>;
>
> // Child.tsx
> const Child = ({ theme }) => <GrandChild theme={theme} />;
>
> // Parent.tsx
> const Parent = () => {
>   const [theme, setTheme] = useState('light');
>   return <Child theme={theme} />;
> };
> ```
>
> Refattorizza questo codice per usare la React Context API ed eliminare il prop drilling. Crea un `ThemeContext`, un `ThemeProvider` e mostra come `GrandChild` può consumare il contesto direttamente.

**Output Generato (previsto):**

```typescript
// 1. Creazione del Contesto (contexts/ThemeContext.tsx)
'use client';

import { createContext, useContext, useState, ReactNode } from 'react';

const ThemeContext = createContext<{ theme: string }>({ theme: 'light' });

export const ThemeProvider = ({ children }: { children: ReactNode }) => {
  const [theme, setTheme] = useState('light');
  return (
    <ThemeContext.Provider value={{ theme }}>{children}</ThemeContext.Provider>
  );
};

export const useTheme = () => useContext(ThemeContext);

// 2. Refactoring dei Componenti

// GrandChild.tsx (ora consuma il contesto)
const GrandChild = () => {
  const { theme } = useTheme();
  return <p>Il tema è {theme}</p>;
};

// Child.tsx (ora non ha più bisogno di props)
const Child = () => <GrandChild />;

// Parent.tsx (ora wrappa tutto nel Provider)
const Parent = () => {
  return (
    <ThemeProvider>
      <Child />
    </ThemeProvider>
  );
};
```

**Analisi: L'Importanza della Supervisione Umana**

A prima vista, si potrebbe concludere che "L'IA ha eseguito un refactoring perfetto". E in un certo senso, è vero: ha capito l'intento di usare la Context API e ha eliminato il "prop drilling".

**Tuttavia, questo è un esempio magistrale di dove la supervisione di uno sviluppatore esperto è fondamentale.** Il codice generato è **sintatticamente corretto ma funzionalmente incompleto**.

Il problema, che un'analisi più attenta rivela, è che il `ThemeProvider` crea la funzione `setTheme` ma non la rende mai disponibile al resto dell'app. Questo crea uno stato "leggi e non scrivere", rendendo impossibile cambiare il tema. L'IA ha risolto il problema specifico del prompt ("eliminare il prop drilling") ma non ha colto l'intento più ampio di creare un provider di tema _funzionale_.

Questo non è un fallimento dell'IA, ma una dimostrazione del suo attuale modo di operare: ottimizza per il compito immediato che le viene assegnato.

### Come Guidare l'IA a Trovare l'Errore

Come si può guidare l'IA a riconoscere un errore di questo tipo? Con un prompt di follow-up ben costruito. Immaginiamo di presentare il codice generato all'IA con una nuova richiesta.

**Prompt di Follow-up (Esempio):**

> Agisci come un senior React developer che sta facendo una code review.
> Analizza il seguente snippet di codice per il `ThemeProvider`. C'è qualcosa di sbagliato o mancante dal punto di vista funzionale? Spiega il tuo ragionamento passo dopo passo.
>
> ```typescript
> // [Incollare qui il codice del ThemeProvider generato]
> ```

Questo tipo di prompt è efficace perché usa diverse tecniche chiave:

1.  **Assegnazione di Ruolo Specifica**: `Agisci come un senior React developer che sta facendo una code review`. Questo imposta un contesto di analisi critica, spingendo l'IA a cercare problemi non evidenti.
2.  **Domanda Mirata**: `C'è qualcosa di sbagliato o mancante dal punto di vista funzionale?`. La domanda non si limita alla sintassi, ma si concentra sulla _funzionalità_, guidando l'IA a pensare all'uso pratico del codice.
3.  **Richiesta di Ragionamento (Chain-of-Thought)**: `Spiega il tuo ragionamento passo dopo passo`. Questa è la parte più importante. Obbliga l'IA a esporre il suo processo di pensiero, facilitando la scoperta di problemi logici che una valutazione superficiale potrebbe mancare.

Questo processo iterativo di generazione, revisione e interrogazione è il cuore del moderno workflow di sviluppo assistito dall'IA.

### Il Codice Corretto e Funzionale

Ecco come appare il codice dopo aver applicato la correzione emersa dal processo di revisione. La modifica chiave è esporre nel contesto anche una funzione per modificare lo stato.

```typescript
// 1. Creazione del Contesto (contexts/ThemeContext.tsx) - CORRETTO
'use client';

import { createContext, useContext, useState, ReactNode } from 'react';

// Definiamo un tipo più completo per il valore del nostro contesto
interface ThemeContextType {
  theme: string;
  toggleTheme: () => void;
}

// Forniamo valori di default che corrispondono al tipo
const ThemeContext = createContext<ThemeContextType>({
  theme: 'light',
  toggleTheme: () => console.warn('no theme provider'), // Funzione di avviso come default
});

export const ThemeProvider = ({ children }: { children: ReactNode }) => {
  const [theme, setTheme] = useState('light');

  const toggleTheme = () => {
    setTheme((prevTheme) => (prevTheme === 'light' ? 'dark' : 'light'));
  };

  // Ora il value contiene sia il tema corrente che la funzione per cambiarlo
  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
};

export const useTheme = () => useContext(ThemeContext);

// 2. Refactoring dei Componenti (invariato)
// GrandChild.tsx, Child.tsx, e Parent.tsx rimangono come sopra.

// 3. Esempio di componente che ORA PUÒ MODIFICARE il tema
const ThemeSwitcher = () => {
  const { theme, toggleTheme } = useTheme();
  return (
    <button onClick={toggleTheme} className="p-2 border rounded">
      Passa al tema {theme === 'light' ? 'dark' : 'light'}
    </button>
  );
};
```

**Analisi Finale:**
Con questa correzione, il `ThemeProvider` non solo risolve il prop drilling, ma fornisce un sistema completo e funzionale per la gestione del tema. Questo dimostra come l'interazione critica e iterativa tra sviluppatore e IA porti a risultati robusti e pronti per la produzione.

---

### Caso 3: Integrazione di una Libreria di Data-Fetching (SWR)

Il data-fetching manuale con `useEffect` e `useState` è verboso e prono a errori. Chiediamo all'IA di refattorizzare un componente per usare una libreria moderna come `SWR`.

**Prompt:**

> Ho questo componente React che fa data-fetching manuale:
>
> ```tsx
> 'use client';
> import { useEffect, useState } from 'react';
>
> const ProductInfo = ({ productId }) => {
>   const [product, setProduct] = useState(null);
>   const [isLoading, setIsLoading] = useState(true);
>   const [error, setError] = useState(null);
>
>   useEffect(() => {
>     const fetchProduct = async () => {
>       try {
>         setIsLoading(true);
>         const res = await fetch(`/api/products/${productId}`);
>         const data = await res.json();
>         setProduct(data);
>       } catch (err) {
>         setError(err);
>       } finally {
>         setIsLoading(false);
>       }
>     };
>     fetchProduct();
>   }, [productId]);
>
>   if (isLoading) return <p>Caricamento...</p>;
>   if (error) return <p>Errore nel caricamento.</p>;
>   return <div>{product.name}</div>;
> };
> ```
>
> Refattorizza questo componente per usare la libreria `SWR`. Gestisci gli stati di caricamento e di errore usando i valori restituiti dall'hook `useSWR`.

**Output Generato (previsto):**

```typescript
'use client';

import useSWR from 'swr';

const fetcher = (url: string) => fetch(url).then((res) => res.json());

const ProductInfo = ({ productId }: { productId: string }) => {
  const {
    data: product,
    error,
    isLoading,
  } = useSWR(productId ? `/api/products/${productId}` : null, fetcher);

  if (isLoading) return <p>Caricamento...</p>;
  if (error) return <p>Errore nel caricamento.</p>;

  return <div>{product.name}</div>;
};
```

**Analisi:**
Impressionante. L'IA ha ridotto 20 righe di codice a 5. Ha capito che doveva definire una funzione `fetcher`, ha usato correttamente l'hook `useSWR` e ha mappato i valori restituiti (`data`, `error`, `isLoading`) alla UI. Ha anche aggiunto una logica intelligente: la richiesta parte solo se `productId` esiste (`productId ? ... : null`), prevenendo richieste API non necessarie. Questo è un esempio perfetto di come l'IA possa aiutarci a scrivere codice più moderno, conciso e robusto.

---

[< Indietro (Componenti)](./02-generazione-di-componenti-react.md) | [Torna all'Indice](./index.md) | [Avanti (Styling) >](./04-styling-e-animazioni.md)
