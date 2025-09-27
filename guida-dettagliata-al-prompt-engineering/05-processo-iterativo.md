# 5. Il Processo Iterativo di Raffinamento

Le tecniche viste finora sono gli strumenti nella nostra cassetta degli attrezzi. Il processo iterativo è la metodologia con cui usiamo questi strumenti. Raramente si scrive un prompt perfetto al primo tentativo. L'eccellenza si raggiunge attraverso un ciclo di progettazione, esecuzione, analisi e raffinamento.

Il processo può essere riassunto in 4 fasi:

1.  **Progettazione:** Formula un'ipotesi su quale sia il modo migliore per chiedere qualcosa. Inizia semplice.
2.  **Esecuzione:** Invia il prompt al modello e ottieni un output.
3.  **Analisi:** L'output soddisfa i tuoi requisiti? È accurato? È nel formato giusto? Dove sono le lacune?
4.  **Raffinamento:** Modifica il prompt per colmare le lacune. Aggiungi chiarezza, contesto, esempi o una tecnica più avanzata. Ritorna al punto 2.

---

### Caso Pratico: Generare una Funzione Python

Vediamo questo ciclo in azione con un obiettivo da ingegnere del software: **"Generare una funzione Python per validare un indirizzo email."**

#### Iterazione 1: Il Prompt Iniziale (Vago)

> **Prompt:**
> ```
> Scrivi una funzione in Python che controlla se un'email è valida.
> ```
> 
> **Output (probabile):**
> ```python
> def is_valid_email(email):
>   if "@" in email and "." in email.split('@')[1]:
>     return True
>   else:
>     return False
> ```
> 
> **Analisi:**
> *   **Cosa non va:** Questa funzione è estremamente semplicistica. Considererebbe valide email come `"a@b.c"` o `"a@.c"`. Non gestisce casi limite e non è robusta.
> *   **Causa:** Il prompt era troppo vago. Non abbiamo definito cosa significa "valida".

#### Iterazione 2: Aggiungere Specificità (Usare Regex)

Raffiniamo il prompt specificando il *come*.

> **Prompt:**
> ```
> Scrivi una funzione in Python che controlla se un'email è valida. Usa le espressioni regolari (regex) per una validazione più accurata.
> ```
> 
> **Output (probabile):**
> ```python
> import re
> 
> def is_valid_email(email):
>   # Regex semplice per la validazione di un'email
>   regex = r'^[a-zA-Z0-9_.+-]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+$'
>   if re.match(regex, email):
>     return True
>   else:
>     return False
> ```
> 
> **Analisi:**
> *   **Miglioramento:** Ora la validazione è molto più robusta grazie all'uso di una regex.
> *   **Nuovo Problema:** La regex, sebbene migliore, potrebbe non essere perfetta. Inoltre, la funzione è "nuda": mancano commenti, docstring e type hints, rendendola poco professionale.

#### Iterazione 3: Aggiungere Esempi (Few-Shot) e Vincoli

Aggiungiamo esempi per gestire casi limite e chiediamo una funzione più completa.

> **Prompt:**
> ```
> Agisci come un programmatore Python senior. Scrivi una funzione che valida un indirizzo email usando una regex robusta.
> 
> La funzione deve restituire `True` per email come:
> - 'test.name@example.com'
> - 'user+alias@sub.domain.co.uk'
> 
> E deve restituire `False` per email come:
> - 'plainaddress'
> - '@missing-local-part.com'
> - 'user@.invalid-domain.com'
> 
> Assicurati che la funzione includa una docstring chiara e i type hints.
> ```
> 
> **Output (probabile e quasi perfetto):**
> ```python
> import re
> 
> def is_valid_email(email: str) -> bool:
>   """
>   Valida un indirizzo email usando una regex robusta.
> 
>   Args:
>     email: La stringa dell'indirizzo email da validare.
> 
>   Returns:
>     True se l'email è valida, False altrimenti.
>   """
>   if not isinstance(email, str):
>     return False
>   
>   # Regex conforme allo standard RFC 5322
>   regex = r"(^[a-zA-Z0-9_.+-]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+$)"
>   if re.fullmatch(regex, email):
>     return True
>   else:
>     return False
> ```
> 
> **Analisi:**
> *   **Miglioramento:** L'output è ora eccellente. La funzione è robusta, ben documentata e segue le best practice di Python. L'uso di esempi (Few-Shot) ha aiutato il modello a capire le sfumature della richiesta.

---

### Lezione Appresa

Questo caso pratico dimostra che il prompt engineering non è un'azione singola, ma un dialogo. Ogni iterazione, costruita sull'analisi dell'output precedente, ci avvicina al risultato desiderato. Partendo da un'idea vaga, abbiamo applicato i principi di **specificità**, **ruolo**, **esempi** e **formato** per guidare il modello verso la produzione di codice di alta qualità.

---

[< Indietro (Tecniche Avanzate)](./04-tecniche-avanzate.md) | [Torna all'Indice](./index.md) | [Avanti (Conclusione) >](./06-conclusione.md)
