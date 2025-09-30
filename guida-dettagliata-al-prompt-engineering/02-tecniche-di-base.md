# 2. Le Tecniche di Base: Zero-Shot vs Few-Shot

Una volta compresi i principi fondamentali, il passo successivo è decidere _quanti esempi_ fornire al modello all'interno del prompt. Questa scelta definisce le due tecniche di base più comuni: Zero-Shot e Few-Shot.

---

### Zero-Shot Prompting (Nessun Esempio)

Come suggerisce il nome, questa tecnica consiste nel fare una richiesta al modello senza fornirgli alcun esempio preliminare. Ci si affida interamente alla sua vasta conoscenza pre-addestrata per comprendere e eseguire il compito.

- **Come funziona:** Il modello analizza la tua istruzione (che deve essere molto chiara, vedi [Principi Fondamentali](./01-principi-fondamentali.md)) e formula una risposta basandosi sui pattern che ha appreso durante il suo addestramento su miliardi di testi.
- **Quando usarlo:** È ideale per compiti semplici, generici e ben definiti, dove non è richiesto un formato di output particolarmente strano o specifico. Esempi: traduzioni, riassunti di testi brevi, risposte a domande fattuali.

**Esempio Pratico (Classificazione del sentiment):**

> **Prompt:**
>
> ```
> Classifica il sentiment del seguente commento del cliente in Positivo, Negativo o Neutro.
>
> Commento: "La spedizione è arrivata con due giorni di ritardo, ma il prodotto è di qualità eccellente."
> ```
>
> **Output (probabile):**
>
> ```
> Neutro
> ```
>
> _Il modello è in grado di bilanciare l'aspetto negativo (ritardo) e quello positivo (qualità) per dare una valutazione complessiva neutra o mista, senza aver bisogno di esempi._

---

### Few-Shot Prompting (Con Pochi Esempi)

Questa tecnica, nota anche come "In-Context Learning" (apprendimento nel contesto), prevede di includere nel prompt alcuni esempi (solitamente da 2 a 5) di input e del relativo output desiderato. In questo modo, si "insegna" al modello il pattern specifico da seguire.

- **Come funziona:** Il modello analizza gli esempi forniti, ne deduce la logica, lo stile o il formato, e applica lo stesso pattern alla nuova richiesta che si trova alla fine del prompt. Non è un vero e proprio "addestramento", ma una guida contestuale estremamente efficace.
- **Quando usarlo:** È fondamentale per compiti complessi, ambigui, o quando si desidera un output con una struttura molto specifica. Esempi: estrazione di dati strutturati, classificazione con categorie non standard, risposte in un formato personalizzato (es. JSON).

**Esempio Pratico (Estrazione dati per un database):**

> **Prompt:**
>
> ```
> Estrai il nome del prodotto e il prezzo da ogni frase, formattando l'output come "Prodotto: [Nome] - Prezzo: [Prezzo]".
>
> Frase: "La nuova Smart TV LED 4K è in offerta a 499€."
> Output: Prodotto: Smart TV LED 4K - Prezzo: 499€
>
> Frase: "Puoi acquistare le cuffie wireless Noise-Cancelling per 129€."
> Output: Prodotto: Cuffie wireless Noise-Cancelling - Prezzo: 129€
>
> Frase: "Oggi il notebook ultraleggero con chip M3 costa 1299€."
> Output:
> ```
>
> **Output (probabile):**
>
> ```
> Prodotto: Notebook ultraleggero con chip M3 - Prezzo: 1299€
> ```
>
> _Grazie agli esempi, il modello ha imparato a riconoscere l'entità "prodotto" e "prezzo" e a formattare l'output esattamente come richiesto._

---

### Riepilogo

| Caratteristica        | Zero-Shot Prompting               | Few-Shot Prompting                             |
| :-------------------- | :-------------------------------- | :--------------------------------------------- |
| **Esempi nel Prompt** | Nessuno                           | Da 2 a 5                                       |
| **Complessità Task**  | Bassa                             | Alta                                           |
| **Controllo Output**  | Basso                             | Alto                                           |
| **Ideale per**        | Compiti generici e veloci         | Compiti specifici e strutturati                |
| **Rischio**           | L'output può essere imprevedibile | Richiede più sforzo nella scrittura del prompt |

La scelta tra Zero-Shot e Few-Shot è uno dei primi bivi nel percorso di un prompt engineer. La regola generale è: parti sempre con lo Zero-Shot per la sua semplicità. Se il risultato non è soddisfacente o sufficientemente strutturato, passa al Few-Shot.

---

[< Indietro (Principi Fondamentali)](./01-principi-fondamentali.md) | [Torna all'Indice](./index.md) | [Avanti (Chain-of-Thought) >](./03-chain-of-thought.md)
