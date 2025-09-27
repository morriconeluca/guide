# 7. La Tua Libreria di Prompt per il Frontend

Questo capitolo è diverso dagli altri. È una raccolta di prompt pronti all'uso, testati e ottimizzati per i task più comuni di uno sviluppatore frontend. Consideralo il tuo "cheat sheet" personale. Copia, incolla e adatta questi template alle tue esigenze.

La struttura di un buon prompt spesso include:

1.  **Ruolo:** `Agisci come...`
2.  **Contesto:** `Sto lavorando su un'app Next.js 15 con TypeScript e Tailwind...`
3.  **Task:** `Crea un componente...`, `Scrivi un test...`, `Fai il refactoring...`
4.  **Requisiti/Vincoli:** Elenco puntato di requisiti specifici.
5.  **Esempio (Opzionale):** `Ecco un esempio di...`

---

### 1. Generazione di Componenti

#### Prompt per un Componente UI Generico

> Agisci come un senior React/Next.js developer.
> Crea un componente chiamato `[NomeComponente]`.
> 
> **Contesto Tecnologico:**
> - React 19, Next.js 15, TypeScript, Tailwind CSS.
> - Il componente deve essere un Server Component di default, a meno che non sia necessaria interattività.
> 
> **Requisiti Funzionali e di Stile:**
> - [Descrivi l'aspetto e il comportamento del componente. Esempio: "Deve essere una card con un'immagine in alto, un titolo, un paragrafo di testo e una lista di tag in basso."]
> - [Specifica le props che deve accettare. Esempio: "Deve accettare `imageUrl`, `title`, `description` (stringa) e `tags` (array di stringhe).]
> - [Aggiungi eventuali requisiti di accessibilità (a11y). Esempio: "L'immagine deve avere un attributo `alt` derivato dal titolo."]

#### Prompt per un Componente Scheletro (Loading)

> Agisci come un esperto di UX e UI design.
> Crea un componente `SkeletonCard` che serva come placeholder di caricamento per il mio componente `ProductCard`.
> 
> **Descrizione Visiva:**
> - Deve avere la stessa dimensione e layout di base di una `ProductCard`.
> - Al posto dell'immagine, mostra un rettangolo grigio.
> - Al posto del titolo e della descrizione, mostra delle barre grigie di lunghezza diversa.
> - Usa classi di Tailwind CSS e aggiungi un'animazione `animate-pulse` per dare un effetto di caricamento.

---

### 2. Hooks e Logica

#### Prompt per un Custom Hook

> Agisci come un esperto di React Hooks.
> Scrivi un custom hook `useDebounce` in TypeScript.
> 
> **Requisiti:**
> 1.  L'hook deve accettare due argomenti: `value` (il valore da debouncare) e `delay` (un numero in millisecondi).
> 2.  Deve restituire il valore "debouncato" (ovvero, il valore di `value` solo dopo che non è cambiato per `delay` millisecondi).
> 3.  Includi una documentazione TSDoc chiara che spieghi come usarlo.

---

### 3. Testing

#### Prompt per Generare un File di Test

> Agisci come un ingegnere QA specializzato in test frontend.
> Ecco il codice per il mio componente React `[NomeComponente]`:
> 
> ```tsx
> [Incolla qui il codice del tuo componente]
> ```
> 
> Scrivi un file di test (`[NomeComponente].test.tsx`) usando Vitest e React Testing Library.
> 
> **Scenari da coprire:**
> 1.  Verifica che il componente renderizzi correttamente con le props di base.
> 2.  [Descrivi un'interazione utente. Esempio: "Simula il click su un bottone e verifica che venga chiamato l'handler `onClick`."]
> 3.  [Descrivi uno stato specifico. Esempio: "Verifica che, passando la prop `disabled`, il bottone non sia cliccabile."]

---

### 4. Documentazione

#### Prompt per Scrivere la Documentazione di un Componente

> Agisci come un technical writer.
> Ecco il codice per il mio componente React `[NomeComponente]`:
> 
> ```tsx
> [Incolla qui il codice del tuo componente]
> ```
> 
> Scrivi la documentazione per questo componente in formato Markdown. La documentazione deve includere:
> 1.  Una breve descrizione dello scopo del componente.
> 2.  Una tabella che elenca tutte le `props`, il loro tipo, se sono obbligatorie e una breve descrizione.
> 3.  Un esempio di utilizzo base in JSX/TSX.

---

### 5. Refactoring

#### Prompt per Refactoring (es. da JS a TS)

> Agisci come un senior TypeScript developer.
> Ho questo componente React scritto in JavaScript con `PropTypes`. Fai il refactoring completo del file in TypeScript.
> 
> ```jsx
> [Incolla qui il codice del componente JavaScript]
> ```
> 
> **Requisiti del Refactoring:**
> 1.  Converti i `PropTypes` in un'interfaccia TypeScript per le props.
> 2.  Aggiungi i tipi a tutti gli stati (`useState`), agli handler di eventi e ai parametri delle funzioni.
> 3.  Assicurati che il codice finale sia type-safe e segua le best practice di TypeScript in React.

---

Costruisci e personalizza la tua libreria partendo da questi template. Salva i prompt che funzionano meglio per te. Con il tempo, diventerà uno degli strumenti più potenti a tua disposizione.

[< Indietro (Testing)](./06-testing-e-debugging.md) | [Torna all'Indice](./index.md)
