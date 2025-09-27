# 1. Setup del Workflow AI-Powered

Benvenuto nel primo capitolo pratico. Prima di chiedere all'IA di scrivere il nostro primo componente, dobbiamo assicurarci che parli la nostra stessa lingua e rispetti le regole del nostro team. Un setup corretto è ciò che distingue un aiuto sporadico da un vero co-pilota integrato nel workflow.

L'obiettivo di questo capitolo è configurare il nostro ambiente per garantire che l'IA generi codice:

- **Coerente:** Segue le nostre convenzioni di stile e struttura.
- **Corretto:** Utilizza il nostro stack tecnologico (React 19, Next.js 15, TypeScript).
- **Contestualizzato:** Comprende la struttura del nostro progetto.

---

### L'Editor: VS Code + La Scelta dell'Assistente AI

Useremo Visual Studio Code come editor. La sua integrazione con gli strumenti di IA è, al momento, la più avanzata. Oltre agli indispensabili **ESLint** e **Prettier** per la qualità e la formattazione del codice, la scelta chiave è l'assistente AI. Ecco tre ottime opzioni:

1.  **[GitHub Copilot](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot-chat):** Lo standard di mercato, sviluppato da GitHub/Microsoft. Offre eccellente completamento del codice e un'interfaccia chat (`Cmd/Ctrl + I`) molto potente, con una profonda conoscenza del contesto del progetto.

2.  **[Gemini Code Assist](https://marketplace.visualstudio.com/items?itemName=Google.geminicodeassist):** L'assistente di Google, che sfrutta la potenza dei modelli Gemini. Può essere installato come estensione a sé stante o come parte della suite Google Cloud Code. Offre un'ampia finestra di contesto e feature avanzate per la generazione e l'analisi del codice.

3.  **[Windsurf (formerly Codeium)](https://marketplace.visualstudio.com/items?itemName=Codeium.codeium):** Un'alternativa molto popolare, nota per la sua velocità e per un generoso piano gratuito per sviluppatori individuali. Offre funzionalità di autocomplete e chat molto simili a Copilot e indicizza l'intera codebase per un contesto migliore.

**La scelta dipende dalle tue preferenze e dal tuo budget. Per questa guida, gli esempi di prompt funzioneranno con tutti e tre, ma l'implementazione del "System Prompt" varia leggermente.**

---

### Definire il Contesto del Progetto (Il nostro "System Prompt")

Come facciamo a insegnare all'assistente AI le regole del nostro progetto senza doverle ripetere in ogni prompt? La risposta è creare un "System Prompt", ovvero un set di istruzioni di base che l'IA deve seguire per l'intero progetto.

Questo è il passo più importante per ottenere output coerenti e di alta qualità. Vediamo come implementarlo per ciascun tool.

#### Per GitHub Copilot

Il metodo ufficiale è creare un file qui: `.github/copilot-instructions.md`. Copilot leggerà questo file per avere contesto su ogni tua richiesta.

#### Per Gemini Code Assist

Il metodo è quasi identico. Si crea un file chiamato `GEMINI.md` nella root del progetto. Il suo scopo è lo stesso: fornire istruzioni e contesto a livello di progetto.

#### Per Windsurf (Codeium)

Windsurf indicizza automaticamente l'intera codebase per ottenere contesto. Per istruzioni esplicite, puoi usare la sua interfaccia di Chat per fornire direttive o configurare un "Custom Context" nelle sue impostazioni avanzate, anche se non si basa su un singolo file come gli altri due.

### Template di Istruzioni Universale

Ecco un template solido che puoi usare come base. **Incollalo nel file corretto (`.github/copilot-instructions.md` o `GEMINI.md`) o usalo come riferimento per la chat del tuo assistente.**

````markdown
# Istruzioni AI per il Progetto [Nome Progetto]

Questo documento fornisce le linee guida all'assistente AI per lo sviluppo di questo progetto.

## 1. Stack Tecnologico Principale

- **Framework:** Next.js 15
- **Libreria UI:** React 19
- **Linguaggio:** TypeScript
- **Styling:** Tailwind CSS
- **State Management:** Zustand per il client state, Server Actions per le mutazioni.
- **Data Fetching:** Fetch API nativa all'interno di Server Components e Route Handlers.
- **Testing:** Vitest e React Testing Library.
- **Linting/Formatting:** ESLint e Prettier.

## 2. Convenzioni di Codifica

- **Componenti:** Scrivi sempre componenti funzionali. Usa `PascalCase` per i nomi dei file e dei componenti (es. `PrimaryButton.tsx`).
- **Hooks:** I custom hooks devono iniziare con il prefisso `use` (es. `useAuth.ts`).
- **TypeScript:** Usa tipi espliciti per props e stati. Evita `any`.
- **Styling:** Utilizza esclusivamente le classi di Tailwind CSS. Non usare stili inline o file CSS/SCSS separati.
- **Importazioni:** Organizza le importazioni in questo ordine: built-in di React, librerie esterne, componenti interni, hooks, utility, stili.

## 3. Linee Guida per la Generazione di Codice

- **Componenti React:** Devono essere funzionali e scritti in TypeScript (`.tsx`). Le props devono essere definite con un'interfaccia TypeScript.
- **Next.js:** Privilegia i Server Components di default. Rendi un componente un Client Component (`'use client'`) solo se strettamente necessario (es. se usa hooks come `useState` o `useEffect`).
- **Server Actions:** Per le mutazioni di dati (es. submit di un form), genera una Server Action e usala direttamente nel form del componente.
- **Test:** Quando richiesto di generare test, usa Vitest e React Testing Library. Concentrati sul testare il comportamento percepito dall'utente, non i dettagli di implementazione.

## 4. Esempio di Componente

Questo è un esempio di un componente ben scritto che puoi usare come riferimento per lo stile:

```typescript
import type { FC } from 'react';

interface SimpleCardProps {
  title: string;
  description: string;
}

const SimpleCard: FC<SimpleCardProps> = ({ title, description }) => {
  return (
    <div className="rounded-lg border bg-card text-card-foreground shadow-sm">
      <div className="flex flex-col space-y-1.5 p-6">
        <h3 className="text-2xl font-semibold leading-none tracking-tight">
          {title}
        </h3>
      </div>
      <div className="p-6 pt-0">
        <p className="text-sm text-muted-foreground">{description}</p>
      </div>
    </div>
  );
};

export default SimpleCard;
```
````

Con queste istruzioni impostate, il tuo assistente AI è ora un membro del team molto più consapevole e allineato, indipendentemente da quale hai scelto.

---

[< Indietro (Indice)](./index.md) | [Avanti (Generazione Componenti) >](./02-generazione-di-componenti-react.md)
