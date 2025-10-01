# 8. Estendere Gemini CLI: MCP e Extensions

I tool integrati e i comandi personalizzati offrono già un'enorme flessibilità. Tuttavia, per sbloccare il vero potenziale di Gemini CLI e integrarlo con sistemi esterni complessi, è necessario esplorare le sue capacità di estensione. In questo capitolo analizzeremo i due meccanismi principali: il Model Context Protocol (MCP) e il sistema di Extensions.

## Estendere i Tool con MCP (Model Context Protocol)

L'MCP è un protocollo che permette a Gemini CLI di scoprire e utilizzare dinamicamente tool esposti da server esterni. In pratica, puoi scrivere un piccolo server (in qualsiasi linguaggio) che offre nuove "capacità" (tool) e Gemini CLI sarà in grado di usarle come se fossero integrate.

### Come Funziona?

1.  **Avvii un Server MCP**: Questo server, quando contattato, descrive i tool che mette a disposizione, specificando il loro nome, la descrizione e i parametri che accettano (usando lo standard OpenAPI Schema).
2.  **Configuri Gemini CLI**: Nel tuo file `settings.json`, dici a Gemini CLI dove trovare questo server.
3.  **Discovery**: All'avvio, Gemini CLI contatta il server, riceve l'elenco dei nuovi tool e li aggiunge al contesto del modello.
4.  **Utilizzo**: Da quel momento, il modello può decidere di usare i nuovi tool esattamente come farebbe con quelli integrati, e la CLI orchestrerà la chiamata al tuo server esterno.

### Configurare un Server MCP

La configurazione avviene nella sezione `mcpServers` del tuo file `settings.json`. Puoi definire uno o più server, ciascuno con un alias.

_Esempio: configurare un server MCP locale scritto in Python._

**File**: `~/.gemini/settings.json`

```json
{
  // ... altre impostazioni
  "mcpServers": {
    "my-local-tools": {
      "command": "python",
      "args": ["/percorso/del/tuo/mcp_server.py"],
      "description": "Server con tool personalizzati per il mio progetto."
    }
  }
}
```

In questo esempio, Gemini CLI eseguirà il comando `python /percorso/del/tuo/mcp_server.py` per avviare il server e comunicare con esso.

### Verificare i Server MCP

Una volta configurato un server, puoi usare il comando `/mcp` per verificare che sia stato caricato correttamente e per ispezionare i tool che offre:

- **/mcp**: Mostra l'elenco dei server configurati e il loro stato.
- **/mcp desc**: Mostra anche le descrizioni dettagliate di ogni tool fornito dai server.

## Il Sistema di Extensions

Le estensioni sono un meccanismo ancora più potente e strutturato per pacchettizzare e distribuire funzionalità aggiuntive. Un'estensione può contenere:

- Un insieme di **comandi personalizzati** (file `.toml`).
- Uno o più **server MCP** pre-configurati.
- File di contesto (`GEMINI.md`) specifici.
- Regole di sicurezza (es. tool da escludere).

In pratica, un'estensione è un "pacchetto di capacità" autonomo e condivisibile.

### Gestire le Estensioni

La gestione avviene tramite il comando `gemini extensions`:

- **`gemini extensions install <URL>`**: Installa un'estensione da un repository Git.
  ```bash
  gemini extensions install https://github.com/gemini-cli-extensions/security
  ```
- **`gemini extensions list`**: Mostra tutte le estensioni installate.
- **`gemini extensions disable <nome-estensione>`**: Disabilita un'estensione.
- **`gemini extensions uninstall <nome-estensione>`**: Rimuove un'estensione.

### Struttura di un'Estensione

Ogni estensione è una directory che contiene un file `gemini-extension.json`, il quale ne definisce il contenuto e il comportamento. Questo file può specificare i server MCP da avviare, i comandi da registrare e molto altro.

Questo sistema permette alla community di creare e condividere "plugin" per Gemini CLI, estendendone le capacità in modi specifici (es. un'estensione per interagire con AWS, una per la sicurezza, una per il deploy su Kubernetes, ecc.).

---

[< 8. Esempio Pratico: Creare un Comando Custom](./08-esempio-pratico-comando-custom.md) | [Torna all'Indice](./index.md) | [10. Sicurezza e Gestione Enterprise >](./10-sicurezza-e-gestione-enterprise.md)
