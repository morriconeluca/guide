# 3. Hooks Custom e Gestione Dati

Un componente non è solo UI, è anche logica. In questo capitolo, vedremo come usare l'IA per estrarre e gestire la logica di business e di data-fetching, creando codice più pulito, manutenibile e riutilizzabile. Ci concentreremo su tre task da senior developer: creare un custom hook, fare refactoring di "prop drilling" e integrare una libreria di data-fetching.

---

### Caso 1: Creazione di un Custom Hook\n\nI custom hooks sono il modo migliore in React per estrarre e riutilizzare logica stateful. Chiediamo all\'IA di crearne uno molto comune: `useMediaQuery`, per reagire ai cambi di viewport.\n\n**Prompt:**\n\n> Agisci come un senior React developer.\n> Crea un custom hook chiamato `useMediaQuery` in un file `useMediaQuery.ts`.\n> \n> Requisiti:\n> 1.  L\'hook deve accettare una stringa con una media query CSS (es. `\'(min-width: 768px)\'`).\n> 2.  Deve restituire un booleano che indica se la query è attualmente soddisfatta.\n> 3.  Deve funzionare correttamente anche in un ambiente di Server-Side Rendering (SSR) come Next.js, senza errori.\n> 4.  Deve aggiornare il suo stato ogni volta che la viewport cambia.\n> 5.  Deve gestire correttamente la pulizia degli event listener per evitare memory leak.\n\n**Output Generato (previsto):**\n\n```typescript\n// hooks/useMediaQuery.ts\n\'use client\';\n\nimport { useState, useEffect } from \'react\';\n\nexport function useMediaQuery(query: string): boolean {\n  const [matches, setMatches] = useState(false);\n\n  useEffect(() => {\n    // window non è definito sul server, quindi eseguiamo questo codice solo sul client\n    const mediaQueryList = window.matchMedia(query);\n    \n    const handleChange = (event: MediaQueryListEvent) => {\n      setMatches(event.matches);\n    };\n\n    // Imposta lo stato iniziale\n    handleChange(mediaQueryList);\n\n    // Aggiungi il listener\n    mediaQueryList.addEventListener(\'change\handleChange);\n\n    // Funzione di cleanup per rimuovere il listener\n    return () => {\n      mediaQueryList.removeEventListener(\'change\', handleChange);\n    };\n  }, [query]);\n\n  return matches;\n}\n```\n\n**Analisi: L\'Errore Nascosto dell\'Idratazione (Hydration)**\n\nA prima vista, questo hook sembra perfetto. Gestisce lo stato, gli eventi e la pulizia. Tuttavia, in un\'applicazione Next.js (o qualsiasi app con SSR), questo codice nasconde un bug critico: un **errore di idratazione**.\n\n1.  **Sul Server**: `useState(false)` inizializza il valore a `false`. Il server genera l'HTML con questo presupposto.\n2.  **Sul Client**: Durante l\'idratazione, `useEffect` viene eseguito. Se la media query dell\'utente è `true` (es. schermo largo), `setMatches(true)` viene chiamato immediatamente. \n3.  **Discrepanza**: React confronta l\'HTML del server (basato su `false`) con il render del client (basato su `true`) e rileva una discrepanza, causando un errore in console e potenziali problemi di rendering.\n\nL\'IA ha soddisfatto i requisiti letterali del prompt, ma non ha gestito questa sfumatura cruciale dell'ambiente SSR.\n\n### Come Guidare l\'IA alla Correzione\n\nPer far emergere questo problema, è necessario un prompt di revisione che specifichi il contesto di esecuzione.\n\n**Prompt di Revisione (Esempio):**\n\n> Agisci come un esperto di Next.js. Analizza questo hook `useMediaQuery` specificamente per problemi legati al Server-Side Rendering e all\'idratazione. Spiega perché il codice potrebbe causare un \"hydration mismatch\" e fornisci una soluzione robusta.\n\nQuesto prompt mirato costringe l\'IA a considerare il ciclo di vita completo del componente in Next.js, portando alla luce il problema.\n\n### Il Codice Corretto\n\nLa soluzione corretta garantisce che il primo rendering del client sia identico a quello del server, eseguendo l\'aggiornamento dello stato solo dopo che il componente è stato montato.\n\n```typescript\n// hooks/useMediaQuery.ts - CORRETTO\n\'use client\';\n\nimport { useState, useEffect } from \'react\';\n\nexport function useMediaQuery(query: string): boolean {\n  // Stato iniziale `false` sul server e nel primo render del client.\n  const [matches, setMatches] = useState(false);\n\n  useEffect(() => {\n    const mediaQueryList = window.matchMedia(query);\n    \n    // Funzione per aggiornare lo stato\n    const updateMatches = () => setMatches(mediaQueryList.matches);\n\n    // Aggiorna lo stato al primo mount sul client per riflettere lo stato reale\n    updateMatches();\n\n    // Aggiungi il listener per i cambi futuri\n    mediaQueryList.addEventListener(\'change\', updateMatches);\n\n    // Funzione di cleanup\n    return () => {\n      mediaQueryList.removeEventListener(\'change\', updateMatches);\n    };\n  }, [query]);\n\n  return matches;\n}\n```\n\n**Analisi Finale:**\nQuesto caso insegna che quando si generano hook o componenti che interagiscono con le API del browser, è fondamentale considerare (e specificare nel prompt) il contesto SSR per ottenere codice veramente robusto.

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

**Analisi: L'Importanza della Supervisione Umana**

A prima vista, si potrebbe concludere che "L'IA ha eseguito un refactoring perfetto". E in un certo senso, è vero: ha capito l'intento di usare la Context API e ha eliminato il "prop drilling".

**Tuttavia, questo è un esempio magistrale di dove la supervisione di uno sviluppatore esperto è fondamentale.** Il codice generato è **sintatticamente corretto ma funzionalmente incompleto**.

Il problema, che un'analisi più attenta rivela, è che il `ThemeProvider` crea la funzione `setTheme` ma non la rende mai disponibile al resto dell'app. Questo crea uno stato "leggi e non scrivere", rendendo impossibile cambiare il tema. L'IA ha risolto il problema specifico del prompt ("eliminare il prop drilling") ma non ha colto l'intento più ampio di creare un provider di tema *funzionale*.

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
2.  **Domanda Mirata**: `C'è qualcosa di sbagliato o mancante dal punto di vista funzionale?`. La domanda non si limita alla sintassi, ma si concentra sulla *funzionalità*, guidando l'IA a pensare all'uso pratico del codice.
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
    setTheme(prevTheme => (prevTheme === 'light' ? 'dark' : 'light'));
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
