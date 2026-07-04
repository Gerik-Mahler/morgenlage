# MORGENLAGE

Persoenliches taegliches Briefing fuer Gerik (Weltlage, Wirtschaft, Lichtblick, Forschung,
Horb am Neckar, Gelegenheitsradar, Handlungsempfehlungen). Warm gestaltet, wetter-adaptives
Titelbild, Swipe durch die letzten Tage.

## Aufbau
- `index.html` - stabile Render-Engine (Layout, Swipe, Wetter-Hero). Aendert sich selten.
- `data.json` - die Inhalte der letzten 7 Tage. Wird taeglich von der Cloud-Routine geschrieben.
  Fehlt sie, rendert `index.html` eingebettete Beispieldaten (Fallback).

Gehostet ueber GitHub Pages. Langfristig zieht das Briefing in die Texura-App (M9) um.
