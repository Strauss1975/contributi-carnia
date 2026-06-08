# Dashboard Contributi Carnia 2026

Dashboard interattiva per i bandi e contributi disponibili per i Comuni di **Sutrio** e **Ampezzo** (UD), aggiornabile senza dipendere da API esterne.

## 🚀 Deploy su GitHub Pages (gratuito, ~5 minuti)

### 1. Crea il repository

1. Vai su [github.com](https://github.com) → **New repository**
2. Nome: `contributi-carnia` (o qualsiasi nome)
3. Visibilità: **Public** (GitHub Pages è gratis solo per repo pubblici)
4. Clicca **Create repository**

### 2. Carica i file

```
contributi-carnia/
├── index.html          ← dashboard principale
├── data/
│   └── bandi.json      ← i dati dei bandi (questo è il file da aggiornare)
└── README.md
```

**Metodo più semplice (senza Git):**
- Vai nel repository → **Add file** → **Upload files**
- Trascina tutti i file
- Clicca **Commit changes**

**Con Git:**
```bash
git clone https://github.com/TUO_USERNAME/contributi-carnia.git
# copia i file nella cartella
git add .
git commit -m "Prima versione dashboard"
git push
```

### 3. Abilita GitHub Pages

1. Nel repository → **Settings** → **Pages** (menu laterale)
2. Source: **Deploy from a branch**
3. Branch: **main** → cartella **/ (root)**
4. Clicca **Save**

Dopo 1-2 minuti la dashboard sarà live su:
```
https://TUO_USERNAME.github.io/contributi-carnia/
```

---

## 🔄 Come aggiornare i dati

### Aggiornamento manuale (più semplice)

1. Vai su GitHub → `data/bandi.json`
2. Clicca l'icona matita (✏️ Edit)
3. Modifica il JSON (es. cambia la scadenza, aggiorna lo stato, aggiungi un bando)
4. Clicca **Commit changes**

La dashboard si aggiorna entro qualche minuto (GitHub Pages si rideploya automaticamente).

### Aggiornamento automatico (opzionale, avanzato)

Aggiungi questo file in `.github/workflows/check-scadenze.yml` per ricevere avvisi automatici:

```yaml
name: Controlla scadenze
on:
  schedule:
    - cron: '0 8 * * MON'  # ogni lunedì alle 8
jobs:
  check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Verifica scadenze imminenti
        run: |
          python3 -c "
          import json, datetime
          data = json.load(open('data/bandi.json'))
          oggi = datetime.date.today()
          for b in data['bandi']:
              if b.get('scadenza'):
                  d = datetime.date.fromisoformat(b['scadenza'])
                  gg = (d - oggi).days
                  if 0 < gg <= 30:
                      print(f'⚠️  URGENTE: {b[\"titolo\"]} scade in {gg} giorni ({d})')
          "
```

---

## 📋 Struttura di bandi.json

```json
{
  "meta": {
    "aggiornato": "2026-06-07",      // data ultima modifica
    "aggiornato_da": "Ufficio tecnico",
    "versione": "1.0"
  },
  "comuni": [ ... ],   // dati anagrafici dei comuni
  "bandi": [
    {
      "id": "id-univoco",
      "titolo": "Nome del bando",
      "ente": "Ente erogatore",
      "tipo": "regionale",           // "regionale" o "nazionale"
      "ambito": "Sport",
      "scadenza": "2026-06-25",      // formato ISO YYYY-MM-DD, null se non c'è scadenza fissa
      "scadenza_testo": "25 giu 2026",
      "urgenza": "red",              // "red", "amber", "blue", "green"
      "stato": "aperto",             // "aperto", "in_attesa", "chiuso"
      "copertura_pct": 90,           // percentuale numerica per il grafico
      "docs": [...]                  // elenco documenti da allegare
    }
  ]
}
```

### Valori di `stato`
- `aperto` → bando attivo, domande accettate
- `in_attesa` → bando confermato ma non ancora aperto
- `chiuso` → bando scaduto (viene nascosto automaticamente)

### Aggiungere un nuovo bando

Copia uno dei blocchi esistenti in `bandi.json`, assegna un nuovo `id` univoco e compila i campi. La dashboard lo mostra automaticamente.

### Aggiungere un nuovo comune

Aggiungi un oggetto in `comuni` con gli stessi campi degli altri. Il tab comparirà automaticamente nella sidebar.

---

## 🛠 Funzionamento tecnico

- **Zero dipendenze esterne**: nessuna API, nessun server, nessun costo
- **Aggiornamento**: la dashboard fa `fetch('./data/bandi.json')` ad ogni caricamento
- **Cache locale**: i dati vengono salvati in `sessionStorage` per 1 ora, evitando richieste inutili
- **Pulsante Aggiorna**: forza il reload del JSON ignorando la cache
- **Funziona offline**: se il fetch fallisce, usa i dati in cache se disponibili
- **Responsive**: funziona su desktop, tablet e mobile

---

## 📁 File inclusi nel pacchetto

| File | Descrizione |
|---|---|
| `index.html` | Dashboard web interattiva (Sutrio + Ampezzo) |
| `data/bandi.json` | Dati aggiornabili di tutti i bandi |
| `README.md` | Questo file |

Separatamente sono disponibili anche le versioni PDF e HTML stampabili per ciascun comune.

---

*Comune di Sutrio (UD) · Comune di Ampezzo (UD) · Carnia — FVG*  
*Aggiornato al 7 giugno 2026*
