# 4. Styling Avanzato e Animazioni

L'IA è uno strumento potentissimo per tradurre il linguaggio umano del design (es. "un avviso vistoso ma non aggressivo") in codice CSS. In questo capitolo vedremo come guidare l'IA per creare componenti stilizzati con Tailwind CSS, gestire varianti complesse e aggiungere animazioni fluide.

Useremo un unico componente, un `Alert`, e lo miglioreremo passo dopo passo.

---

### Caso 1: Da Descrizione a UI con Tailwind CSS

Partiamo da una descrizione puramente testuale. L'IA è sorprendentemente brava a interpretare il linguaggio del design.

**Prompt:**

> Crea un componente `Alert` in React e TypeScript. Deve essere un componente stateless.
> 
> Requisiti di stile (usa Tailwind CSS):
> 1.  Un contenitore principale con angoli arrotondati, un bordo sottile e un padding interno.
> 2.  All'interno, deve avere un'icona di informazione sulla sinistra.
> 3.  Accanto all'icona, un titolo in grassetto e sotto il titolo un testo descrittivo con un colore più tenue.
> 4.  Usa colori a tema "informativo" (es. sfumature di blu).

**Output Generato (previsto):**

```tsx
import type { FC } from 'react';

// Un'icona SVG di esempio. In un progetto reale, useresti una libreria di icone.
const InfoIcon: FC = () => (
  <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" strokeWidth={1.5} stroke="currentColor" className="h-6 w-6 text-blue-500">
    <path strokeLinecap="round" strokeLinejoin="round" d="m11.25 11.25.041-.02a.75.75 0 0 1 1.063.852l-.708 2.836a.75.75 0 0 0 1.063.853l.041-.021M21 12a9 9 0 1 1-18 0 9 9 0 0 1 18 0Zm-9-3.75h.008v.008H12V8.25Z" />
  </svg>
);

interface AlertProps {
  title: string;
  children: React.ReactNode;
}

const Alert: FC<AlertProps> = ({ title, children }) => {
  return (
    <div role="alert" className="rounded-lg border border-blue-300 bg-blue-50 p-4 dark:bg-blue-950">
      <div className="flex items-start space-x-4">
        <div className="flex-shrink-0">
          <InfoIcon />
        </div>
        <div className="flex-1">
          <h5 className="font-semibold text-blue-800 dark:text-blue-200">{title}</h5>
          <p className="text-sm text-blue-700 dark:text-blue-300">{children}</p>
        </div>
      </div>
    </div>
  );
};

export default Alert;
```

**Analisi:**
L'IA ha tradotto correttamente ogni requisito descrittivo in classi Tailwind. "Angoli arrotondati" è diventato `rounded-lg`, "bordo sottile" `border`, "padding interno" `p-4`. Ha usato `flex` per allineare gli elementi e ha scelto una palette di colori blu coerente. Ha persino generato un'icona SVG come placeholder.

---

### Caso 2: Gestire Varianti di Stile con `cva`

Un `Alert` non è quasi mai di un solo tipo. Come gestiamo le varianti `success`, `warning`, `destructive`? Invece di scrivere un `switch` manuale, chiediamo all'IA di usare una libreria specifica: `class-variance-authority`.

**Prompt:**

> Prendi il componente `Alert` precedente.
> Refattorizzalo usando la libreria `class-variance-authority` (cva) per gestire 4 varianti di `intent`: 'info', 'success', 'warning', 'destructive'.
> 
> Ogni variante deve avere colori di sfondo, testo, bordo e icona appropriati, mantenendo la coerenza visiva.

**Output Generato (previsto):**

```tsx
// ... (import e icone per ogni variante)
import { cva, type VariantProps } from 'class-variance-authority';

const alertVariants = cva(
  'rounded-lg border p-4',
  {
    variants: {
      intent: {
        info: 'border-blue-300 bg-blue-50 text-blue-700',
        success: 'border-green-300 bg-green-50 text-green-700',
        warning: 'border-yellow-300 bg-yellow-50 text-yellow-700',
        destructive: 'border-red-300 bg-red-50 text-red-700',
      },
    },
    defaultVariants: {
      intent: 'info',
    },
  }
);

// L'interfaccia ora estende le props delle varianti
interface AlertProps extends React.HTMLAttributes<HTMLDivElement>, VariantProps<typeof alertVariants> {
  title: string;
}

const Alert: FC<AlertProps> = ({ className, intent, title, children }) => {
  // ... (logica per scegliere l'icona giusta in base all'intent)
  return (
    <div role="alert" className={alertVariants({ intent, className })}>
      {/* ... JSX con icona e testi ... */}
    </div>
  );
};
```

**Analisi:**
Chiedere esplicitamente di usare `cva` è una mossa da professionisti. L'IA capisce che non vogliamo una logica condizionale sparsa nel JSX, ma una soluzione di design system pulita e centralizzata. Il codice generato è ora molto più manutenibile e facile da estendere.

---

### Caso 3: Aggiungere Animazioni con Framer Motion

Un'interfaccia moderna è anche un'interfaccia viva. Aggiungiamo un tocco di classe con un'animazione di entrata, usando un'altra libreria standard: `Framer Motion`.

**Prompt:**

> Prendi il componente `Alert` refattorizzato con `cva`.
> Modificalo per usare `Framer Motion`.
> 
> Aggiungi un'animazione in cui l'alert, quando viene montato, appare con un fade-in (da opacità 0 a 1) e un leggero slide-up verticale (da 10px più in basso alla sua posizione finale).

**Output Generato (previsto):**

```tsx
// ... (import di cva, etc.)
import { motion } from 'framer-motion';

// ... (definizione di alertVariants come prima)

const Alert: FC<AlertProps> = ({ className, intent, title, children }) => {
  // ...
  return (
    <motion.div
      role="alert"
      className={alertVariants({ intent, className })}
      initial={{ opacity: 0, y: 10 }}
      animate={{ opacity: 1, y: 0 }}
      transition={{ duration: 0.3, ease: 'easeOut' }}
    >
      {/* ... JSX interno dell'alert ... */}
    </motion.div>
  );
};
```

**Analisi:**
L'IA ha correttamente:
1.  Sostituito il `div` principale con `motion.div`.
2.  Aggiunto la prop `initial` per definire lo stato di partenza dell'animazione (invisibile e spostato in basso).
3.  Aggiunto la prop `animate` per definire lo stato finale (visibile e nella sua posizione normale).
4.  Aggiunto una `transition` per rendere l'animazione fluida.

In pochi secondi, abbiamo trasformato un componente statico in uno dinamico e professionale, semplicemente descrivendo l'effetto visivo desiderato e nominando la libreria da usare.

---

[< Indietro (Hooks e Dati)](./03-hooks-e-gestione-dati.md) | [Torna all'Indice](./index.md) | [Avanti (Next.js) >](./05-nextjs-15-server-centric.md)
