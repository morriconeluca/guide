# 1. I Principi Fondamentali del Prompt Engineering

Alla base di ogni interazione di successo con un modello linguistico c'è un prompt ben formulato. Indipendentemente dalla complessità del compito, il rispetto di questi quattro principi fondamentali è il fattore più importante per determinare la qualità della risposta.

---

### Principio 1: Chiarezza e Specificità

L'ambiguità è il nemico di un buon output. Il modello non può leggere la tua mente, quindi le tue istruzioni devono essere cristalline.

*   **Cosa significa:** Usa un linguaggio semplice e diretto. Evita il gergo, le doppie negazioni o le frasi contorte. Sii esplicito su cosa vuoi, perché lo vuoi e come lo vuoi.
*   **Quando usarlo:** Sempre. È la regola d'oro.

**Esempio Pratico:**

> ❌ **Negativo:** "Dammi informazioni sulla storia di Roma."
> *Perché è negativo? È un oceano di informazioni. Da dove iniziare? La fondazione? L'Impero? Il Medioevo?*
> 
> ✅ **Positivo:** "Agisci come uno storico specializzato sull'Impero Romano. Scrivi un riassunto di 300 parole sui principali fattori economici che hanno contribuito alla caduta dell'Impero Romano d'Occidente."
> *Perché è positivo? Definisce un ruolo (storico), un argomento preciso (fattori economici della caduta), un'area geografica (Impero d'Occidente) e un vincolo di lunghezza (300 parole).*

---

### Principio 2: Fornire Contesto

Il contesto è l'insieme di informazioni di background che permettono al modello di comprendere appieno lo scenario della tua richiesta. Senza contesto, il modello fa delle ipotesi, che sono spesso sbagliate.

*   **Cosa significa:** Includi nel prompt tutti i dettagli rilevanti, dati, esempi o vincoli che il modello deve conoscere per svolgere il compito.
*   **Quando usarlo:** Quando la richiesta si basa su informazioni che non sono di conoscenza universale o quando vuoi che la risposta si adatti a una situazione specifica.

**Esempio Pratico:**

> ❌ **Negativo:** "Scrivi un'email per posticipare una riunione."
> *Perché è negativo? Quale riunione? Con chi? Di quanto posticipare? Qual è il tono?*
> 
> ✅ **Positivo:** "Sono il project manager di un team software. Scrivi un'email professionale ma cordiale al nostro cliente, 'Cliente S.p.A.', per posticipare la riunione di revisione del progetto, originariamente prevista per domani alle 10:00. Spiega che abbiamo bisogno di più tempo per finalizzare la demo della nuova feature. Proponi due nuove date: dopodomani alla stessa ora o il giorno seguente alle 14:00."
> *Perché è positivo? Fornisce il tuo ruolo, il destinatario, l'oggetto, il motivo del posticipo e le alternative specifiche.*

---

### Principio 3: Assegnare un Ruolo (Persona)

Dire al modello *chi* deve essere è una delle tecniche più semplici ed efficaci. Assegnare un ruolo (o "persona") imposta il tono, lo stile, il livello di dettaglio e la base di conoscenza che il modello utilizzerà.

*   **Cosa significa:** Iniziare il prompt con una frase come "Agisci come un...", "Vesti i panni di un...", "Sei un...".
*   **Quando usarlo:** Quasi sempre. È particolarmente utile per ottenere risposte specialistiche o creative.

**Esempio Pratico:**

> ❌ **Negativo:** "Spiegami come funziona un buco nero."
> *Perché è negativo? La risposta potrebbe essere troppo tecnica o troppo semplicistica.*
> 
> ✅ **Positivo:** "Agisci come un astrofisico che parla a un gruppo di studenti delle scuole superiori. Spiega cos'è un buco nero usando analogie semplici e un linguaggio accessibile, evitando formule matematiche complesse."
> *Perché è positivo? Definisce l'esperto (astrofisico) e il pubblico (studenti), guidando il livello di complessità e lo stile della risposta.*

---

### Principio 4: Definire il Formato di Output

Se hai bisogno che la risposta segua una struttura precisa, chiedila esplicitamente. Questo è cruciale quando l'output dell'IA deve essere usato in un altro programma, in un report o in qualsiasi sistema che richieda dati strutturati.

*   **Cosa significa:** Specificare se vuoi un elenco, una tabella, un blocco di codice, un file JSON, un testo in Markdown, ecc. Puoi anche usare dei delimitatori (es. `###`, `'''`) per separare le sezioni del prompt.
*   **Quando usarlo:** Ogni volta che la struttura della risposta è importante quanto il suo contenuto.

**Esempio Pratico:**

> ❌ **Negativo:** "Confronta i pro e i contro di Python e JavaScript per lo sviluppo web."
> *Perché è negativo? Potresti ricevere un paragrafo lungo e difficile da analizzare.*
> 
> ✅ **Positivo:** "Crea una tabella in formato Markdown che confronti Python e JavaScript per lo sviluppo web lato backend. La tabella deve avere tre colonne: 'Caratteristica', 'Python' e 'JavaScript'. Includi almeno le seguenti caratteristiche: Ecosistema, Performance, Curva di apprendimento, Community."
> *Perché è positivo? Specifica il formato (tabella Markdown), la struttura (3 colonne con nomi precisi) e il contenuto minimo da includere.*

---

[< Indietro (Indice)](./index.md) | [Avanti (Tecniche di Base) >](./02-tecniche-di-base.md)
