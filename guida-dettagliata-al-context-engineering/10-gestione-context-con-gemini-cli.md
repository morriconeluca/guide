# 10. Gestione del Contesto con Gemini CLI

La Gemini Command Line Interface (CLI) è un potente agente AI open source che incarna i principi del Context Engineering, fornendo un accesso diretto a Gemini dal terminale. Questa CLI è progettata per interpretare prompt in linguaggio naturale e, attraverso un ciclo di ragionamento e azione (ReAct), interagire con strumenti integrati e server Model Context Protocol (MCP) per eseguire compiti complessi.

## Gemini CLI come Agente AI

A differenza delle interfacce tradizionali che richiedono comandi specifici per ogni azione, la Gemini CLI agisce come un vero e proprio agente AI. Quando le viene fornito un prompt, essa analizza l'intento dell'utente e decide autonomamente quali azioni intraprendere, quali strumenti utilizzare e quali informazioni recuperare per formulare la risposta più appropriata.

## Utilizzo Base: Prompting Diretto

L'interazione più comune con Gemini CLI avviene tramite il prompting diretto. Basta digitare `gemini` seguito dal tuo prompt:

```bash
gemini "Crea un componente React per un contatore semplice con un bottone per incrementare e uno per decrementare."
```

La CLI elaborerà la richiesta, genererà il codice o la risposta e la presenterà nel terminale.

## Strumenti Integrati

La Gemini CLI è dotata di una serie di strumenti integrati che le consentono di interagire con l'ambiente locale e il web. Questi strumenti sono fondamentali per la sua capacità di raccogliere contesto e agire. Alcuni esempi includono:

- **Operazioni su file:** Lettura e scrittura di file nel filesystem locale.
- **Comandi terminale:** Esecuzione di comandi shell (es. `grep`, `ls`, `git`).
- **Ricerca web:** Accesso a informazioni aggiornate tramite ricerche su internet.
- **Recupero web:** Recupero di contenuti da URL specifici.

Questi strumenti permettono alla CLI di "vedere" e "agire" nel tuo ambiente di lavoro, rendendola un partner di sviluppo estremamente contestualizzato.

## Il Ciclo ReAct in Gemini CLI

La capacità della Gemini CLI di ragionare e agire si basa sul framework ReAct (Reason and Act). Internamente, la CLI segue un processo simile a questo:

1.  **Thought (Pensiero):** L'agente analizza il prompt dell'utente e formula un piano d'azione.
2.  **Act (Azione):** L'agente decide di utilizzare uno o più strumenti integrati (es. `read_file`, `run_shell_command`, `web_search`).
3.  **Observation (Osservazione):** L'agente riceve l'output dello strumento.
4.  **Thought (Pensiero):** L'agente valuta l'osservazione e, se necessario, affina il piano o esegue ulteriori azioni.
5.  **Risposta Finale:** L'agente formula una risposta completa e contestualizzata per l'utente.

Questo ciclo si ripete finché il compito non è completato o l'agente ha fornito la migliore risposta possibile.

## Comandi Interni e Gestione del Contesto

Durante una sessione interattiva, la Gemini CLI offre anche comandi interni per gestire il contesto e monitorare il suo stato. Questi comandi non sono comandi shell diretti, ma direttive per l'agente stesso:

- `/memory`: Per visualizzare o gestire la memoria conversazionale.
- `/stats`: Per ottenere statistiche sull'utilizzo del modello o del contesto.
- `/tools`: Per elencare gli strumenti disponibili o abilitarne/disabilitarne alcuni.
- `/mcp`: Per gestire le connessioni ai server Model Context Protocol.

## Gemini CLI nel Ciclo di Vita dello Sviluppo

Come visto nel Capitolo 7, la Gemini CLI si integra perfettamente nelle metodologie di sviluppo AI-driven:

- **Prompt Driven Development (PDD) e PRD Driven Development (PRDDD):** L'esecuzione diretta dei prompt (`gemini "il tuo prompt"`) è il metodo standard per generare codice, piani o risposte basate su requisiti specifici.
- **Spec Driven Development (SDD):** Sebbene la versione attuale della CLI non supporti un flag `--spec` dedicato, è possibile integrare le specifiche in un prompt più ampio o sviluppare estensioni personalizzate per guidare la generazione del codice.

La Gemini CLI è un esempio concreto di come il Context Engineering possa essere applicato per creare strumenti di sviluppo potenti e flessibili, capaci di comprendere e agire nel contesto del tuo progetto.

---

[< Indietro (Architetture)](./09-architetture-per-la-gestione-del-contesto.md) | [Torna all'Indice](./index.md) | [Avanti (Model Context Protocol)](./11-model-context-protocol-mcp.md)
