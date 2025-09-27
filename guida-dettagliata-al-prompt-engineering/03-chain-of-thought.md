# 3. La Catena di Pensiero (Chain-of-Thought)

La tecnica della "Catena di Pensiero" (Chain-of-Thought o CoT) rappresenta uno dei più importanti progressi nel campo del prompt engineering. Invece di chiedere al modello una risposta diretta, lo si istruisce a "pensare ad alta voce", ovvero a esporre il processo di ragionamento passo dopo passo che lo porta alla conclusione.

Questo approccio migliora drasticamente l'accuratezza in compiti che richiedono logica, calcoli matematici o inferenze complesse.

---

### Il Problema del Ragionamento Complesso

I modelli linguistici sono eccellenti nel riconoscere e riprodurre pattern, ma possono fallire in problemi che richiedono più passaggi logici. Se chiedi direttamente la soluzione, il modello potrebbe tentare di "indovinare" la risposta basandosi su esempi simili visti durante l'addestramento, spesso sbagliando.

**Esempio Pratico: Un Problema di Logica**

Vediamo come un prompt standard fallisce e come il CoT risolve il problema.

> **Problema:**
> In una stanza ci sono 5 persone. Se ogni persona stringe la mano a ogni altra persona una sola volta, quante strette di mano ci sono in totale?

#### Tentativo 1: Prompt Standard (Senza CoT)

> **Prompt:**
> ```
> In una stanza ci sono 5 persone. Se ogni persona stringe la mano a ogni altra persona una sola volta, quante strette di mano ci sono in totale? Fornisci solo il numero finale.
> ```
> 
> **Output (probabile e sbagliato):**
> ```
> 20
> ```
> *Perché è sbagliato? Il modello potrebbe aver fatto un calcolo semplicistico come 5 * 4, senza considerare che la stretta di mano tra la Persona A e la Persona B è la stessa che tra B e A, e che una persona non stringe la mano a se stessa.*

#### Tentativo 2: Prompt con Chain-of-Thought

> **Prompt:**
> ```
> In una stanza ci sono 5 persone. Se ogni persona stringe la mano a ogni altra persona una sola volta, quante strette di mano ci sono in totale? Ragiona passo dopo passo prima di dare la risposta finale.
> ```
> 
> **Output (corretto):**
> ```
> Certo, analizziamo il problema passo dopo passo.
> 
> 1.  Chiamiamo le persone A, B, C, D, E.
> 2.  La persona A stringe la mano a 4 persone: B, C, D, E. (4 strette di mano)
> 3.  La persona B ha già stretto la mano ad A, quindi stringe la mano a 3 nuove persone: C, D, E. (3 strette di mano)
> 4.  La persona C ha già stretto la mano ad A e B, quindi stringe la mano a 2 nuove persone: D, E. (2 strette di mano)
> 5.  La persona D ha già stretto la mano ad A, B e C, quindi stringe la mano a 1 nuova persona: E. (1 stretta di mano)
> 6.  La persona E ha già stretto la mano a tutti.
> 
> Sommiamo tutte le strette di mano uniche: 4 + 3 + 2 + 1 = 10.
> 
> La risposta finale è 10.
> ```
> *Perché è corretto? Forzando il modello a esplicitare i passaggi, lo abbiamo guidato attraverso un processo logico che previene errori impulsivi e porta alla soluzione corretta.*

---

### Tipi di Chain-of-Thought

Esistono due modi principali per attivare la Catena di Pensiero:

1.  **Zero-Shot CoT:** È la versione più semplice, quella vista nell'esempio sopra. Si aggiunge al prompt una semplice frase magica come:
    *   "Ragiona passo dopo passo."
    *   "Let's think step by step."
    *   "Spiega il tuo ragionamento."
    Questa semplice istruzione è spesso sufficiente per attivare la modalità di ragionamento del modello.

2.  **Few-Shot CoT:** È un approccio ancora più robusto. Si fornisce al modello un esempio completo di un problema simile, già risolto con un ragionamento passo-passo. Questo non solo attiva il CoT, ma insegna anche al modello il *tipo* di ragionamento che desideri.

---

In sintesi, la tecnica CoT trasforma il modello da un "oracolo che indovina" a un "assistente che ragiona". È uno strumento indispensabile per aumentare l'affidabilità delle risposte in qualsiasi compito non banale.

---

[< Indietro (Tecniche di Base)](./02-tecniche-di-base.md) | [Torna all'Indice](./index.md) | [Avanti (Tecniche Avanzate) >](./04-tecniche-avanzate.md)
