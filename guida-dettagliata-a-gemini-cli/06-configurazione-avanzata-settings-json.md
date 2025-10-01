# 6. Configurazione Avanzata: `settings.json`

Gemini CLI è uno strumento altamente configurabile. Sebbene funzioni perfettamente "out-of-the-box", puoi personalizzare quasi ogni suo aspetto per adattarlo al tuo workflow. In questo capitolo esploreremo i meccanismi di configurazione, con un focus sul file `settings.json`.

## La Gerarchia della Configurazione

Per evitare sorprese, è fondamentale capire come Gemini CLI decide quale impostazione applicare. Esiste una gerarchia di precedenza, dove i livelli più alti sovrascrivono quelli più bassi:

1.  **Valori di Default**: Impostazioni predefinite nel codice della CLI.
2.  **File di Impostazioni**: File `settings.json` a vari livelli (System, User, Project).
3.  **Variabili d'Ambiente**: Variabili come `GEMINI_MODEL` o `GEMINI_API_KEY`.
4.  **Argomenti da Riga di Comando**: Flag passati all'avvio, come `--model` o `--yolo`.

Questo significa che un flag `--model` usato all'avvio avrà sempre la precedenza su un modello definito in un file `settings.json` o in una variabile d'ambiente.

## I File `settings.json`

Il modo principale per rendere persistente la tua configurazione è attraverso i file `settings.json`. Anche questi seguono una gerarchia:

- **User Settings (Impostazioni Utente)**

  - **Posizione**: `~/.gemini/settings.json`
  - **Scopo**: Contiene le tue impostazioni globali, che si applicano a tutte le sessioni di Gemini CLI, indipendentemente dal progetto in cui ti trovi. È il posto ideale per definire il tuo tema preferito, il modello di default, o l'editor.

- **Project Settings (Impostazioni di Progetto)**
  - **Posizione**: `.gemini/settings.json` (all'interno della directory del tuo progetto).
  - **Scopo**: Contiene impostazioni specifiche solo per quel progetto. **Sovrascrive** le impostazioni utente. È perfetto per definire tool pre-approvati per il progetto, un `GEMINI.md` con un nome custom, o un modello specifico richiesto dal team.

Esistono anche file di configurazione a livello di sistema, ma per l'uso quotidiano, la combinazione Utente + Progetto è la più comune e potente.

## Impostazioni Comuni in `settings.json`

Il file `settings.json` è un file JSON strutturato per categorie. Vediamo alcune delle impostazioni più utili.

- **`general`**: Impostazioni generali di comportamento.

  - `"vimMode": true`: Abilita la navigazione con i tasti di Vim nell'input.
  - `"checkpointing": {"enabled": true}`: Abilita di default la funzione di checkpointing.

- **`ui`**: Personalizzazione dell'interfaccia utente.

  - `"theme": "GitHub"`: Imposta un tema di colori (es. `GitHub`, `Dracula`, `Nord`).
  - `"hideBanner": true`: Nasconde il banner di avvio.

- **`model`**: Impostazioni relative al modello AI.

  - `"name": "gemini-2.5-flash"`: Definisce il modello da usare di default.

- **`context`**: Configurazione del contesto istruzionale.

  - `"fileName": ["CONTEXT.md", "GEMINI.md"]`: Dice alla CLI di cercare anche file chiamati `CONTEXT.md` oltre a `GEMINI.md`.
  - `"fileFiltering": {"respectGitIgnore": false}`: Forza la CLI a includere nel contesto anche i file che sarebbero ignorati da `.gitignore`.

- **`tools`**: Configurazione dei tool.
  - `"sandbox": true`: Abilita di default il sandboxing per i comandi shell.
  - `"allowed": ["run_shell_command(git status)"]`: Pre-approva l'esecuzione del comando `git status` senza chiedere conferma.

### Esempio di `settings.json` Utente

Un tipico file `~/.gemini/settings.json` potrebbe assomigliare a questo:

```json
{
  "general": {
    "vimMode": true,
    "checkpointing": {
      "enabled": true
    }
  },
  "ui": {
    "theme": "Dracula",
    "hideTips": true
  },
  "model": {
    "name": "gemini-2.5-pro-latest"
  }
}
```

## Variabili d'Ambiente

Le variabili d'ambiente sono un altro modo potente per configurare la CLI, e sovrascrivono le impostazioni nei file `settings.json`. Sono particolarmente utili per dati sensibili o per configurazioni temporanee.

Le più importanti sono:

- `GEMINI_API_KEY`: Per l'autenticazione tramite API key.
- `GOOGLE_CLOUD_PROJECT`: Per specificare il tuo progetto Google Cloud.
- `GEMINI_MODEL`: Per specificare il modello da usare (es. `gemini-2.5-flash`).

Puoi definirle nel tuo file di profilo della shell (es. `~/.zshrc`, `~/.bashrc`) o in un file `.env` nella directory del tuo progetto per caricarle automaticamente.

Nel prossimo capitolo, vedremo come usare queste configurazioni per creare workflow avanzati e comandi personalizzati.

---

[< 5. Tool Use e Function Calling](./05-tool-use-e-function-calling.md) | [Torna all'Indice](./index.md) | [7. Workflow Avanzati e Scripting >](./07-workflow-avanzati-e-scripting.md)
