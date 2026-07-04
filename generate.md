# MORGENLAGE — Erzeugungs-Anleitung (fuer die Cloud-Routine)

Du erzeugst die heutige Ausgabe des persoenlichen Briefings **MORGENLAGE** fuer Gerik
(Region: **Horb am Neckar**) und aktualisierst `data.json`. Frischer Cloud-Checkout, es
stehen Bash, WebSearch, WebFetch, Read/Write/Edit zur Verfuegung. Am Ende committen und pushen.

## Redaktions-Standard (BINDEND)
- Warm und persoenlich, wie von einem Assistenten ("Guten Morgen, Gerik"), aber **sachlich und
  neutral**: kein Boulevard, keine Meinung, keine Dramatisierung.
- **Ausfuehrlich:** jeder Beitrag 3 bis 6 Saetze mit echtem Hintergrund und der Einordnung
  "warum das zaehlt".
- **JEDES Fremdwort und JEDER Eigenname wird IM TEXT kurz erklaert** (nicht nur "Lucy", sondern
  "die NASA-Raumsonde Lucy"; nicht nur "EZB", sondern "die Europaeische Zentralbank, EZB").
  Setze NIE Vorwissen voraus.
- Korrektes Deutsch mit **echten Umlauten** (nicht ae/oe/ue/ss), keine Em- oder En-Striche.
- **Keine erfundenen Fakten.** Nur, was du in den Quellen findest. Zahlen und Namen genau. Findest
  du zu einer Rubrik nichts Belastbares, lass sie an diesem Tag weg.

## Schritt 1 — Wetter (Horb am Neckar)
`curl -s "https://api.open-meteo.com/v1/forecast?latitude=48.44&longitude=8.69&daily=weathercode,temperature_2m_max&timezone=Europe%2FBerlin&forecast_days=1"`
- Temperatur = `daily.temperature_2m_max[0]`, auf ganze Grad runden.
- Wettercode = `daily.weathercode[0]` -> `weather`-Feld:
  0-1 = `clear`; 2-3, 45, 48 = `clouds`; 51-67, 80-82 = `rain`; 71-77, 85, 86 = `snow`; 95-99 = `thunder`.
- `temp`-Feld z.B. `"27°C · sonnig"` (sonnig / bewoelkt / Regen / Schnee / Gewitter passend zum Code).

## Schritt 2 — Inhalte sammeln (WebSearch/WebFetch)
Aktuelle, serioese Meldungen von heute bzw. den letzten ein bis zwei Tagen, je Rubrik 1 bis 2:
- **Weltlage (neutral):** 1-2 wichtige, sachliche Meldungen. Bei Krieg/Leid nuechtern und OHNE Bild.
- **Wirtschaft:** 1-2 (Konjunktur, EZB, Deutschland/Euroraum). Ein `merk` (tone "g") ist erlaubt.
- **Lichtblick:** 1-2 echte positive Nachrichten (Quellen z.B. positive.news, goodnewsnetwork.org).
- **Forschung & Fortschritt:** 1-2 (z.B. sciencedaily.com).
- **Horb am Neckar:** 1-2 lokale Termine/Meldungen (horb.de/Veranstaltungen, meinestadt). Wo
  sinnvoll mit dem heutigen Wetter verknuepfen.
- **Handlungsempfehlungen (ans Ende):** 1-2 KI-/Entwickler-Signale mit konkretem Hebel fuer Geriks
  System (Quellen: simonwillison.net, Hacker News, GitHub, Model Context Protocol). Wo es passt,
  ein einsetzbares **Prompt-Snippet**.
- **Gelegenheitsradar (ans Ende):** heute 1 (an manchen Tagen mehr) konkrete, serioese Gelegenheit
  mit echtem Mehrwert fuer einen Solo-Unternehmer (Foerderung mit Frist, kostenlose Credits,
  nuetzliches Werkzeug). Nur echte, keine erfundenen.

## Schritt 3 — Bilder (optional, sachlich, darf faszinieren)
Nur fuer **Lichtblick, Forschung, Horb**: pro Beitrag EIN passendes Motiv. Es soll **sachlich zum
Thema passen und darf Neugier oder Faszination wecken** (z.B. ein eindrucksvolles Weltraum-, Natur-
oder Technik-Motiv), aber **nicht manipulieren und nicht auf Betroffenheit oder Schock zielen**:
keine inszenierten Gesichter, keine Elends- oder Angstbilder. Keyless von Openverse holen:
`curl -s "https://api.openverse.org/v1/images/?q=<neutraler+begriff>&license_type=commercial&page_size=1"`
Nimm `results[0].url` (direkter Bild-Link) ins `img`-Feld. Scheitert die Suche oder ist das Motiv
nicht klar neutral, lass `img` einfach weg. **Weltlage und Wirtschaft bekommen KEIN Bild.**

## Schritt 4 — data.json bauen
Lies das vorhandene `data.json`. Baue den heutigen Tag als neues Tages-Objekt und haenge es **ans
Ende** des `days`-Arrays (neuester Tag rechts / zuletzt). **Ist der letzte vorhandene Eintrag
bereits von heute (gleiches Datum), ersetze ihn, statt ein Duplikat anzuhaengen** (idempotent bei
Wiederholung). Behalte hoechstens die **letzten 7 Tage** (aelteste vorne entfernen). `sub` bleibt `"Dein tägliches Briefing"`. Nur der heutige (letzte) Tag
bekommt zusaetzlich `"colophon":"kuratiert aus deinen Quellen · Fotos: Openverse"`.

**Sektions-Reihenfolge (bindend):** zuerst die Nachrichten (Weltlage, Wirtschaft, Lichtblick,
Forschung, Horb am Neckar), dann `{"type":"thought",...}`, dann `{"type":"divider"}`, dann
`Gelegenheitsradar` (opp), dann `Handlungsempfehlungen` (action). **Aktionen immer ganz unten.**

### Schema (genau einhalten)
- `day` = `{ "label", "weather", "temp", "greeting", ["colophon"], "sections":[...] }`
  - `label`: `"<Wochentag>, <D>. <Monat> <Jahr>"` auf Deutsch (z.B. "Samstag, 4. Juli 2026").
- Sektionen:
  - `{"type":"news","icon":"globe|chart|leaf|flask|pin","eyebrow":"<Rubrik>","items":[{ "title","text",["img"],"source",["url"] }], ["merk":{"text":"...","tone":"g"}]}`
  - `{"type":"thought","text":"..."}`  (ein ruhiger Satz; OHNE Anfuehrungszeichen, die setzt die App)
  - `{"type":"divider"}`
  - `{"type":"opp","icon":"compass","eyebrow":"Gelegenheitsradar","item":{ "title","text","why","source",["url"] }}`
  - `{"type":"action","icon":"gauge","eyebrow":"Handlungsempfehlungen",["also":"..."],"cards":[{ "icon":"gauge|layers|terminal","title","tag","paras":["...","..."],["merk"],["konkret"],["snippetLabel"],["snippet"],"source",["url"],["score"] }]}`
- Icon je Rubrik: Weltlage=globe, Wirtschaft=chart, Lichtblick=leaf, Forschung=flask, Horb=pin,
  Gelegenheit=compass, Handlung=gauge. Im `snippet`: Zeilenumbrueche als `\n`, Platzhalter als
  `<span class="ph">{ ... }</span>`.

## Schritt 5 — Pruefen, committen, pushen
- `node -e "JSON.parse(require('fs').readFileSync('data.json','utf8')); console.log('ok')"` muss `ok` sagen.
- `git add data.json` ; `git commit -m "MORGENLAGE Tagesausgabe <DATUM>"` ; `git push`
- Bei abgelehntem Push: `git pull --no-rebase`, dann erneut.
Fasse zum Schluss 3 Zeilen zusammen: Wetter in Horb, die drei wichtigsten Meldungen, die Handlungsempfehlung des Tages.
