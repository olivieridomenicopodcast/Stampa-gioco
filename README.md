# stampa-gioco

Istruzioni per Claude: creare il kit **stampa e gioca** (carte formato poker 9 per A4, tabellone, plance, foglio punti, PDF) di un gioco da tavolo in playtest, con tanto di pulsante di download nell'app.

**Come si usa:** apri una sessione con questo repository **e** il repository del gioco, poi scrivi: *«Segui le istruzioni nel repo stampa-gioco»*.

Riferimento già fatto: Cromozapd (`olivieridomenicopodcast/cromozapd-`, branch `ccr-5b3c90aa-gh0360`): `tools/build-print.js`, `stampa/index.html`, `js/ui/sprites.js`, test «kit stampabile» in `tests/engine.test.js`, pulsante in `index.html` e cache in `sw.js`.

---

## ISTRUZIONI (da seguire alla lettera)

Ruolo: stai lavorando al prototipo digitale di un mio gioco da tavolo per il playtest. Parla e scrivi SEMPRE in italiano. Lavora solo sul branch indicato dalla sessione (commit + push, NESSUNA pull request se non te la chiedo).

### Obiettivo
Voglio poter giocare il gioco con carta e forbici. Fai un controllo completo di tutto ciò che il gioco richiede fisicamente e produci tutto ciò che si può stampare. I token fisici (segnalini, dadi, pedine) NON vanno creati: se servono, elencali alla fine, con quantità e suggerimento di cosa usare al loro posto.

### Passo 1 — Inventario (prima di creare qualsiasi cosa)
Leggi regolamento, codice e grafiche già presenti e fammi un elenco di ogni componente fisico richiesto dalle regole: mazzi (con quantità esatte per tipo di carta), carte speciali o di riferimento, tabelloni o tappeti, plance giocatore, fogli punti o segnapunti, schede riassuntive, regolamento. Per ognuno: cosa è già disponibile come grafica nell'app, cosa manca, cosa è stampabile e cosa no. Confronta le quantità con quelle del regolamento e del codice, e segnalami ogni incongruenza invece di risolverla in silenzio. Se ti manca un'informazione, chiedimela solo se non puoi ricavarla dal progetto.

### Passo 2 — Creazione
Crea tutto ciò che è stampabile, riusando le grafiche dell'app (sprite SVG) dove esistono e disegnando in codice quelle che mancano. Requisiti:
- Carte nel formato carte da poker (63,5 × 88,9 mm), 9 per foglio A4 (3×3, griglia centrata), con segni di taglio nei margini, fuori dalle carte. Controlla che il rapporto delle grafiche coincida con quello della carta (altrimenti adatta le grafiche, non deformarle).
- Se ci sono mazzi con dorso diverso, ogni carta ha il suo dorso giusto. Per il fronte/retro, le colonne del retro vanno SPECCHIATE (ribaltamento sul lato lungo). Se le carte da stampare non riempiono l'ultimo foglio, riempi gli spazi con carte speciali o di riferimento che si tengono scoperte.
- Due versioni dei PDF delle carte: "fronte/retro" (pagine alternate) e "solo fronti" (per bustine con una carta qualunque dietro).
- Tabellone, zone di gioco e plance in A4 orizzontale (o verticale se più sensato), con i posti dove appoggiare le carte nella dimensione reale (un po' più grandi della carta, per posarla senza precisione), etichette chiare e un promemoria del turno e del punteggio. Non inventare regole: il testo deve riflettere esattamente il regolamento; se una pratica da tavolo non è nel regolamento, segnalala come [interpretazione] e dimmelo.
- Foglio punti stampabile, se il conteggio al tavolo ha passaggi non banali (con un esempio compilato), e il regolamento in PDF.
- Tutto deve essere generato da uno script nel repository (es. tools/build-print.js), non a mano, in modo che si rigeneri quando cambiano le regole. I PDF si fanno con Chromium/Playwright (già installati: usa il Chromium preinstallato, non scaricare nulla).
- Un file stampa/index.html con: cosa stampare, quanti fogli, impostazioni di stampa (A4, scala 100% e non "adatta alla pagina", fronte/retro sul lato lungo), cosa tagliare, cosa tenere fuori dal mazzo, preparazione del tavolo.

### Passo 3 — Integrazione nell'app
Nella schermata iniziale dell'app aggiungi un pulsante ben visibile (es. "🖨 Stampa e gioca") che apra stampa/index.html, da cui si scaricano direttamente i PDF. Aggiungi i file stampabili HTML alla cache offline del service worker (e incrementa la versione della cache). I PDF vanno committati nel repository.

### Passo 4 — Verifica (obbligatoria, non basta che gli script girino)
- Converti in immagine le pagine dei PDF (es. pdftoppm) e GUARDALE: controlla testi che escono dai bordi, sovrapposizioni, carte tagliate, dorsi sbagliati, valori sbagliati. Correggi e ricontrolla finché è pulito.
- Verifica la scala reale: la carta stampata deve essere 63,5 × 88,9 mm e le pagine A4.
- Aggiungi un test automatico che controlli conteggi (carte totali, per mazzo, numero di fogli), formato e assenza di "undefined"/"NaN" nei file generati.
- Lancia tutti i test esistenti e una prova dell'app nel browser (Chromium/Playwright) per accertarti di non aver rotto niente.

### Regole di lavoro
- Procedi a piccoli passi e riporta con onestà cosa hai verificato e cosa NO (per esempio: non puoi sapere come la mia stampante allinea il fronte/retro).
- Se qualcosa nelle regole ti sembra assurdo, mancante o contraddittorio: non cambiarlo da solo. Analizza, proponi, e aspetta la mia decisione.
- Alla fine, riassumi: cosa ho da stampare (file, numero di fogli), cosa devo tagliare, quali token fisici mi servono e che cosa ho lasciato fuori o in dubbio.

### Riferimento
Ho già fatto la stessa cosa per un altro mio gioco, Cromozapd (repository `olivieridomenicopodcast/cromozapd-`, branch `ccr-5b3c90aa-gh0360`). Se riesci ad accedervi, guarda come l'ho fatto lì e prendilo come modello, adattandolo a questo gioco senza copiarlo alla cieca:
- `tools/build-print.js`: lo script che genera tutto (carte 3×3 con segni di taglio, retro specchiato, tabellone, zone, plance, PDF via Playwright);
- `stampa/index.html`: la pagina di istruzioni con i link ai PDF;
- `js/ui/sprites.js`: come sono disegnate in codice le carte (SVG con rapporto 100×140, uguale a quello poker);
- il test "kit stampabile" in `tests/engine.test.js`: controlla conteggi e formato;
- `index.html` e `sw.js`: il pulsante nell'app e la cache offline.

Se non riesci ad accedere a quel repository, procedi comunque da solo seguendo i passi sopra.
