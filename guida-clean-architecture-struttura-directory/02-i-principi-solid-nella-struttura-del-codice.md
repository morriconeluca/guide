## 02 - I Principi SOLID nella Struttura del Codice

I principi SOLID sono il fondamento di un'architettura robusta e manutenibile. Non sono regole astratte, ma guide pratiche che influenzano direttamente come organizziamo il codice. Vediamo come ogni principio si manifesta nella struttura delle directory.

### S - Single Responsibility Principle (SRP)

> Un modulo dovrebbe essere responsabile di uno e un solo attore (un gruppo di utenti o stakeholder).

Questo significa separare il codice che cambia per motivi diversi. Se il reparto contabilità e il reparto HR hanno logiche diverse che operano sugli impiegati, queste logiche non dovrebbero risiedere nello stesso file.

**Impatto sulla struttura:**

*   **Separazione per ruolo:** Invece di una classe `Employee` onnipotente, creiamo moduli specifici.

```plaintext
/src
  /features
    /employee
      - employee.data.ts       # Struttura dati condivisa
      /accounting
        - pay.calculator.ts    # Logica per il calcolo della paga (Attore: Contabilità)
      /human-resources
        - hour.reporter.ts     # Logica per la reportistica (Attore: HR)
      /persistence
        - employee.repository.ts # Logica per il salvataggio (Attore: DBA)
```

### O - Open-Closed Principle (OCP)

> Un'entità software dovrebbe essere aperta all'estensione, ma chiusa alla modifica.

Per aggiungere nuove funzionalità non dovremmo modificare codice esistente e testato, ma aggiungerne di nuovo. Questo si ottiene tramite astrazioni (interfacce o classi base).

**Impatto sulla struttura:**

*   **Uso di interfacce e implementazioni separate:** Si definisce un'interfaccia in un modulo stabile e si creano nuove implementazioni in altri moduli.

```plaintext
/src
  /features
    /reporting
      - report.generator.ts        # Interfaccia 'ReportGenerator'
      /generators
        - pdf.report.generator.ts  # Implementazione per PDF
        - csv.report.generator.ts  # Implementazione per CSV
        - html.report.generator.ts # Nuova estensione, senza modificare il resto
```

### L - Liskov Substitution Principle (LSP)

> I sottotipi devono essere sostituibili ai loro tipi base senza alterare la correttezza del programma.

Questo principio riguarda il comportamento corretto dell'ereditarietà. Una struttura di directory che promuove una chiara definizione dei "contratti" (interfacce) aiuta a rispettare questo principio, anche se la sua validazione avviene nel codice stesso.

### I - Interface Segregation Principle (ISP)

> I client non dovrebbero essere costretti a dipendere da interfacce che non usano.

È meglio avere molte interfacce piccole e specifiche per un ruolo, piuttosto che una grande e generica. 

**Impatto sulla struttura:**

*   **Creazione di cartelle per interfacce specifiche:** Si definiscono interfacce granulari che corrispondono a casi d'uso specifici.

```plaintext
/src/ 
  /features
    /document
      /interfaces
        - readable.document.ts   # Interfaccia per chi deve solo leggere
        - writable.document.ts   # Interfaccia per chi deve scrivere
        - printable.document.ts  # Interfaccia per chi deve stampare
```

### D - Dependency Inversion Principle (DIP)

> I moduli di alto livello non dovrebbero dipendere dai moduli di basso livello. Entrambi dovrebbero dipendere da astrazioni.

Questo è il principio che governa la Clean Architecture. Le logiche di business (alto livello) non devono dipendere dai dettagli implementativi come database o framework (basso livello). La dipendenza viene "invertita" tramite un'interfaccia.

**Impatto sulla struttura:**

*   **Le interfacce appartengono al client (alto livello):** L'interfaccia `UserRepository` non è definita nel layer del database, ma nel layer del dominio o dell'applicazione che la *usa*.

```plaintext
/src
  /application
    /user
      - user.service.ts          # Modulo di alto livello
      - user.repository.ts       # Interfaccia (astrazione) definita qui!

  /infrastructure
    /persistence
      - postgres.user.repository.ts # Modulo di basso livello che implementa l'interfaccia
```
In questo modo, `application` non sa nulla di `infrastructure`. È `infrastructure` che dipende dall'astrazione definita in `application`.

---

[< Capitolo Precedente: 01 - Introduzione ai Valori dell'Architettura](./01-introduzione-ai-valori-dell-architettura.md)

[> Capitolo Successivo: 03 - I Livelli e la Regola delle Dipendenze](./03-i-livelli-e-la-regola-delle-dipendenze.md)

[< Torna all'Indice](./index.md)
