## 01 - Introduzione ai Valori dell'Architettura

Ogni sistema software fornisce due diversi e talvolta contrastanti oggetti di valore: il **comportamento** e la **struttura**.

### Comportamento (Urgenza)

Il primo valore, il comportamento, è ciò che il software *fa*. È l'insieme di funzionalità e requisiti che il sistema deve soddisfare. Quando un manager o un cliente chiede una nuova feature, sta chiedendo una modifica al comportamento. Questo valore è quasi sempre percepito come **urgente**. Un software che non funziona o che manca di una funzionalità critica richiede un'attenzione immediata.

### Struttura (Importanza)

Il secondo valore, la struttura (o architettura), è ciò che rende il software "soft", ovvero **facile da modificare**. Una buona architettura permette di aggiungere o cambiare funzionalità con uno sforzo proporzionale alla portata della modifica, non alla sua forma. Questo valore è **importante**, ma raramente percepito come urgente.

L'errore più comune è sacrificare l'importanza della struttura per l'urgenza del comportamento. Come dice Robert C. Martin, "programmare male è sempre più lento del programmare bene".

### La Matrice di Eisenhower applicata al Software

Una metafora efficace per descrivere questo conflitto: la matrice di Eisenhower, che classifica le attività in base alla loro urgenza e importanza.

| | URGENTE | NON URGENTE |
| :--- | :--- | :--- |
| **IMPORTANTE** | Crisi, problemi bloccanti | **Architettura, design, prevenzione** |
| **NON IMPORTANTE** | Interruzioni, meeting non necessari | Distrazioni, lavoro di routine |

*   **Comportamento (Features):** Spesso cade nel quadrante "Urgente", a volte importante (una crisi), a volte no (una richiesta minore).
*   **Architettura (Struttura):** Risiede quasi sempre nel quadrante "Importante ma non Urgente".

Il ruolo di un architetto e di un team di sviluppo senior è quello di difendere l'architettura, assicurandosi che le decisioni importanti non vengano costantemente posticipate a causa di quelle urgenti. Una buona struttura di directory è la prima e più visibile forma di una buona architettura.

### Caratteristiche di una Buona Architettura

Una buona architettura, e di conseguenza una buona struttura di directory, deve rendere un sistema:

*   **Indipendente dai Framework:** Le regole di business non devono dipendere dal framework scelto (es. React, Express, Django).
*   **Testabile:** Le regole di business devono poter essere testate senza UI, database, web server o altre dipendenze esterne.
*   **Indipendente dalla UI:** L'interfaccia utente può essere cambiata o sostituita facilmente senza impattare il resto del sistema.
*   **Indipendente dal Database:** Il core del sistema non deve sapere se i dati sono salvati su SQL, NoSQL o un file system.

Nei prossimi capitoli, vedremo come questi principi si traducono in pratica nell'organizzazione di file e cartelle.

---

[< Indietro (Indice)](./index.md) | [Avanti (I Principi SOLID) >](./02-i-principi-solid-nella-struttura-del-codice.md)