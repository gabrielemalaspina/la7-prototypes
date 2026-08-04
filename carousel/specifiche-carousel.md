# Carousel — Specifiche layout responsive (v2)

Prototipo interattivo: `la7-prototypes/carousel/index.html`

Gli oggetti scalano proporzionalmente al viewport tramite vw. Il numero di oggetti visibili (N) non è definito esplicitamente: emerge da peek, min/max e padding.

## Formula

```
item-width = (100vw - padding - gap × (N - 0.5)) / N
```

`padding` nella formula si riferisce al solo lato sinistro (offset visivo prima del primo oggetto). Il `padding-right` è presente nel container per simmetria visiva a fine scroll, ma non entra nel calcolo.

Il valore di padding nella formula varia per breakpoint (mobile/desktop) indipendentemente dal cambio di N.

## Regole di sistema

- Peek fisso: 0.5 — l'ultimo oggetto è (in linea di principio) visibile al 50% per segnalare lo scroll
- N è sempre nella forma n.5 (es. 1.5, 2.5, 3.5…)
- Quando l'oggetto raggiunge max-width, N aumenta di 1 e l'oggetto riparte da un valore inferiore (dente di sega)
- Il carousel mostra un solo tipo di oggetto alla volta, mai misti (es. solo video)
- **Limite noto:** il peek 0.5 è garantito solo nella fascia di viewport in cui l'oggetto segue la formula "pura", cioè tra i punti in cui il valore calcolato incrocia min-width e max-width. Quando l'oggetto è agganciato (clampato) a min o max, il numero reale di elementi visibili e la percentuale di peek dell'ultimo elemento si discostano dalla regola N.5 — a quelle risoluzioni l'ultimo elemento può risultare visibile per intero, o per una quota diversa dal 50%. È una conseguenza diretta della scelta di min/max rispetto ai breakpoint, non un'eccezione da correggere: il clamp ha priorità sul peek per garantire la leggibilità dell'oggetto.

## Parametri griglia

|         | Mobile | Desktop |
|---------|--------|---------|
| Padding | 16px   | 64px    |
| Gap     | 8px    | 16px    |

(soglia mobile/desktop: 1024px)

## Video 16:9 — min 220px / max 320px

| Viewport | N   | Item  |
|----------|-----|-------|
| 375px    | 1.5 | 234px |
| 768px    | 2.5 | 295px |
| 1024px   | 3.5 | 261px |
| 1440px   | 4.5 | 292px |

## Poster 3:4 e Podcast 1:1 — min 128px / max 256px

| Viewport | N   | Item  |
|----------|-----|-------|
| 375px    | 2.5 | 137px |
| 768px    | 3.5 | 160px |
| 1024px   | 4.5 | 199px |
| 1440px   | 5.5 | 236px |

## Article — min 300px / max 380px *(NUOVO)*

Layout orizzontale: thumb quadrata (40% larghezza, aspect-ratio 1:1) a sinistra + contenuto testuale a destra (data, titolo su 3 righe, abstract su 2 righe).

N dedicato — **non** riusa lo schema del tipo Video: il range dimensionale (300–380px) è più stretto e più alto, quindi richiede una propria sequenza.

| Viewport | N   | Item              |
|----------|-----|-------------------|
| 375px    | 1.5 | 300px (clampato)  |
| 768px    | 2.5 | 300px (clampato)  |
| 1024px   | 2.5 | 371px (reale)     |
| 1440px   | 3.5 | 379px (reale)     |

Nota: N resta 2.5 sia nella fascia 768–1023px sia in quella 1024–1439px — il solo cambio di padding/gap a 1024px basta a far rientrare il valore sotto il max, senza bisogno di incrementare N.

**Regola specifica Article:** su mobile (< 768px) il campo data non viene mostrato — solo titolo e abstract.

## Note implementative

- N e i breakpoint intermedi sono derivati dalla formula
- Nuovi formati si aggiungeranno definendo solo min-width, max-width e aspect ratio; il sistema (formula, breakpoint 768/1024/1440, gestione N dedicata per formato) non cambia
- Sopra 1440px il meccanismo dente di sega continua: l'oggetto cresce fino a max-width, poi N aumenta (attualmente non sono definiti breakpoint oltre 1440px)
- Per ogni nuovo formato va verificato se lo schema N di un formato esistente è riutilizzabile, oppure se serve una sequenza dedicata (vedi caso Article)

---

## Delta rispetto alle specifiche precedenti

1. **Nuovo formato: Article** — layout orizzontale (thumb + testo), non presente nella versione precedente del documento (che copriva solo Video 16:9 e Poster 3:4 / Podcast 1:1).
2. **N dedicato per Article**: 1.5 / 2.5 / 2.5 / 3.5 (non lo schema Video 1.5/2.5/3.5/4.5, incompatibile con il range dimensionale di Article).
3. **Min-width Article**: partito da 311px → ridotto a **300px** (cifra tonda, nessun impatto sullo schema N).
4. **Max-width Article**: calcolato inizialmente a ~379px → arrotondato a **380px** (cifra tonda; a 1440px il valore ora rientra "reale", senza clamp).
5. **Data nascosta su mobile per Article**: sotto i 768px la card mostra solo titolo e abstract, comportamento voluto e non presente per gli altri formati.
6. **Nuova regola di sistema**: chiarito esplicitamente che il peek 0.5 non è garantito in ogni risoluzione — vale solo nella fascia "reale" tra i due clamp. È un limite noto del sistema attuale, non una singola eccezione di Article (capita anche su Video/Poster nelle fasce clampate visibili nel prototipo, es. N 3.5/4.5 già clampati a 1024/1440px per Video).
