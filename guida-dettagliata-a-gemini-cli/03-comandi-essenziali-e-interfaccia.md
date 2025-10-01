# 3. Comandi Essenziali e Interfaccia

Ora che Gemini CLI è installato e funzionante, è il momento di imparare a interagire con l'agente. L'interfaccia di Gemini CLI è potente perché permette di fare molto più che semplicemente "chattare". In questo capitolo, esploreremo i tre prefissi di comando fondamentali: `@`, `!`, e `/`.

## Anatomia dell'Interfaccia

Quando avvii `gemini`, ti trovi di fronte a un'interfaccia pulita. In basso, c'è la riga di input dove scriverai i tuoi prompt e comandi. La parte superiore è dove appariranno le risposte dell'agente e i risultati dei comandi. In fondo, una barra di stato ti darà informazioni contestuali utili, come il modello in uso o il numero di file di contesto caricati.

## Il Comando `@`: Fornire Contesto

Il comando `@` è forse il più importante per il **Context Engineering**. Il suo scopo è **iniettare il contenuto di file o intere directory direttamente nel tuo prompt**.

Quando usi `@`, Gemini CLI utilizza internamente il tool `read_many_files` per leggere il contenuto specificato e aggiungerlo alla conversazione prima di inviarlo al modello. Questo permette all'agente di "vedere" il tuo codice e rispondere a domande specifiche su di esso.

**Sintassi:**

- `@percorso/del/tuo/file.js Spiega questo codice.`
- `Riassumi il contenuto di questa directory: @src/components/`

**Comportamento Chiave:**

- **File Singolo**: Fornendo il percorso a un file, il suo intero contenuto viene aggiunto al contesto.
- **Directory**: Fornendo il percorso a una directory, la CLI legge ricorsivamente i file al suo interno (escludendo di default i file ignorati da `.gitignore`, come `node_modules` o `.git`).
- **Multimodalità**: Puoi usare `@` anche con file di immagine (PNG, JPG), PDF e altri formati supportati per fare domande multimodali.

## Il Comando `!`: Interagire con la Shell

Il prefisso `!` è il tuo ponte diretto verso la shell del sistema operativo, senza dover uscire da Gemini CLI.

Esistono due modi per usarlo:

### 1. Esecuzione di un Singolo Comando

Fai precedere un qualsiasi comando shell da `!` per eseguirlo istantaneamente. L'output (o l'errore) del comando verrà mostrato nell'interfaccia, e subito dopo potrai continuare a interagire con Gemini.

**Esempi:**

- `!ls -la` (per vedere i file nella directory corrente)
- `!git status` (per controllare lo stato del tuo repository)

### 2. Attivare la "Shell Mode"

Digitando `!` da solo e premendo Invio, attiverai la **modalità shell**. L'interfaccia cambierà leggermente per indicare che sei in questa modalità. Da qui, ogni riga che scriverai sarà interpretata come un comando shell. Per uscire e tornare a Gemini, digita `exit` o premi `Ctrl+D`.

> **Attenzione alla Sicurezza**: I comandi eseguiti con `!` hanno gli stessi permessi dell'utente che ha avviato la CLI. Fai attenzione a quali comandi esegui, specialmente se suggeriti dall'AI.

## I Comandi `/`: Gestire la Sessione

I comandi che iniziano con la barra (`/`) sono "meta-comandi" che controllano il comportamento della CLI stessa. Ce ne sono molti, ma per iniziare questi sono i più importanti:

- **/help** (o `/?`): Mostra la schermata di aiuto con l'elenco di tutti i comandi disponibili.
- **/clear** (o `Ctrl+L`): Pulisce lo schermo del terminale, mantenendo però la cronologia della conversazione in memoria.
- **/copy**: Copia l'ultimo output dell'agente negli appunti del sistema.
- **/quit** (o `/exit`, `Ctrl+C`): Chiude la sessione di Gemini CLI.

Abbiamo appena scalfito la superficie dei comandi `/`. Capitoli successivi esploreranno comandi più avanzati come `/memory`, `/chat`, `/settings` e `/tools`.

Con questi tre tipi di comandi, ora hai gli strumenti base per un'interazione efficace. Nel prossimo capitolo, ci tufferemo nel concetto più potente di Gemini CLI: la gestione del contesto tramite i file `GEMINI.md`.

---

[< 2. Installazione e Primo Avvio](./02-installazione-e-primo-avvio.md) | [Torna all'Indice](./index.md) | [4. Il Cuore del Context Engineering: `GEMINI.md` >](./04-il-cuore-del-context-engineering-gemini-md.md)
