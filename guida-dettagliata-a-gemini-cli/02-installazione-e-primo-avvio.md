# 2. Installazione e Primo Avvio

In questo capitolo vedremo come installare Gemini CLI sul tuo sistema, configureremo l'autenticazione e faremo il primo avvio per assicurarci che tutto funzioni correttamente.

## Requisiti di Sistema

Prima di iniziare, assicurati che il tuo sistema soddisfi un requisito fondamentale:

- **Node.js**: È richiesta la versione **20 o superiore**.

Puoi verificare la tua versione di Node.js con il comando:

```bash
node -v
```

Gemini CLI è compatibile con macOS, Linux e Windows.

## Metodi di Installazione

Puoi scegliere tra diversi metodi di installazione, a seconda delle tue preferenze.

### 1. Esecuzione Istantanea con `npx`

Se vuoi provare Gemini CLI senza installarlo globalmente, puoi usare `npx`. Questo comando scaricherà ed eseguirà l'ultima versione disponibile ogni volta che lo lanci.

```bash
# Non richiede installazione
npx https://github.com/google-gemini/gemini-cli
```

### 2. Installazione Globale con `npm`

Per un uso continuativo, è consigliabile installare il pacchetto globalmente tramite `npm` (Node Package Manager).

```bash
npm install -g @google/gemini-cli
```

### 3. Installazione Globale con Homebrew (macOS/Linux)

Se usi macOS o Linux e hai [Homebrew](https://brew.sh/) installato, puoi usare questo comodo gestore di pacchetti.

```bash
brew install gemini-cli
```

## Canali di Release

Gemini CLI offre tre "canali" di release per darti controllo sulla stabilità e sulle nuove funzionalità. Puoi scegliere un canale specifico durante l'installazione con `npm`:

- **Stable (`@latest`)**: È il canale raccomandato per la maggior parte degli utenti. Le versioni sono testate e promosse settimanalmente. È il canale di default.
  ```bash
  npm install -g @google/gemini-cli@latest
  ```
- **Preview (`@preview`)**: Contiene le funzionalità della settimana, ma potrebbe avere bug o regressioni. Utile per testare le novità in anteprima.
  ```bash
  npm install -g @google/gemini-cli@preview
  ```
- **Nightly (`@nightly`)**: Build giornaliere direttamente dal branch principale. Da usare solo se sei consapevole dei rischi e vuoi le modifiche più recenti in assoluto.
  ```bash
  npm install -g @google/gemini-cli@nightly
  ```

## Metodi di Autenticazione

Per poter comunicare con i modelli Gemini, la CLI deve autenticarsi. Ci sono tre metodi principali, ciascuno adatto a scenari diversi.

### Opzione 1: Login with Google (Consigliato per Sviluppatori Individuali)

È il metodo più semplice e veloce.

- **Ideale per**: Sviluppatori individuali, iscritti a Google AI Pro/Ultra, utenti con licenza Gemini Code Assist.
- **Vantaggi**: Offre il **free tier** (60 richieste/min), non richiede gestione di API key e usa automaticamente i modelli più recenti come Gemini 2.5 Pro.

**Come si usa:**

Semplicemente lancia la CLI. Al primo avvio, ti verrà chiesto di scegliere un metodo di autenticazione. Seleziona "Login with Google" e verrai reindirizzato al browser per completare l'accesso.

```bash
gemini
```

> **Nota**: Se usi una licenza a pagamento (es. Code Assist) fornita dalla tua organizzazione, potresti dover impostare il tuo progetto Google Cloud:
> `export GOOGLE_CLOUD_PROJECT="IL_TUO_PROJECT_ID"`

### Opzione 2: Gemini API Key

Questo metodo ti dà un controllo più granulare sul modello da usare.

- **Ideale per**: Sviluppatori che necessitano di usare modelli specifici o di passare a piani a pagamento con limiti più alti.
- **Vantaggi**: Controllo sul modello, fatturazione basata sull'uso.

**Come si usa:**

1.  Ottieni la tua API Key da [Google AI Studio](https://aistudio.google.com/apikey).
2.  Imposta la chiave come variabile d'ambiente prima di lanciare la CLI.

```bash
export GEMINI_API_KEY="LA_TUA_API_KEY"
gemini
```

### Opzione 3: Vertex AI

La soluzione per team enterprise e carichi di lavoro in produzione.

- **Ideale per**: Contesti aziendali che richiedono sicurezza avanzata, compliance e integrazione con l'infrastruttura Google Cloud.
- **Vantaggi**: Limiti di richieste più alti, funzionalità enterprise.

**Come si usa:**

Anche qui si usa una API key, ma ottenuta dalla Google Cloud Console, e si imposta una variabile d'ambiente aggiuntiva.

```bash
export GOOGLE_API_KEY="LA_TUA_API_KEY_VERTEX"
export GOOGLE_GENAI_USE_VERTEXAI=true
gemini
```

## Primo Avvio

Una volta completata l'installazione e l'autenticazione, sei pronto. Lancia il comando `gemini` nel tuo terminale dalla root del tuo progetto. Verrai accolto dall'interfaccia interattiva, pronto a ricevere il tuo primo prompt.

```bash
cd /percorso/del/tuo/progetto
gemini
```

Ora che Gemini CLI è installato e funzionante, nel prossimo capitolo esploreremo i comandi essenziali per interagire con l'agente.

---

[< 1. Introduzione a Gemini CLI](./01-introduzione-a-gemini-cli.md) | [Torna all'Indice](./index.md) | [3. Comandi Essenziali e Interfaccia >](./03-comandi-essenziali-e-interfaccia.md)
