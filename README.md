# 🏃 DC Coach Tracker

Tool collaborativo per raccogliere dati dal **Daily Challenge** di Athletics Championship e stimare empiricamente il **coefficiente dell'allenatore** per ogni disciplina.

> Progetto personale — Club Aquile / SergioCosi

---

## Il problema

Nel gioco, il valore dell'allenatore influisce sulla prestazione fisica (e quindi sui DP), ma il coefficiente esatto non è documentato ufficialmente. Questo tool raccoglie osservazioni reali da più giocatori per stimarlo disciplina per disciplina.

## Come funziona

Ogni volta che giochi una gara del Daily Challenge, inserisci i dati di **una o più discipline**: la tua prestazione e quella dell'avversario, con i rispettivi valori di atleta e allenatore.

Il sistema poi trova automaticamente le **coppie comparabili** — cioè quelle dove l'atleta è simile (Δ ≤ 150 di default) ma l'allenatore è diverso — e calcola quanti DP vale mediamente una differenza di +200 coach.

### Formula utilizzata

```
coefficiente_disc = (ΔDP / Δcoach) × 200
```

Normalizzato a una differenza di +200 coach (cioè: passare da allenatore 800 ad allenatore 1000 vale X DP su quella disciplina).

---

## Struttura dell'app

L'app è una **singola pagina HTML** ospitata su GitHub Pages, con backend su Supabase.

### Tab Inserisci

1. Scrivi il tuo nick (si salva in automatico)
2. Scegli **Decathlon** (M) o **Heptathlon** (F)
3. Seleziona la disciplina dal menu a tendina
4. Inserisci i 4 valori per te + 4 per l'avversario: Prestazione, DP, Atleta 🔥, Allenatore 💧
5. Premi **Aggiungi** — il menu avanza automaticamente alla disciplina successiva

### Tab Sessione

Mostra le righe inserite nella sessione corrente, come verifica rapida prima di chiudere.

### Tab Analisi

Carica tutti i dati dal database e mostra per ogni disciplina:

| Metrica | Descrizione |
|---------|-------------|
| **Media DP** | Media del ΔDP per +200 coach, su tutte le coppie comparabili |
| **Mediana DP** | Più robusta agli outlier |
| **Coppie** | Quante coppie hanno soddisfatto il filtro Δatleta |

Il filtro **Max Δatleta** è regolabile: più è stretto, più i confronti sono puliti ma meno coppie si trovano.

---

## Discipline tracciate

### Decathlon (M)
100m · Lungo · Peso · Alto · 400m · 110H · Disco · Asta · Giavellotto · 1500m

### Heptathlon (F)
100m ost. · Alto · Peso · 200m · Lungo · Giavellotto · 800m

---

## Setup (una volta sola)

### 1. Database Supabase

Apri il **SQL Editor** del tuo progetto Supabase e incolla il contenuto di [`ac_dc_confronto_schema.sql`](./ac_dc_confronto_schema.sql), poi esegui.

Crea la tabella `ac_dc_confronto` con RLS abilitato:
- lettura pubblica (tutti vedono i dati aggregati)
- inserimento anonimo (chiunque può contribuire)

### 2. GitHub Pages

Nel repo, vai in **Settings → Pages → Source: Deploy from branch → main → / (root)** e salva.

L'app sarà disponibile su `https://<tuo-utente>.github.io/dctracker/`.

---

## File nel repo

```
dctracker/
├── index.html                  ← app completa (single-page)
├── ac_dc_confronto_schema.sql     ← SQL per creare la tabella su Supabase
└── README.md                   ← questo file
```

---

## Note tecniche

- **Frontend**: HTML/CSS/JS puro, nessuna dipendenza locale
- **Backend**: [Supabase](https://supabase.com) (PostgreSQL + API REST)
- **SDK**: `@supabase/supabase-js` v2 via CDN (jsdelivr)
- La anon key di Supabase è incorporata nell'HTML — è la chiave pubblica, progettata per il frontend

---

## Contribuire

Chiunque del club può usare l'app inserendo il proprio nick. Più dati ci sono, più le stime del coefficiente convergono. Non serve account, non serve login — basta il nick.

Se l'avversario non ha giocato (VT), puoi comunque inserire i tuoi dati lasciando vuoti i campi avversario: contribuiranno alle analisi future quando si accumuleranno dati incrociati tra utenti diversi.
