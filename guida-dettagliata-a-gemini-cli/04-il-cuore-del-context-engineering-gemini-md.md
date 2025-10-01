# 4. Il Cuore del Context Engineering: `GEMINI.md`

Se i comandi `@`, `!` e `/` sono il modo in cui _tu_ parli con Gemini CLI, il file `GEMINI.md` è il modo in cui il tuo _progetto_ parla con Gemini CLI. Questo capitolo è il più importante della guida, perché svela la funzionalità che trasforma Gemini da un semplice assistente a un vero e proprio membro del team, consapevole del contesto del progetto.

## Cos'è il Contesto Istruzionale (Instructional Context)?

Immagina di dover spiegare a un nuovo collega le regole di un progetto: lo stile del codice, le librerie da usare, l'architettura da seguire, ecc. Ripeteresti queste istruzioni ogni volta che gli chiedi qualcosa? Ovviamente no. Gli daresti un documento di onboarding.

Il **Contesto Istruzionale** (o "memoria") di Gemini CLI funziona esattamente così. È un insieme di istruzioni, linee guida e informazioni di base che vengono **automaticamente pre-caricate nel prompt** a ogni tua richiesta. Questo permette all'agente di avere sempre presente il contesto del progetto, senza che tu debba ripeterlo ogni volta.

Lo strumento principale per fornire questo contesto è un file Markdown, che per convenzione si chiama `GEMINI.md`.

## Il Sistema di Memoria Gerarchica

La vera potenza di Gemini CLI risiede nel suo sistema di **memoria gerarchica**. La CLI non cerca un solo file `GEMINI.md`, ma ne cerca molteplici in posizioni diverse e li concatena in un ordine di precedenza logico. Questo ti permette di avere istruzioni globali, a livello di progetto e persino a livello di singola componente.

L'ordine di caricamento è il seguente:

1.  **Contesto Globale (Utente)**

    - **Posizione**: `~/.gemini/GEMINI.md` (nella tua home directory).
    - **Scopo**: Contiene le tue istruzioni personali che valgono per **tutti** i tuoi progetti. Esempio: "Rispondi sempre in italiano", "Preferisco commenti JSDoc per tutte le funzioni".

2.  **Contesto di Progetto (Root e Antenati)**

    - **Posizione**: La CLI cerca un file `GEMINI.md` nella directory di lavoro corrente e in tutte le directory "antenate", fino alla radice del progetto (identificata da una cartella `.git`) o alla tua home directory.
    - **Scopo**: Fornisce il contesto principale per l'intero progetto. Qui si definiscono lo stile del codice, le tecnologie usate, gli obiettivi del software, ecc.

3.  **Contesto Locale (Sotto-directory)**
    - **Posizione**: La CLI cerca file `GEMINI.md` anche nelle sotto-directory rispetto a dove ti trovi.
    - **Scopo**: Permette di dare istruzioni iper-specifiche per una particolare parte del progetto. Ad esempio, un `GEMINI.md` dentro `src/api/` potrebbe contenere regole solo per la gestione delle chiamate API.

Il contenuto di tutti i file trovati viene unito e passato al modello. Puoi vedere quanti file di contesto sono stati caricati guardando l'indicatore nella barra di stato della CLI.

## Esempio Pratico: un `GEMINI.md` per un Progetto React

Ecco come potrebbe apparire un `GEMINI.md` alla radice di un progetto React + TypeScript:

```markdown
# Contesto Progetto: My Awesome App

## Istruzioni Generali

- Questo è un progetto React scritto in TypeScript.
- Lo stile del codice segue le regole di Prettier e ESLint configurate nel progetto.
- Usa componenti funzionali e Hooks. Evita i class components.
- Per lo stato globale, utilizziamo Zustand. Non introdurre Redux.

## Stile e Convenzioni

- **Componenti**: I nomi dei file e dei componenti devono essere in PascalCase (es. `MyButton.tsx`).
- **Tipi**: Le interfacce per le props dei componenti devono essere chiamate `I[NomeComponente]Props` (es. `IMyButtonProps`).
- **Styling**: Usiamo Styled Components. Ogni componente deve avere il suo file di stile associato (es. `MyButton.styles.ts`).

## Componente Specifico: `src/components/UserProfile`

- Questo componente gestisce la visualizzazione dei dati utente.
- Deve essere robusto e gestire il caso in cui l'utente non sia loggato o i dati siano in caricamento, mostrando uno spinner o un messaggio appropriato.
```

## Gestire la Memoria in Tempo Reale: Il Comando `/memory`

La CLI ti dà pieno controllo sul contesto istruzionale anche durante una sessione interattiva, tramite il comando `/memory`.

- **/memory show**
  Mostra l'intero contesto attualmente caricato, concatenato da tutti i file `GEMINI.md` trovati. È utilissimo per fare debugging e capire esattamente quali istruzioni sta ricevendo il modello.

- **/memory refresh**
  Se modifichi un file `GEMINI.md` mentre la CLI è in esecuzione, le modifiche non verranno recepite automaticamente. Usa questo comando per forzare un ricaricamento di tutti i file di contesto.

- **/memory add <testo da aggiungere>...**
  Aggiunge un'istruzione al contesto solo per la sessione corrente. È utile per dare istruzioni temporanee senza dover modificare un file.

Nel prossimo capitolo, vedremo come Gemini CLI passa dalle istruzioni all'azione, esplorando l'uso dei tool e il function calling.

---

[< 3. Comandi Essenziali e Interfaccia](./03-comandi-essenziali-e-interfaccia.md) | [Torna all'Indice](./index.md) | [5. Tool Use e Function Calling >](./05-tool-use-e-function-calling.md)
