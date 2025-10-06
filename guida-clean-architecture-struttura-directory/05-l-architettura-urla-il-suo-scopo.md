## 05 - L'Architettura Urla il suo Scopo

Cosa dovrebbe comunicare la struttura di directory di primo livello del tuo progetto? Se la prima cosa che vedi sono cartelle come `controllers`, `views`, `models` o `framework-config`, la tua architettura sta "urlando" il nome del framework che stai usando, non lo scopo del tuo software.

Una buona architettura, invece, **urla il suo dominio di business**. Questo è il principio della "Screaming Architecture".

### Architettura che Urla il Framework

Un'applicazione web generata da un framework come Ruby on Rails, Django o Express ha spesso una struttura come questa:

```plaintext
/my-project
  /controllers
  /models
  /views
  /helpers
  /config
  ...
```

Questa è un'architettura per un'applicazione web. Ma che tipo di applicazione? Un sistema di contabilità? Un gestionale per inventario? Un forum? Dalla struttura è impossibile capirlo. L'architettura sta solo dicendo: "Sono un'applicazione web". Il framework è diventato l'elemento dominante, e il dominio di business è solo un dettaglio secondario.

### Architettura che Urla il Dominio

Ora, confronta la struttura precedente con questa:

```plaintext
/my-accounting-system
  /invoicing
  /payroll
  /tax-calculation
  /reporting
  /shared-kernel
  /infrastructure
  ...
```

Questa architettura urla: "Sono un sistema di contabilità!". I casi d'uso e le aree del dominio sono i concetti di primo livello. I dettagli come il fatto che sia un'applicazione web, che usi un database SQL o che sia costruita con un certo framework sono secondari, e infatti sono spesso relegati in una cartella generica come `infrastructure`.

Questa struttura ha vantaggi enormi:

1.  **Chiarezza:** Un nuovo sviluppatore capisce immediatamente di cosa si occupa il sistema.
2.  **Manutenibilità:** Se devi modificare una regola di business sulla fatturazione, sai esattamente dove guardare: la cartella `invoicing`.
3.  **Stabilità:** Le regole di business (es. `tax-calculation`) sono isolate e non vengono influenzate da cambiamenti nei dettagli (es. aggiornare il framework web).
4.  **Testabilità:** Ogni area di business può essere testata in modo isolato.

### I Framework sono Strumenti, non Stili di Vita

I framework sono utili, ma sono un dettaglio implementativo. Sono strumenti da usare, non fondamenta a cui legarsi. La tua architettura non dovrebbe essere costruita *attorno* al framework. Al contrario, il framework dovrebbe essere un *plugin* per la tua applicazione.

Quando la tua architettura urla il suo scopo, stai trattando il framework come un dettaglio. Quando urla il nome del framework, hai permesso al dettaglio di diventare il cuore dell'architettura, violando i principi della Clean Architecture.

Nel prossimo capitolo, vedremo in dettaglio come implementare una struttura basata sulle funzionalità (o feature), che è l'applicazione pratica di questo principio.

---

[< Capitolo Precedente: 04 - Attraversare i Confini con i DTO](./04-attraversare-i-confini-con-i-dto.md)

[> Capitolo Successivo: 06 - Esempio: Struttura per Funzionalità (Feature-based)](./06-esempio-struttura-per-funzionalita-feature-based.md)

[< Torna all'Indice](./index.md)
