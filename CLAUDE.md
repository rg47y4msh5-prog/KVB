# KVB — Klein Vaarbewijs oefenmateriaal

Statische site op GitHub Pages: https://rg47y4msh5-prog.github.io/KVB/
Repo: rg47y4msh5-prog/KVB, branch `main`, geen build-stap — wat in de repo staat, staat live.

## Rolverdeling (belangrijk)

- **Dit repo + Claude Code**: de leeromgeving en de site bouwen en onderhouden.
- **Claude.ai-project "KVB"**: bron van waarheid voor examencontent (vragenbanken, referenties, artikelverificatie tegen wetten.overheid.nl). Nieuwe of gewijzigde vrágen worden dáár gemaakt en gevalideerd, niet hier. Hier mag je vragen wel herstructureren/verplaatsen, nooit inhoudelijk herschrijven.
- **Claude.ai-project "HTML asset design review"**: reviewpijplijn voor visuals. Nieuwe SVG-assets lopen via dat project.

## Harde conventies

1. **Alles standalone.** Elk HTML-bestand werkt met een dubbelklik: CSS, JS en SVG's inline, geen externe requests, geen frameworks, geen build. De assetgalerijen embedden hun SVG's als base64-data-URI's met `loading="lazy"`.
2. **Bankstructuur KVB1** (`kvb1-oefenbank.html`): `const SLOTS=[["A.1",1],…]` (40 slots) en `const BANK=[{s,t,o,u,a,v?},…]` (207 vragen). Velden: s=slot, t=vraagtekst, o=3 opties waarvan **de eerste altijd het juiste antwoord is** (de engine schudt), u=uitleg, a=artikel/toetsterm-verwijzing, v=optionele inline SVG-visual. Gedeeld defs-blok als verborgen `<svg width="0">` direct na `<body>` (ids: ah, kschip, gschip, zboot, sschip, cb26, pont, wroos).
3. **Bankstructuur KVB2** (`kvb2-oefenbank.html`): `const BANK={}` object per slotcode, gevuld met `BANK["E.1"]=[…]`-blokken; 27 slots, 108 vragen; meerkeuze plus invulvragen (`typ:"in"` met `ans`, `marge`, `fmt`, `eh`). Datasectie eindigt vóór de delimiter `/* ===== ENGINE ===== */` — wijzigingen aan de data altijd vóór die delimiter, de engine erna niet aanraken zonder expliciete opdracht.
4. **Vraagvisuals verklappen nooit het antwoord.** Alleen de situatie plus een neutrale vraagregel; geen labels als "loefwaarts", geen bordomschrijvingen. Uitlegvisuals mogen wél benoemen.
5. **Element-ids in bankvisuals zijn uniek per vraag** (suffix per slot, bv. `ah_E3`), omdat de reviewmodus alle visuals tegelijk rendert.
6. **Origineel werk.** Vragen zijn eigen parafrases (geen CBR-kopieën), SVG's eigen tekeningen (boekpagina's zijn stijlreferentie, geen overtrekvoorbeeld).

## Validatie (draaien na élke wijziging aan een oefenbank)

- Node: extraheer SLOTS/BANK via `new Function(code + '; return {SLOTS,BANK};')()` en controleer: aantal slots (40/27), aantal vragen, per-slot-minimum (KVB1 ≥3, KVB2 ≥4), 3 unieke opties per mc-vraag, velden t/u/a aanwezig, geen dubbele vraagteksten.
- Python: elke `v`-visual door `xml.etree.ElementTree.fromstring()`; geen dubbele ids over alle visuals samen; alle `href="#…"`/`url(#…)`-referenties resolven (eigen visual of gedeeld defs-blok).
- Links: alle `href`/`src` relatief en bestaand; geen absolute paden; geen externe URL's.
- Diff-check bij merges: bestaande vragen moeten byte-identiek behouden blijven tenzij de opdracht expliciet anders zegt.

## Deployment

`git add -A && git commit && git push` naar `main`; Pages bouwt binnen ±1 minuut. Geen force-push. Vóór elke push de validaties draaien.

## Geparkeerd (op te pakken bij de leeromgeving)

- **VIS-register**: scènes ontkoppelen van vragen via `const VIS={id:"<svg…>"}` + `vid`-verwijzing per vraag, engine-lookup `VIS[q.vid]||q.v`. Doel: één scène, meerdere vragen (scène-eerst schrijven); leak-scan dan per combinatie scène × vraag.
- **Labelconventie assets**: `<g class="art">` / `<g class="lbl">` scheiden zodat vraagvarianten mechanisch te strippen zijn; verplicht voor nieuw Design-werk.


## Bronmateriaal (map `bron/` — LOKAAL, nooit committen)

- `bron/` staat in `.gitignore` en mag nooit naar de publieke repo: het cursusboek is auteursrechtelijk beschermd.
- `bron/bron_cursusboek_tekst.md` — volledige tekstlaag van het cursusboek (16e druk 2025), per gedrukte pagina gemarkeerd met `=== pagina N ===`. Dit is de snelste ingang: grep op onderwerp, lees de relevante pagina's. De lesopbouw (KVB1 les 1–4 ≈ p4–163, KVB2 les 5–8 ≈ p164–265) is de blauwdruk voor de leeromgeving-structuur.
- `bron/p{nr}.jpg` (of `*_Page_{nr}.jpg`) — paginascans. Alleen per pagina openen wanneer een figuur nodig is als stijl-/detailreferentie; nooit als geheel inlezen (context) en nooit reproduceren.
- Werkwijze: structuur en formuleringen altijd eerst uit de tekstlaag halen; een JPG er alleen bij pakken als de tekst naar een figuur verwijst. Alles wat in de site belandt is eigen parafrase en eigen tekening.

## Leeromgeving (het doel)

Opbouw volgt het cursusboek (KVB1 les 1–4, KVB2 les 5–8): per les theorie → visual → oefenvragen, met voortgang per les en doorverwijzing naar de oefenbanken voor de examensimulatie. Zelfde standalone-filosofie; de vragenbanken zijn de databron en blijven leidend.
