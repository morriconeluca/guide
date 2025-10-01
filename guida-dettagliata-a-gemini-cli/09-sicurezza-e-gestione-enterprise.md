# 9. Sicurezza e Gestione Enterprise

L'uso di un agente AI potente come Gemini CLI in un ambiente professionale, specialmente in team o a livello aziendale, solleva importanti questioni di sicurezza, governance e standardizzazione. Questo capitolo esplora le funzionalità integrate in Gemini CLI per affrontare queste sfide.

## Esecuzione Sicura con il Sandboxing

La capacità di Gemini CLI di eseguire comandi shell (`run_shell_command`) è incredibilmente potente, ma rappresenta anche un potenziale rischio per la sicurezza. Per mitigare questo rischio, la CLI offre una modalità di **sandboxing** che isola l'esecuzione dei tool dall'ambiente del sistema host.

### Cos'è e Perché Usarlo

Quando il sandboxing è attivo, i comandi non vengono eseguiti direttamente sulla tua macchina, ma all'interno di un ambiente containerizzato (solitamente Docker). Questo container ha un accesso limitato al file system (spesso solo alla directory del progetto), impedendo a un comando, malevolo o errato, di danneggiare il tuo sistema operativo o accedere a file sensibili al di fuori dell'area di lavoro.

### Come Abilitarlo

Puoi abilitare il sandboxing in due modi principali:

1.  **Flag all'Avvio**: Usa il flag `--sandbox` (o `-s`) per una singola sessione.
    ```bash
    gemini --sandbox
    ```
2.  **Configurazione Persistente**: Impostalo nel tuo file `settings.json` (a livello di utente o di progetto) per averlo sempre attivo.
    ```json
    {
      "tools": {
        "sandbox": true
      }
    }
    ```

### Personalizzare l'Ambiente Sandbox

Di default, Gemini CLI usa un'immagine Docker pre-costruita. Tuttavia, se il tuo progetto ha dipendenze specifiche (es. `terraform`, `kubectl`, versioni specifiche di linguaggi), puoi creare un `Dockerfile` personalizzato.

Basta creare un file chiamato `sandbox.Dockerfile` nella directory `.gemini/` del tuo progetto. La CLI lo userà per costruire un'immagine sandbox su misura per le tue esigenze.

**Esempio**: `.gemini/sandbox.Dockerfile`

```dockerfile
# Parti dall'immagine base di Gemini CLI
FROM gemini-cli-sandbox

# Aggiungi le tue dipendenze
# Esempio: installare l'CLI di AWS
RUN apt-get update && apt-get install -y aws-cli
```

## Telemetria e Privacy

Per migliorare il prodotto, Gemini CLI raccoglie statistiche di utilizzo anonime. È fondamentale capire cosa viene raccolto e cosa no, e come disattivare questa funzione se necessario.

### Cosa Viene Raccolto

- **Utilizzo dei Tool**: Nomi dei tool chiamati, successo/fallimento dell'esecuzione, durata.
- **Richieste all'API**: Modello Gemini usato, durata della richiesta, esito.
- **Informazioni di Sessione**: Configurazione della CLI (es. tool abilitati, modalità di approvazione).

### Cosa NON Viene Raccolto

La documentazione è molto chiara su questo punto per garantire la privacy:

- **Nessuna Informazione Personale (PII)**: Nomi, email, API key non vengono raccolti.
- **Nessun Contenuto di Prompt o Risposte**: Il contenuto delle tue conversazioni rimane privato.
- **Nessun Contenuto di File**: Il contenuto dei file letti o scritti non viene registrato.

### Come Disattivare la Telemetria

Puoi disattivare la raccolta dati in qualsiasi momento nel tuo file `settings.json`:

```json
{
  "privacy": {
    "usageStatisticsEnabled": false
  }
}
```

## Gestione in Contesti Enterprise

Per i team e le aziende, è cruciale poter imporre configurazioni standard e policy di sicurezza.

- **Configurazione Centralizzata**: Gli amministratori di sistema possono usare i file di configurazione a livello di sistema (es. `/etc/gemini-cli/settings.json` su Linux) per definire impostazioni che sovrascrivono quelle di utente e progetto. Questo permette di definire policy valide per tutta l'organizzazione.

- **Imporre l'Autenticazione**: È possibile forzare l'uso di un metodo di autenticazione specifico, come Vertex AI (l'opzione consigliata per l'enterprise), tramite l'impostazione `security.auth.enforcedType`.

- **Restringere i Tool**: Un amministratore può usare la configurazione di sistema per disabilitare tool considerati rischiosi (es. `"tools": {"exclude": ["run_shell_command"]}`), limitando la superficie d'attacco.

Queste funzionalità rendono Gemini CLI uno strumento gestibile e più sicuro anche in ambienti regolamentati, dove la governance e la sicurezza sono priorità assolute.

---

[< 8. Estendere Gemini CLI: MCP e Extensions](./08-estendere-gemini-cli-mcp-e-extensions.md) | [Torna all'Indice](./index.md) | [10. Appendice: Riferimento Rapido >](./10-appendice-riferimento-rapido.md)
