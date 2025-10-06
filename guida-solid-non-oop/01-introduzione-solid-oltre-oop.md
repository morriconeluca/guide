## 01 - Introduzione: SOLID Oltre l'OOP

I principi SOLID, acronimo coniato da Michael Feathers sulla base degli insegnamenti di Robert C. Martin ("Uncle Bob"), sono universalmente riconosciuti come un fondamento per la scrittura di software di alta qualità. Tradizionalmente, vengono spiegati e insegnati usando il lessico della programmazione orientata agli oggetti (OOP): classi, interfacce, ereditarietà.

Questo legame storico con l'OOP, tuttavia, oscura la loro vera natura. I principi SOLID non riguardano le "classi", ma la **gestione delle dipendenze tra i moduli software**. Come sottolinea lo stesso Uncle Bob, per "classe" si può intendere "un semplice raggruppamento di funzioni e dati".

L'obiettivo di questi principi è creare un software che sia:

*   **Facile da modificare:** I cambiamenti ai requisiti non dovrebbero richiedere una riscrittura radicale del sistema.
*   **Facile da comprendere:** La struttura del codice dovrebbe essere chiara e le responsabilità ben definite.
*   **Facile da riutilizzare:** I componenti del software dovrebbero essere abbastanza disaccoppiati da poter essere riutilizzati in altri contesti.

### Perché un Contesto Non-OOP?

Con la crescente popolarità di paradigmi come la programmazione funzionale (FP) e di linguaggi come Go, Rust, o anche JavaScript/TypeScript usati in stile funzionale, sorge una domanda legittima: **i principi SOLID sono ancora rilevanti?**

La risposta è un sonoro **sì**.

L'accoppiamento e la coesione sono problemi universali. Un monolite di funzioni ingarbugliate in un singolo file è problematico tanto quanto un groviglio di classi strettamente accoppiate. La necessità di isolare i moduli, di estendere il comportamento senza modificare codice testato e di dipendere da astrazioni stabili esiste in qualsiasi paradigma.

In questa guida, intraprenderemo un esercizio di "traduzione". Prenderemo l'essenza di ogni principio SOLID e la reinterpreteremo in un contesto basato su **moduli** (file), **funzioni** e **strutture dati**, usando esempi pratici per dimostrare come queste idee senza tempo possano guidarci a scrivere codice migliore, indipendentemente dal paradigma.

---

[< Indietro (Indice)](./index.md) | [Avanti (SRP: Responsabilità a Livello di Modulo) >](./02-srp-responsabilita-a-livello-di-modulo.md)
