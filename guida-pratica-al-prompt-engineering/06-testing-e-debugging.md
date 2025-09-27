# 6. Testing e Debugging Assistito

Scrivere test e fare debugging sono due delle attività più dispendiose in termini di tempo. Fortunatamente, l'IA può agire come un instancabile assistente QA e un esperto diagnosta, facendoci risparmiare ore di lavoro.

---

### Caso 1: Generazione di Unit Test con Vitest e React Testing Library

Scrivere la struttura di un test (import, `describe`, `it`, `render`) è un lavoro ripetitivo. Deleghiamolo.

**Il Componente da Testare (`Accordion.tsx`):**

```tsx
'use client';

import { useState } from 'react';

interface AccordionProps {
  title: string;
  children: React.ReactNode;
}

export const Accordion = ({ title, children }: AccordionProps) => {
  const [isOpen, setIsOpen] = useState(false);

  return (
    <div>
      <h3>
        <button onClick={() => setIsOpen(!isOpen)} aria-expanded={isOpen}>
          {title}
        </button>
      </h3>
      {isOpen && <div>{children}</div>}
    </div>
  );
};
```

**Prompt:**

> Agisci come un senior frontend developer esperto in testing.
> Ecco un componente `Accordion` in React.
> 
> ```tsx
> // [Incolla il codice del componente Accordion qui]
> ```
> 
> Scrivi un file di test completo (`Accordion.test.tsx`) per questo componente usando Vitest e React Testing Library.
> 
> Il test deve coprire i seguenti scenari:
> 1.  Il componente renderizza il titolo correttamente.
> 2.  Il contenuto dell'accordion è inizialmente nascosto.
> 3.  Dopo aver cliccato sul bottone del titolo, il contenuto diventa visibile.

**Output Generato (previsto):**

```tsx
// Accordion.test.tsx
import { render, screen, fireEvent } from '@testing-library/react';
import { describe, it, expect } from 'vitest';
import { Accordion } from './Accordion';

describe('Accordion', () => {
  const title = 'Titolo di Test';
  const content = 'Contenuto di Test';

  it('dovrebbe renderizzare il titolo', () => {
    render(<Accordion title={title}>{content}</Accordion>);
    expect(screen.getByText(title)).toBeInTheDocument();
  });

  it('dovrebbe avere il contenuto nascosto inizialmente', () => {
    render(<Accordion title={title}>{content}</Accordion>);
    expect(screen.queryByText(content)).not.toBeInTheDocument();
  });

  it('dovrebbe mostrare il contenuto dopo il click', () => {
    render(<Accordion title={title}>{content}</Accordion>);

    // Controlla che il contenuto sia assente
    expect(screen.queryByText(content)).not.toBeInTheDocument();

    // Simula il click dell'utente
    const button = screen.getByRole('button', { name: title });
    fireEvent.click(button);

    // Ora il contenuto dovrebbe essere visibile
    expect(screen.getByText(content)).toBeInTheDocument();
  });
});
```

**Analisi:**
L'IA ha generato un file di test perfetto. Ha importato le utility corrette, ha strutturato i test in modo leggibile con `describe` e `it`, e ha usato le API di React Testing Library (`screen`, `fireEvent`) nel modo corretto per simulare il comportamento dell'utente. Questo non è solo codice boilerplate; è un test ben scritto che segue le best practice, generato in pochi secondi.

---

### Caso 2: Debugging di un Errore Criptico (Hydration Error)

Gli errori di idratazione in Next.js sono potenti ma spesso difficili da diagnosticare per chi è alle prime armi. Vediamo come l'IA può fare da mentore.

**Lo Scenario:**
Stai sviluppando un componente che dovrebbe mostrare l'ora corrente, ma ricevi un fastidioso errore nella console.

**Il Codice Problematico (`CurrentTime.tsx`):**

```tsx
const CurrentTime = () => {
  const time = new Date().toLocaleTimeString();
  return <p>Ora corrente: {time}</p>;
};
```

**L'Errore nella Console:**
`Warning: Text content did not match. Server: "10:30:55" Client: "10:30:56"`

**Prompt:**

> Agisci come un ingegnere esperto di Next.js.
> Sto ricevendo il seguente errore di idratazione:
> `Warning: Text content did not match. Server: "10:30:55" Client: "10:30:56"`
> 
> Questo è il codice del mio componente:
> ```tsx
> const CurrentTime = () => {
>   const time = new Date().toLocaleTimeString();
>   return <p>Ora corrente: {time}</p>;
> };
> ```
> 
> Spiegami in modo semplice qual è la causa principale di questo errore e forniscimi due soluzioni pratiche per risolverlo.

**Output dell'IA (previsto):**

> Certo, questo è un classico errore di idratazione in Next.js. Analizziamolo.
> 
> **Causa del Problema:**
> Il problema nasce da una discrepanza tra ciò che il server ha generato e ciò che il client ha renderizzato al primo caricamento.
> 1.  **Render del Server:** Quando la pagina viene richiesta, il server Next.js esegue il tuo componente, genera l'HTML con l'ora corrente (es. `10:30:55`) e lo invia al browser.
> 2.  **Render del Client (Idratazione):** Il browser riceve l'HTML. Subito dopo, React si avvia sul client per "idratare" l'HTML, ovvero per renderlo interattivo. Durante questo processo, riesegue il tuo componente. Ma nel frattempo è passato un secondo, e `new Date().toLocaleTimeString()` ora restituisce `10:30:56`.
> 3.  **Discrepanza:** React confronta l'HTML del server (`...55`) con quello che ha appena generato sul client (`...56`), trova una differenza e ti avvisa con quell'errore.
> 
> In breve, non puoi renderizzare contenuti che dipendono da API specifiche del browser (come l'ora esatta) direttamente nel JSX di un componente renderizzato sul server.
> 
> **Soluzioni Pratiche:**
> 
> **Soluzione 1 (Consigliata): Spostare la logica solo sul client con `useEffect`.**
> Fai in modo che il componente renderizzi un valore di default sul server e aggiorni l'ora solo dopo che l'idratazione è completata, usando `useEffect`.
> 
> ```tsx
> 'use client';
> import { useState, useEffect } from 'react';
> 
> const CurrentTime = () => {
>   const [time, setTime] = useState('');
> 
>   useEffect(() => {
>     // Questo codice viene eseguito solo sul client
>     setTime(new Date().toLocaleTimeString());
>   }, []); // L'array vuoto assicura che venga eseguito solo una volta al mount
> 
>   if (!time) return <p>Caricamento ora...</p>; // O un valore di fallback
> 
>   return <p>Ora corrente: {time}</p>;
> };
> ```
> 
> **Soluzione 2 (Per discrepanze minori): `suppressHydrationWarning`.**
> Se la discrepanza è inevitabile e non causa problemi visivi (come un timestamp), puoi dire a React di ignorarla su uno specifico elemento.
> 
> ```tsx
> const CurrentTime = () => {
>   const time = new Date().toLocaleTimeString();
>   return <p>Ora corrente: <span suppressHydrationWarning>{time}</span></p>;
> };
> ```
> Usa questa opzione con cautela, perché nasconde il problema invece di risolverlo alla radice, ma è utile per attributi come `className` che possono variare legittimamente.

**Analisi:**
L'IA non si è limitata a dare una soluzione. Ha agito da mentore: ha spiegato il *perché* dell'errore (il concetto di idratazione e la discrepanza server/client), ha fornito la soluzione più corretta e robusta (`useEffect`) e ha anche menzionato un'alternativa pragmatica (`suppressHydrationWarning`), spiegandone i pro e i contro. Questo tipo di risposta accelera enormemente la curva di apprendimento di un developer.

---

[< Indietro (Next.js)](./05-nextjs-15-server-centric.md) | [Torna all'Indice](./index.md) | [Avanti (Libreria di Prompt) >](./07-libreria-di-prompt.md)
