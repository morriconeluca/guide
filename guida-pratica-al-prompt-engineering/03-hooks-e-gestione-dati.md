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

**Analisi:**
L'IA ha generato un hook perfetto. Ha capito la necessità di usare `useEffect` per interagire con le API del browser, ha implementato correttamente la funzione di cleanup (fondamentale!) e ha gestito il caso SSR controllando implicitamente l'esistenza dell'oggetto `window`. Il codice è robusto e pronto per la produzione.

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

const ThemeContext = createContext<{ theme: string; }>({ theme: 'light' });

export const ThemeProvider = ({ children }: { children: ReactNode }) => {
  const [theme, setTheme] = useState('light');
  return (
    <ThemeContext.Provider value={{ theme }}>
      {children}
    </ThemeContext.Provider>
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

**Analisi:**
L'IA ha eseguito un refactoring perfetto. Ha capito l'intento, ha creato un file separato per il contesto (best practice), ha implementato un `ThemeProvider` che contiene la logica di stato e ha creato un custom hook `useTheme` per un consumo pulito. Ha poi mostrato come i componenti `Child` e `GrandChild` vengono semplificati. Questo va ben oltre la semplice generazione di codice; è vera comprensione di un design pattern.

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
  const { data: product, error, isLoading } = useSWR(
    productId ? `/api/products/${productId}` : null,
    fetcher
  );

  if (isLoading) return <p>Caricamento...</p>;
  if (error) return <p>Errore nel caricamento.</p>;

  return <div>{product.name}</div>;
};
```

**Analisi:**
Impressionante. L'IA ha ridotto 20 righe di codice a 5. Ha capito che doveva definire una funzione `fetcher`, ha usato correttamente l'hook `useSWR` e ha mappato i valori restituiti (`data`, `error`, `isLoading`) alla UI. Ha anche aggiunto una logica intelligente: la richiesta parte solo se `productId` esiste (`productId ? ... : null`), prevenendo richieste API non necessarie. Questo è un esempio perfetto di come l'IA possa aiutarci a scrivere codice più moderno, conciso e robusto.

---

[< Indietro (Componenti)](./02-generazione-di-componenti-react.md) | [Torna all'Indice](./index.md) | [Avanti (Styling) >](./04-styling-e-animazioni.md)
