# 8. Esempio Pratico: Creare un Comando Custom

Nel Capitolo 7 abbiamo introdotto il concetto di comandi personalizzati come un modo per automatizzare e riutilizzare prompt complessi. Questo capitolo ti guiderà passo-passo nella creazione di due comandi custom pratici, dimostrando come sfruttare al meglio le funzionalità di iniezione di argomenti, output di shell e contenuto di file.

## Concetto di Comando Custom (Ripasso)

Ricorda che un comando custom è essenzialmente un prompt predefinito salvato in un file `.toml`. Gemini CLI lo espande e lo invia al modello quando invochi il comando. Questi file `.toml` possono risiedere in due posizioni:

- **Comandi Utente (Globali)**: `~/.gemini/commands/` (disponibili in tutti i progetti).
- **Comandi di Progetto (Locali)**: `<root_progetto>/.gemini/commands/` (specifici per il progetto, sovrascrivono quelli globali).

Il nome del comando è determinato dal percorso del file (es. `git/commit.toml` diventa `/git:commit`).

## Esempio Pratico 1: Generare un Messaggio di Commit Git (`/git:commit`)

Questo è un esempio classico e molto utile: un comando che genera un messaggio di commit in formato Conventional Commits basandosi sulle modifiche staged nel tuo repository Git.

### Obiettivo

Creare un comando `/git:commit` che legga il `git diff --staged` e chieda a Gemini di generare un messaggio di commit.

### Passo 1: Creazione della Directory e del File

Per creare un comando globale, assicurati che la directory `~/.gemini/commands/` esista. Poi, crea una sottodirectory `git` per organizzare i comandi relativi a Git e, infine, il file `commit.toml`.

```bash
mkdir -p ~/.gemini/commands/git
touch ~/.gemini/commands/git/commit.toml
```

### Passo 2: Contenuto del File `.toml`

Apri il file `~/.gemini/commands/git/commit.toml` con il tuo editor preferito e incolla il seguente contenuto:

```toml
description = "Genera un messaggio di commit Conventional basato sulle modifiche staged."
prompt = '''
Per favore, genera un messaggio di commit formattato secondo lo standard Conventional Commits, basandoti sul seguente git diff:

!{git diff --staged}

Il messaggio dovrebbe essere conciso, descrittivo e seguire il formato `type(scope): subject`.
'''
```

**Analisi del Contenuto:**

- `description`: Una breve descrizione che apparirà nel menu `/help`.
- `prompt`: Il cuore del comando. Contiene le istruzioni per Gemini.
- `!{git diff --staged}`: Questa è la magia. La sintassi `!{...}` dice a Gemini CLI di **eseguire il comando shell** `git diff --staged` e di **iniettare il suo output** direttamente in questa posizione nel prompt. Questo fornisce a Gemini il contesto esatto delle modifiche da committare.

### Passo 3: Utilizzo del Comando

1.  **Prepara le modifiche:** Assicurati di avere delle modifiche staged nel tuo repository Git.
    `bash
git add .
    `
2.  **Invoca il comando in Gemini CLI:**
    ```
    > /git:commit
    ```

Gemini CLI eseguirà `git diff --staged`, inietterà il risultato nel prompt e lo invierà al modello. Riceverai un messaggio di commit generato, pronto per essere copiato e usato.

## Esempio Pratico 2: Code Review con Linee Guida (`/review`)

Questo comando dimostra come iniettare il contenuto di un file di linee guida e il file da revisionare, usando gli argomenti passati dall'utente.

### Obiettivo

Creare un comando `/review <file_path>` che esegua una code review di un file specifico, basandosi su un file di best practice.

### Passo 1: Creazione del File di Linee Guida

Crea un file `docs/best-practices.md` nella root del tuo progetto (o in una posizione simile) con alcune linee guida di esempio:

**File**: `docs/best-practices.md`

```markdown
# Linee Guida di Code Review

- **Chiarezza**: Il codice deve essere facile da leggere e comprendere.
- **Test**: Ogni funzionalità deve essere coperta da test unitari e di integrazione.
- **Performance**: Evitare cicli o operazioni costose non ottimizzate.
- **Sicurezza**: Attenzione a potenziali vulnerabilità (es. SQL Injection, XSS).
- **Documentazione**: Funzioni complesse e API devono avere commenti chiari.
```

### Passo 2: Creazione della Directory e del File del Comando

Crea il file `review.toml` nella directory dei comandi di progetto (o globali):

```bash
mkdir -p ./.gemini/commands
touch ./.gemini/commands/review.toml
```

### Passo 3: Contenuto del File `.toml`

Apri il file `/.gemini/commands/review.toml` e incolla il seguente contenuto:

```toml
description = "Esegue una code review di un file specifico usando le linee guida del progetto."
prompt = '''
Sei un esperto revisore di codice. Per favore, analizza il seguente file:

{{args}}

Utilizza le seguenti linee guida di best practice per la tua revisione:

@{docs/best-practices.md}

Contenuto del file da revisionare:

@{{args}}

Fornisci un feedback costruttivo, evidenziando aree di miglioramento e potenziali problemi.
'''
```

**Analisi del Contenuto:**

- `{{args}}`: Questo segnaposto verrà sostituito con il percorso del file che l'utente specificherà dopo `/review`.
- `@{docs/best-practices.md}`: La sintassi `@{...}` inietta il contenuto del file `docs/best-practices.md` direttamente nel prompt, fornendo a Gemini le tue linee guida di review.
- `@{{args}}`: Questa combinazione inietta il contenuto del file il cui percorso è stato passato come argomento dall'utente. È un modo potente per rendere il comando dinamico.

### Passo 4: Utilizzo del Comando

1.  **Assicurati che il file esista:** Crea un file di codice di esempio, ad esempio `src/my_module.py`.
2.  **Invoca il comando in Gemini CLI:**
    ```
    > /review src/my_module.py
    ```

Gemini CLI inietterà le tue linee guida e il contenuto di `src/my_module.py` nel prompt, chiedendo al modello di eseguire la review.

## Vantaggi dei Comandi Custom

Questi esempi dimostrano come i comandi custom trasformino Gemini CLI in uno strumento estremamente versatile:

- **Automazione**: Riduci task ripetitivi a un singolo comando.
- **Riusabilità**: Condividi i comandi all'interno del team o tra i tuoi progetti.
- **Coerenza**: Assicurati che l'IA segua sempre le stesse istruzioni e contesti per task specifici.
- **Potenza**: Combina comandi shell, iniezione di file e logica di prompt complessa in un'unica soluzione.

Nel prossimo capitolo, esploreremo come estendere ulteriormente Gemini CLI tramite il Model Context Protocol (MCP) e le Extensions.

---

[< 7. Workflow Avanzati e Scripting](./07-workflow-avanzati-e-scripting.md) | [Torna all'Indice](./index.md) | [9. Estendere Gemini CLI: MCP e Extensions >](./09-estendere-gemini-cli-mcp-e-extensions.md)
