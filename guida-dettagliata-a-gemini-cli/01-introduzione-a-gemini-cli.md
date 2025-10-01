# 1. Introduzione a Gemini CLI

Benvenuto al primo capitolo della nostra guida. Qui esploreremo cos'è Gemini CLI, a chi si rivolge e perché potrebbe diventare uno strumento indispensabile nel tuo arsenale di sviluppo.

## Cos'è Gemini CLI?

Gemini CLI è un agente AI open-source (con licenza Apache 2.0) che porta la potenza dei modelli Gemini di Google direttamente nel tuo terminale. È progettato per essere un'interfaccia leggera e veloce, offrendo un percorso diretto e senza fronzoli dal tuo prompt al modello.

A differenza di un semplice chatbot, Gemini CLI è un vero e proprio **agente interattivo**. Questo significa che non si limita a rispondere alle tue domande, ma può anche eseguire azioni nel tuo ambiente di sviluppo locale, come leggere e scrivere file, eseguire comandi shell e cercare informazioni sul web.

## A Chi si Rivolge?

Questo strumento è pensato primariamente per **sviluppatori, DevOps e data scientist** che vivono nella riga di comando. Se il terminale è il tuo ambiente di lavoro principale, Gemini CLI ti permette di integrare l'intelligenza artificiale nel tuo workflow senza mai dover lasciare la tastiera.

## Perché Usare Gemini CLI? I Vantaggi Chiave

La documentazione ufficiale evidenzia diversi punti di forza che rendono questo strumento particolarmente interessante:

- **🧠 Modelli Potenti**: Accesso diretto a modelli all'avanguardia come **Gemini 2.5 Pro**, con la sua enorme finestra di contesto da 1 milione di token, ideale per analizzare codebase di grandi dimensioni.
- **🎯 Free Tier Generoso**: Un piano gratuito che, al momento della scrittura, offre 60 richieste al minuto e 1.000 al giorno con un account Google personale, perfetto per l'uso individuale.
- **🔧 Tool Integrati**: Funzionalità "out-of-the-box" per interagire con il file system, eseguire comandi shell, effettuare ricerche su Google e recuperare contenuti da URL.
- **🔌 Estensibilità**: Progettato per essere esteso. Tramite il **Model Context Protocol (MCP)**, è possibile integrare tool personalizzati e connettere Gemini CLI ad altri servizi.
- **💻 Filosofia "Terminal-first"**: L'intera esperienza utente è ottimizzata per l'uso da terminale, garantendo efficienza e velocità per chi è abituato a questo tipo di interfaccia.

## Architettura in Breve

Per comprendere come funziona Gemini CLI, è utile conoscere la sua architettura di base, che si fonda su due componenti principali:

1.  **CLI (`packages/cli`)**: È la parte con cui interagisci direttamente. Gestisce l'interfaccia utente nel terminale, l'input, la visualizzazione dell'output, la cronologia dei comandi e la configurazione.
2.  **Core (`packages/core`)**: Funge da "cervello" locale. Orchestra la comunicazione con l'API di Gemini, costruisce il contesto per i prompt, gestisce l'esecuzione dei tool e mantiene lo stato della conversazione.

Quando invii un prompt, il **CLI** lo passa al **Core**. Il Core lo elabora e, se necessario, lo invia all'API di Gemini. Se il modello decide di usare un tool (es. leggere un file), il Core esegue l'azione (chiedendoti conferma se necessario) e invia il risultato indietro al modello per ottenere una risposta finale, che viene poi visualizzata dal CLI.

Nei prossimi capitoli, vedremo come installare e configurare questo potente strumento.

---

[Torna all'Indice](./index.md) | [2. Installazione e Primo Avvio >](./02-installazione-e-primo-avvio.md)
