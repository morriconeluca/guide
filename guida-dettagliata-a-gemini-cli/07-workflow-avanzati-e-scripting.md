# 7. Workflow Avanzati e Scripting

Nei capitoli precedenti abbiamo imparato a usare Gemini CLI in modalità interattiva. Ora è il momento di fare un passo avanti e scoprire come automatizzare i task, creare i nostri comandi e gestire conversazioni complesse. Questo capitolo è dedicato a trasformare Gemini CLI da uno strumento di dialogo a una vera e propria piattaforma di automazione.

## Scripting e Modalità Non-Interattiva

Puoi eseguire Gemini CLI in modo non-interattivo per integrarlo in script di shell o altri processi automatizzati. Invece di avviare l'interfaccia, puoi passare un prompt direttamente dalla riga di comando.

```bash
# Passa un prompt direttamente alla CLI
gemini "Riassumi il file README.md in tre punti principali @README.md"
```

L'output di default è testo semplice, ma per lo scripting è molto più utile avere un output strutturato. Usando il flag `--output-format json`, la CLI restituirà un oggetto JSON che puoi facilmente parsare con tool come `jq`.

```bash
# Esempio di output JSON per lo scripting
gemini "Qual è la versione di react in questo progetto? @package.json" --output-format json
```

## Creare Comandi Personalizzati: Il Tuo Ricettario di Prompt

I comandi personalizzati sono una delle funzionalità più potenti di Gemini CLI. Ti permettono di salvare prompt complessi e riutilizzabili come semplici comandi, da condividere anche con il tuo team.

I comandi sono file `.toml` che risiedono in directory specifiche:

- **Comandi Utente (Globali)**: In `~/.gemini/commands/`. Disponibili in tutti i tuoi progetti.
- **Comandi di Progetto (Locali)**: In `.gemini/commands/` nella root del tuo progetto. Hanno la precedenza su quelli utente e possono essere committati su Git.

Un comando definito in `.gemini/commands/git/commit.toml` diventerà `/git:commit`.

### Formato di un Comando (`.toml`)

Un comando è definito da due campi principali:

- `description` (opzionale): Una breve descrizione mostrata nel menu `/help`.
- `prompt`: Il prompt effettivo da inviare al modello.

### Rendere i Comandi Dinamici

La vera magia sta nel rendere questi comandi dinamici.

**1. Gestire Argomenti con `{{args}}`**

Usa `{{args}}` nel tuo prompt per inserire qualsiasi testo l'utente scriva dopo il nome del comando.

_Esempio: creare un comando `/review` per revisionare un file._

**File**: `.gemini/commands/review.toml`

```toml
description = "Esegue una code review di un file specifico."
prompt = "'''
Per favore, esegui una code review del file `{{args}}`.
Concentrati su manutenibilità, performance e possibili bug.

Ecco il contenuto del file:
@{{args}}
'''"
```

**Uso**: `> /review src/components/Button.tsx`

**2. Eseguire Shell Commands con `!{...}`**

Puoi iniettare l'output di un comando shell direttamente nel prompt. È perfetto per fornire contesto dinamico.

_Esempio: un comando `/git:commit` che genera un messaggio di commit basato sulle modifiche staged._

**File**: `.gemini/commands/git/commit.toml`

````toml
description = "Genera un messaggio di commit Conventional basato sul git diff."
prompt = "'''
Basandoti sul seguente diff, per favore genera un messaggio di commit formattato secondo lo standard Conventional Commits.

```diff
!{git diff --staged}
````

'''"

````
**Uso**: `> /git:commit` (dopo aver fatto `git add`)

**3. Iniettare File con `@{...}`**

Simile a `!{...}`, puoi usare `@{...}` per iniettare il contenuto di un file il cui percorso è definito *staticamente* nel comando.

*Esempio: un comando `/check:style` che confronta un file con le linee guida del progetto.*

**File**: `.gemini/commands/check/style.toml`
```toml
description = "Controlla un file rispetto alle linee guida di stile del progetto."
prompt = "'''
Valuta il file `{{args}}` e dimmi se rispetta le nostre linee guida di stile.

Linee guida:
@{docs/STYLE_GUIDE.md}

Contenuto del file da analizzare:
@{{args}}
'''"
````

**Uso**: `> /check:style src/utils/helpers.ts`

## Gestire lo Stato della Conversazione con `/chat`

Per task complessi che richiedono più passaggi, potresti voler salvare lo stato di una conversazione per riprenderla in seguito. Il comando `/chat` serve proprio a questo.

- **/chat save `<tag>`**: Salva la cronologia della conversazione attuale con un nome (tag).
- **/chat resume `<tag>`**: Ricarica una conversazione salvata in precedenza, ripristinando l'intero contesto.
- **/chat list**: Mostra tutti i punti di salvataggio (tag) disponibili.
- **/chat delete `<tag>`**: Rimuove un salvataggio.
- **/chat share `<file.md>`**: Esporta l'intera conversazione in un file Markdown, ottimo per la documentazione o per condividerla.

Questi strumenti trasformano Gemini CLI in un ambiente di sviluppo potenziato dall'AI, dove puoi automatizzare, standardizzare e riprendere task complessi con facilità.

---

[< 6. Configurazione Avanzata: `settings.json`](./06-configurazione-avanzata-settings-json.md) | [Torna all'Indice](./index.md) | [Avanti (Esempio Pratico: Comando Custom) >](./08-esempio-pratico-comando-custom.md)
