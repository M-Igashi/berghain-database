# Data Policy — What Counts as a Performance

This document defines which events and performances are included in the
database, and how ambiguous cases are resolved. It is the canonical reference
for all imports and corrections.

## Scope: the Saturday Klubnacht slot

The database catalogs club nights that occupy the **Saturday Klubnacht slot**
— nights starting Saturday 23:00 or later on the **Berghain main floor**
and/or **Panorama Bar** — from opening night (18 December 2004) to present.

### Included

| Event type | Notes |
| --- | --- |
| Klubnacht | The flagship weekend event, every Saturday |
| SNAX Club / FC Snax United | The Berghain-floor lineup runs through the Klubnacht hours in parallel with Panorama Bar; counted since the 2026-07 policy change ([#6](https://github.com/M-Igashi/berghain-database/issues/6)) |
| Label nights in the Saturday slot | e.g. *Playhouse versus Klang Elektronik* (2006), *10 Years Highgrade* (2010), *Freude am Tanzen Nacht* (2009) |
| Anniversary nights | *N Jahre Berghain*, held on a Saturday each December |
| Silvester / New Year specials | Imported on their calendar date (often Jan 1) |
| Oster (Easter) Klubnachts | Including Sunday-start editions (e.g. *Oster-Sonntags-Klubnacht* 2016) |
| "Sonntags" slots | The Sunday continuation printed as its own section is counted as **Panorama Bar**, matching how berghain.berlin presents it today |

### Excluded

| Excluded | Reason |
| --- | --- |
| Lab.oratory, Säule (formerly XXX / "+ + +"), Garten, Halle, Kantine am Berghain | Not the two core floors |
| Friday and weekday series (*…get perlonized!*, *Kompaktorama*, *Finest Friday*, Friday label nights, *Leisure System*, etc.) | Not in the Klubnacht slot |
| Panorama-Bar-only specials (*Holy Saturday/Sunday/…*, *Finest X-Mas*, *Feel My Bicep, Johnson!*, standalone *Finest Saturday* nights) | A single-floor party is not a Klubnacht, even on a Saturday or holiday |
| Concerts, theatre, Elektroakustischer Salon | Not club nights |

## Parallel events on one night

When SNAX Club and a Finest Saturday / Klubnacht run in parallel (the annual
Easter Saturday and November *FC Snax United* weekends), they are **merged
into one event per night**: the SNAX Berghain floor joins the event, the
title becomes e.g. `SNAX Club + Oster Klubnacht` or
`FC Snax United + Finest Saturday Klubnacht`, and the SNAX Lab.oratory floor
stays excluded. SNAX has its own event page (today on
[lab-oratory.de](https://www.lab-oratory.de/)), so crawls of the Klubnacht
page alone always miss its Berghain floor — every Easter and November import
must check for it.

## Sources and authority

| Era | Primary source | Notes |
| --- | --- | --- |
| Dec 2004 – Oct 2009 | Official monthly flyer archive (61 flyers) | Visually verified against the original PDFs; events keyed as `YYYYMMDD` |
| Nov 2009 – present | berghain.berlin event pages (official event ids) | For 2010–2011 the current pages are lossy (Saturday-night sets were dropped when running orders were re-rendered); Wayback Machine captures of the berghain.de listings are authoritative there |
| Current SNAX events | lab-oratory.de | Not listed in the berghain.berlin program |

Artist names are normalized and cross-referenced against Resident Advisor and
Discogs. Alias (`aka`) and duo/collective handling is documented in
[Aliases & Collective Acts](aliases-and-units.md).

## Event fields

- `total_artists` — the number of billed performances (an artist billed on
  both floors counts twice, matching the source billing).
- `event_id` — official berghain.berlin id for the web era; `YYYYMMDD` for
  the flyer era. When two official pages are merged (SNAX + Finest), the
  Klubnacht/Finest page id is kept.
