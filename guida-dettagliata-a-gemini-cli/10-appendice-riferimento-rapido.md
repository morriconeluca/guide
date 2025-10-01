# 10. Appendice: Riferimento Rapido

Questo capitolo funge da "cheat sheet" per l'uso quotidiano di Gemini CLI. Contiene un riepilogo dei comandi, dei flag e delle variabili d'ambiente più comuni.

## Prefissi di Comando

| Prefisso | Azione                                       |
| :------- | :------------------------------------------- |
| `@`      | Inietta il contenuto di un file o directory. |
| `!`      | Esegue un comando shell.                     |
| `/`      | Esegue un meta-comando della CLI.            |

## Comandi `/` più Comuni

| Comando               | Descrizione                                                         |
| :-------------------- | :------------------------------------------------------------------ |
| `/help`               | Mostra l'aiuto con tutti i comandi.                                 |
| `/clear` (o `Ctrl+L`) | Pulisce lo schermo del terminale.                                   |
| `/quit` (o `Ctrl+C`)  | Esce da Gemini CLI.                                                 |
| `/copy`               | Copia l'ultimo output negli appunti.                                |
| `/chat <sub-cmd>`     | Gestisce le sessioni (`save`, `resume`, `list`, `delete`, `share`). |
| `/memory <sub-cmd>`   | Gestisce il contesto istruzionale (`show`, `refresh`, `add`).       |
| `/tools`              | Lista i tool disponibili.                                           |
| `/settings`           | Apre l'editor delle impostazioni.                                   |
| `/restore`            | Ripristina i file da un checkpoint.                                 |
| `/mcp`                | Ispeziona i server MCP connessi.                                    |

## Flag da Riga di Comando più Comuni

| Flag                               | Alias | Descrizione                                        |
| :--------------------------------- | :---- | :------------------------------------------------- |
| `--model <nome>`                   | `-m`  | Specifica il modello da usare per la sessione.     |
| `--prompt <testo>`                 | `-p`  | Esegue in modalità non-interattiva (deprecato).    |
| `--sandbox`                        | `-s`  | Abilita la modalità sandbox.                       |
| `--yolo`                           | `-y`  | Approva automaticamente tutte le chiamate ai tool. |
| `--output-format <formato>`        | `-o`  | Imposta il formato di output (`text` o `json`).    |
| `--checkpointing`                  | `-c`  | Abilita il checkpointing per le modifiche ai file. |
| `--include-directories <percorsi>` |       | Include directory aggiuntive nel workspace.        |
| `--help`                           | `-h`  | Mostra l'aiuto della riga di comando.              |

## Variabili d'Ambiente Chiave

| Variabile              | Scopo                                                          |
| :--------------------- | :------------------------------------------------------------- |
| `GEMINI_API_KEY`       | Fornisce la API key per l'autenticazione con Gemini AI Studio. |
| `GOOGLE_API_KEY`       | Fornisce la API key per l'autenticazione con Vertex AI.        |
| `GOOGLE_CLOUD_PROJECT` | Specifica l'ID del tuo progetto Google Cloud.                  |
| `GEMINI_MODEL`         | Imposta il modello di default da usare.                        |

---

[< 9. Sicurezza e Gestione Enterprise](./09-sicurezza-e-gestione-enterprise.md) | [Torna all'Indice](./index.md)
