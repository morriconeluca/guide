# 13. Il Model Context Protocol (MCP)

Il **Model Context Protocol (MCP)** è uno standard aperto e agnostico dal punto di vista del trasporto, progettato per standardizzare il modo in cui i sistemi di Intelligenza Artificiale (AI), in particolare i Large Language Models (LLM), si integrano e accedono a strumenti esterni, fonti di dati e servizi. Sviluppato da Anthropic e adottato da altri importanti fornitori di AI come OpenAI e Google DeepMind, l'MCP mira a superare il "paradosso dell'integrazione AI" o il "problema N x M" di creare integrazioni personalizzate per ogni modello AI e sistema esterno.

## Scopo e Benefici

L'obiettivo primario dell'MCP è consentire ai modelli AI di andare oltre la semplice risposta a domande, per eseguire compiti utili e a più passaggi nel mondo reale. Fornisce un modo sicuro, standardizzato e scalabile per l'AI di:

*   Accedere a risorse esterne.
*   Eseguire azioni.
*   Ottenere contesto rilevante da vari sistemi.

I benefici dell'adozione dell'MCP sono significativi:

*   **Riduzione del Sovraccarico di Sviluppo:** Sostituisce le integrazioni frammentate e personalizzate con un unico protocollo universale, riducendo significativamente la complessità e il tempo di sviluppo.
*   **Miglioramento delle Capacità AI:** Consente ai modelli AI di eseguire compiti complessi e a più passaggi fornendo loro un accesso controllato a dati e strumenti del mondo reale.
*   **Sicurezza e Controllo Migliorati:** Offre meccanismi robusti per la gestione dell'accesso dell'AI ai sistemi esterni, garantendo che i modelli interagiscano solo con ciò che è loro esplicitamente consentito.
*   **Mitigazione delle Allucinazioni:** Fornendo agli LLM l'accesso a dati reali e verificabili, l'MCP può aiutare a ridurre i casi di allucinazioni AI.
*   **Interoperabilità:** Favorisce un ecosistema AI più interoperabile in cui diverse applicazioni AI possono connettersi senza soluzione di continuità con varie fonti di dati e servizi.

## Architettura Client-Host-Server

L'MCP opera su un'architettura client-host-server, definendo ruoli chiari per ciascun componente:

1.  **Host:** È l'applicazione basata su AI (ad esempio, Claude Desktop, Cursor IDE, o altri framework agentici) che si integra con l'MCP. Funge da applicazione rivolta all'utente dove avvengono il ragionamento AI e l'interazione con l'utente.
2.  **Client:** Uno strato di implementazione incorporato nell'applicazione host. Il client è responsabile della gestione delle connessioni con i server MCP e della traduzione tra i requisiti dell'host e l'MCP.
3.  **Server:** Sono programmi che espongono capacità specifiche alle applicazioni AI tramite interfacce di protocollo standardizzate. I server MCP possono essere processi locali o servizi remoti e tipicamente si concentrano su un punto di integrazione specifico, come un file system, un database o un'API di terze parti (ad esempio, GitHub, Slack, servizi di calendario).

## Primitive dell'MCP: Strumenti, Risorse e Prompt

I server MCP offrono funzionalità attraverso tre blocchi fondamentali:

*   **Strumenti (Tools):** Funzioni eseguibili che gli LLM possono invocare attivamente per eseguire azioni. Esempi includono la scrittura su database, la chiamata di API esterne, la modifica di file, la ricerca di informazioni o l'invio di messaggi.
*   **Risorse (Resources):** Fonti di dati passive che forniscono informazioni contestuali di sola lettura alle applicazioni AI, come i contenuti dei file, gli schemi dei database o la documentazione delle API.
*   **Prompt (Prompts):** Modelli di istruzioni predefiniti che guidano il modello su come interagire con strumenti e risorse specifici.

## Principi di Progettazione e Caratteristiche Chiave

*   **Standardizzazione:** L'MCP definisce un modo coerente per i sistemi AI di scoprire, specificare ed eseguire strumenti, eliminando la necessità di integrazioni personalizzate per ogni fonte di dati o strumento.
*   **Sicurezza Basata sulle Capacità:** I server dichiarano esplicitamente le loro capacità, e l'Host media tutte le interazioni AI-risorsa, decidendo cosa l'AI è autorizzata ad accedere o eseguire. Ciò garantisce un accesso controllato e sicuro alle risorse esterne.
*   **Agnostico dal Trasporto:** L'MCP supporta vari meccanismi di comunicazione, inclusi input/output standard (Stdio), HTTP con Server-Sent Events (SSE), WebSockets e HTTP streamabile, consentendo opzioni di deployment flessibili.
*   **JSON-RPC 2.0:** Il protocollo sottostante per lo scambio di messaggi si basa su JSON-RPC 2.0, garantendo un formato di comunicazione strutturato e coerente.
*   **Sessioni Stateful:** L'MCP mantiene connessioni stateful tra client e server durante una sessione, facilitando lo scambio di contesto e la coordinazione.
*   **Scoperta Dinamica e Flusso Bidirezionale:** Gli agenti AI possono scoprire dinamicamente le capacità disponibili e impegnarsi in una comunicazione bidirezionale, consentendo loro di porre domande e ricevere risposte strutturate.
*   **Supervisione Umana:** Il protocollo enfatizza il controllo e la sicurezza dell'utente attraverso meccanismi come la visualizzazione degli strumenti disponibili, la richiesta di approvazione dell'utente per l'esecuzione degli strumenti e la possibilità di impostare i permessi per le operazioni.

In sintesi, l'MCP agisce come un "telecomando universale" o una "porta USB-C" per le applicazioni AI, standardizzando il modo in cui i modelli AI interagiscono con il vasto ecosistema di dati e strumenti esterni, rendendo così l'AI più capace, sicura e integrata nei sistemi esistenti.

---

[< Indietro (Gestione del Contesto con Gemini CLI)](./12-gestione-context-con-gemini-cli.md) | [Torna all'Indice](./index.md) | [Avanti (Progettare e Valutare)](./14-progettare-e-valutare-sistemi-context-aware.md)