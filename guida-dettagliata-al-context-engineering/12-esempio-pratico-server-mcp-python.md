# 12. Esempio Pratico: Creare un Server MCP in Python

Nei capitoli precedenti abbiamo introdotto il Model Context Protocol (MCP) come standard per estendere le capacità di un agente AI. In questo capitolo, passeremo dalla teoria alla pratica e costruiremo un semplice server MCP in Python che espone un tool personalizzato, per poi collegarlo a Gemini CLI.

## Obiettivo

Creeremo un server che offre un unico tool: `saluta`. Questo tool accetta un parametro `nome` (una stringa) e restituisce un messaggio di saluto personalizzato.

## Prerequisiti

- Python 3.7+ installato.
- Gemini CLI installato e configurato.

## 1. Il Codice del Server (`mcp_saluta_server.py`)

Il nostro server deve fare due cose principali:

1.  Rispondere alla richiesta di "discovery" di Gemini CLI, descrivendo i tool che offre.
2.  Eseguire il tool quando Gemini CLI glielo chiede.

Il protocollo MCP si basa su JSON-RPC 2.0, e la comunicazione con un server locale avviene tramite `stdin` e `stdout`.

Crea un file chiamato `mcp_saluta_server.py` e inserisci il seguente codice. Lo analizzeremo subito dopo.

```python
import sys
import json

# Definiamo il nostro tool come una semplice funzione Python
def saluta(nome: str) -> str:
    """Restituisce un saluto personalizzato."""
    return f"Ciao, {nome}! Benvenuto nel mondo dei server MCP."

# Questa è la definizione del nostro tool secondo lo standard che l'LLM può capire.
# Usa una struttura simile a JSON Schema.
TOOL_SALUTA_DEF = {
    "name": "saluta",
    "description": "Restituisce un saluto amichevole a una persona specifica.",
    "parameters": {
        "type": "object",
        "properties": {
            "nome": {
                "type": "string",
                "description": "Il nome della persona da salutare."
            }
        },
        "required": ["nome"]
    }
}

def send_response(response):
    """Funzione helper per inviare una risposta JSON-RPC a stdout."""
    response_str = json.dumps(response)
    # Scriviamo la risposta su stdout, preceduta dalla sua lunghezza come richiesto
    # da alcuni transport layer per il framing dei messaggi.
    sys.stdout.write(f"Content-Length: {len(response_str)}\r\n\r\n{response_str}")
    sys.stdout.flush()

def main():
    """Loop principale del server che ascolta su stdin."""
    while True:
        line = sys.stdin.readline()
        if not line:
            break

        # Ignoriamo le header, cerchiamo la riga vuota che precede il JSON
        if line.strip() == "":
            content_length_line = sys.stdin.readline() # Legge la riga Content-Length se presente
            # Assumiamo che la riga successiva sia il JSON, leggiamo la sua lunghezza se specificata
            # Per semplicità, in questo esempio leggiamo direttamente il corpo JSON
            # In un'implementazione reale, si dovrebbe parsare correttamente Content-Length

            # Leggiamo il corpo del messaggio JSON
            # Questa parte è semplificata. Un server reale dovrebbe leggere esattamente
            # il numero di byte specificato da Content-Length.
            json_line = sys.stdin.readline()
            try:
                request = json.loads(json_line)
                request_id = request.get("id")
                method = request.get("method")
                params = request.get("params")

                if method == "mcp/negotiateCapabilities":
                    # Gemini CLI ci sta chiedendo quali tool abbiamo.
                    response = {
                        "jsonrpc": "2.0",
                        "id": request_id,
                        "result": {
                            "capabilities": {
                                "tools": [TOOL_SALUTA_DEF]
                            }
                        }
                    }
                    send_response(response)

                elif method == "tool/run":
                    # Gemini CLI vuole eseguire un tool.
                    tool_name = params.get("name")
                    tool_args = params.get("args", {})

                    if tool_name == "saluta":
                        nome = tool_args.get("nome", "sconosciuto")
                        risultato = saluta(nome)
                        response = {
                            "jsonrpc": "2.0",
                            "id": request_id,
                            "result": {
                                "output": risultato
                            }
                        }
                        send_response(response)

            except json.JSONDecodeError:
                # Ignora linee non JSON
                pass
            except Exception:
                # In un server reale, qui si gestirebbero gli errori
                pass

if __name__ == "__main__":
    main()

```

## 2. Collegare il Server a Gemini CLI

Ora dobbiamo dire a Gemini CLI di avviare ed ascoltare il nostro server.

1.  Apri il file di configurazione utente di Gemini CLI, che si trova in `~/.gemini/settings.json`.
2.  Aggiungi o modifica la sezione `mcpServers` come segue. Assicurati di usare il **percorso assoluto** al tuo script `mcp_saluta_server.py`.

```json
{
  "mcpServers": {
    "server_saluta": {
      "command": "python",
      "args": ["/Users/tuo_utente/percorso/del/progetto/mcp_saluta_server.py"],
      "description": "Il mio primo server MCP in Python"
    }
  }
}
```

**Importante:** Sostituisci `/Users/tuo_utente/percorso/del/progetto/` con il percorso reale dove hai salvato lo script.

## 3. Testare l'Integrazione

Se hai già una sessione di Gemini CLI aperta, chiudila e riavviala per farle caricare la nuova configurazione.

1.  **Avvia Gemini CLI:**
    ```bash
    gemini
    ```
2.  **Verifica la Scoperta del Tool:**
    Usa il comando `/mcp` per vedere se Gemini CLI ha trovato il tuo server.

    ```
    > /mcp
    ```

    Dovresti vedere `server_saluta` nella lista. Ora, ispeziona i tool che espone:

    ```
    > /mcp desc
    ```

    L'output dovrebbe mostrare il nostro tool `saluta` con la sua descrizione.

3.  **Invoca il Tool:**
    Ora la parte divertente. Chiedi a Gemini di usare il tuo nuovo tool!
    ```
    > Saluta il mondo
    ```
    Gemini CLI analizzerà la tua richiesta. Il suo "pensiero" (ciclo ReAct) sarà simile a questo:
    - **Thought:** "L'utente vuole salutare qualcuno. Ho a disposizione un tool chiamato `saluta` che sembra fare proprio questo. Prende un parametro `nome`. Estraggo 'mondo' dal prompt."
    - **Act:** Genera la chiamata al tool: `saluta(nome="mondo")`.
    - **Observation:** Il tuo script Python viene eseguito, la funzione `saluta("mondo")` restituisce `"Ciao, mondo! Benvenuto nel mondo dei server MCP."`. Questo risultato torna a Gemini.
    - **Thought:** "Ho ricevuto il risultato. Ora posso formulare una risposta per l'utente."
    - **Risposta Finale:** L'agente ti mostrerà una risposta come: "Certo! Ecco il saluto: Ciao, mondo! Benvenuto nel mondo dei server MCP."

Ce l'hai fatta! Hai esteso le capacità di un agente AI avanzato con poche righe di Python, sfruttando un protocollo standard per l'integrazione di tool.

---

[< 11. Il Model Context Protocol (MCP)](./11-model-context-protocol-mcp.md) | [Torna all'Indice](./index.md) | [13. Progettare e Valutare Sistemi Context-Aware >](./13-progettare-e-valutare-sistemi-context-aware.md)
