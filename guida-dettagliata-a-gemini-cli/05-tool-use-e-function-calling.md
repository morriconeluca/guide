# 5. Tool Use e Function Calling

Nei capitoli precedenti abbiamo visto come fornire contesto a Gemini CLI. Ora vedremo come l'agente passa dalle istruzioni all'**azione**. Questa è la capacità che lo distingue da un semplice chatbot: la possibilità di usare "tool" (strumenti) per interagire con il tuo ambiente.

Nel mondo degli LLM, questo concetto è spesso chiamato **Function Calling**. Significa che il modello, invece di limitarsi a generare testo, può chiedere di eseguire una funzione specifica (un tool) con determinati argomenti.

## I Tool Integrati

Gemini CLI arriva con una serie di tool fondamentali già pronti all'uso:

- **`read_file` / `read_many_files`**: Leggono il contenuto di uno o più file. Vengono usati implicitamente quando usi il comando `@`.
- **`write_file`**: Scrive (o sovrascrive) un file.
- **`replace`**: Sostituisce una porzione di testo all'interno di un file.
- **`run_shell_command`**: Esegue un comando nella shell di sistema. Viene usato implicitamente con il comando `!`.
- **`google_web_search`**: Esegue una ricerca su Google per ottenere informazioni aggiornate.
- **`web_fetch`**: Recupera il contenuto da uno o più URL.
- **`save_memory`**: Permette all'agente di salvare un'informazione specifica nella sua memoria a lungo termine (ne parleremo in un capitolo futuro).

Puoi vedere l'elenco completo dei tool disponibili in qualsiasi momento con il comando `/tools`.

## Il Flusso di Esecuzione di un Tool

Capire come un tool viene eseguito è fondamentale per comprendere la logica dell'agente e le sue misure di sicurezza.

1.  **Prompt dell'Utente**: Fornisci un'istruzione che richiede un'azione. Esempio: `"Leggi il file package.json e dimmi quali sono le dipendenze principali."`
2.  **Decisione del Modello**: Il modello Gemini analizza il prompt e capisce che per rispondere deve leggere un file. Decide quindi di usare il tool `read_file` con l'argomento `"package.json"`.
3.  **Richiesta di Approvazione (Security Prompt)**: Questa è una fase **critica**. Prima di eseguire qualsiasi azione che possa modificare il tuo sistema o accedere a dati, Gemini CLI ti mostra esattamente cosa sta per fare e ti chiede l'approvazione. Vedrai un messaggio simile a: `Tool Call: read_file(path='package.json')`. Devi approvare (solitamente premendo `y`) per continuare.
4.  **Esecuzione del Tool**: Una volta approvato, la CLI esegue il tool. In questo caso, legge il contenuto del file `package.json`.
5.  **Invio del Risultato al Modello**: L'output del tool (il contenuto del file JSON) viene inviato di nuovo al modello Gemini come contesto aggiuntivo.
6.  **Risposta Finale**: Il modello usa l'output del tool per formulare la risposta finale e presentartela in un formato leggibile: `"Le dipendenze principali nel tuo package.json sono: react, react-dom, ..."`.

## Sicurezza: Gestire l'Approvazione dei Tool

La richiesta di approvazione è la tua prima linea di difesa. Tuttavia, per workflow più rapidi e con tool che ritieni sicuri, puoi modificare questo comportamento.

- **Modalità YOLO (`--yolo`)**: L'opzione `yolo` (You Only Live Once) disabilita **tutte** le richieste di approvazione. L'agente eseguirà qualsiasi tool senza chiederti il permesso. Usala con estrema cautela e solo se hai piena fiducia in quello che stai facendo.
- **Tool Pre-approvati (`--allowed-tools`)**: Un approccio più sicuro è specificare una lista di comandi che possono essere eseguiti senza approvazione. Ad esempio, potresti pre-approvare i comandi `git` in sola lettura:
  `gemini --allowed-tools "run_shell_command(git status),run_shell_command(git diff)"`

## La Rete di Sicurezza: Checkpointing e `/restore`

Cosa succede se approvi una modifica di un file (`write_file` o `replace`) e il risultato non è quello che volevi? Gemini CLI ha una rete di sicurezza chiamata **Checkpointing**.

Se abilitata, la CLI salva automaticamente una copia dei file **prima** che un tool li modifichi.

- **Abilitazione**: Puoi attivarla con il flag `--checkpointing` all'avvio o impostando `"general.checkpointing.enabled": true` nel tuo file `settings.json`.
- **Ripristino**: Se qualcosa va storto, puoi annullare l'ultima operazione usando il comando `/restore`. La CLI ti mostrerà un elenco dei checkpoint disponibili e potrai scegliere quale ripristinare, riportando i file al loro stato originale.

Nel prossimo capitolo, vedremo come personalizzare in profondità il comportamento di Gemini CLI attraverso i file di configurazione.

---

[< 4. Il Cuore del Context Engineering: `GEMINI.md`](./04-il-cuore-del-context-engineering-gemini-md.md) | [Torna all'Indice](./index.md) | [6. Configurazione Avanzata: `settings.json` >](./06-configurazione-avanzata-settings-json.md)
