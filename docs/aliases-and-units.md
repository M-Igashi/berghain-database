# Aliases & Collective Acts

How this database handles DJs who record under multiple names, and duos / collectives whose members also play solo.

A single human can appear on a lineup under several names, and a back-to-back duo is really two soloists sharing a slot. To keep rankings, search, and per-artist histories accurate, the dataset normalises both cases. This page documents every consolidation made so far (two passes: **May 2026** and **June 2026**).

> Scope: this page covers **alias `aka` notation** and **collective member credits** only. Plain spelling-typo fixes and silent record merges are not listed here.

---

## 1. Aliases — `aka` notation

When one person performs under two (or more) names **and both names have their own performance history in the database**, the entries are merged into a single artist and labelled:

```
Primary Name aka Alias
```

The **primary** is the better-known / more-played name. Aliases that have *no* separate history in the data are left untouched — the goal is to reflect what actually appears in the lineups, not to maintain an exhaustive external discography.

### Annotated aliases

| Canonical entry | Real person / note |
| --- | --- |
| **Answer Code Request aka Patrick Gräser** | Patrick Gräser is the real name behind Answer Code Request (Ostgut Ton) |
| **Truncate aka Audio Injection** | Both are aliases of David Flores |
| **Cajmere aka Green Velvet** | Curtis Alan Jones' two house/techno personas |
| **The Blessed Madonna aka The Black Madonna** | Marea Stamper (renamed the project in 2020) |
| **Ed Davenport aka Inland** | Same producer, two names |
| **Steve Rachmad aka STERAC** | Dutch techno veteran's main alias |
| **Scuba aka SCB** | Paul Rose's two aliases |
| **Daniel Bell aka DBX** | Detroit minimal-techno pioneer |
| **Shed aka Head High aka EQD** | René Pawlowitz — three of his aliases in the data |
| **Planetary Assault Systems aka Luke Slater aka L.B. Dub Corp** | Luke Slater's main techno aliases |
| **Soundstream aka Soundhack** | Both are aliases of Frank Timm |

### Intentionally kept separate

- **Floorplan** / **Robert Hood** — although Floorplan began as Robert Hood's alias, it is now a distinct live act (with his daughter Lyric Hood), so the two entries are deliberately **not** merged.

---

## 2. Collective acts — member credits

When a duo, group, or collective plays, the database **also credits each member who has their own solo history**. This way a back-to-back set or group show counts toward every member's individual record, while the collective keeps its own entry too.

Groups whose members have **no** solo history in the data (e.g. **Âme**, **Tale Of Us**, **Pan-Pot**, **Modeselektor**) are left as a single act — there is nothing to link them to.

### Decomposed acts

| Collective | Members credited |
| --- | --- |
| **Karenn** | Blawan · Pariah |
| **Barker & Baumecker** | Barker · nd_baumecker |
| **MMM** | Errorsmith · Fiedel |
| **Apollonia** | Dan Ghenacia · Dyed Soundorom · Shonky |
| **Voices From The Lake** | Donato Dozzy · Neel |
| **Hercules & Love Affair** | Andy Butler |
| **Zenker Brothers** | Dario Zenker · Marco Zenker |
| **ItaloJohnson** | Adam Marshall · Patrick Bolton |
| **Scion** | DJ Pete (aka Substance) · Vainqueur |
| **Sandwell District** | Function · Regis · Silent Servant |
| **Harri & Domenic** | Harri · Domenic |
| **Overmono** | Truss (Tom Russell) · Tessela (Ed Russell) |
| **Lakker** | Eomac (Ian McDonnell) · Dara Smith |
| **Minilogue** | Sebastian Mullaert · Marcus Henriksson |
| **Doms & Deykers** | Steffi (Steffie Doms) · Martyn (Martijn Deykers) |
| **O/V/R** | James Ruskin · Regis |
| **A&S** | Dimi Angélis · Jeroen Search |
| **CW/A** | Clockwork · Avatism |
| **Highgrade** *(label showcase)* | Tom Clark · Todd Bodine · Daniel Dreier · Markus Homm |
| **Roog Unit** | Ø [Phase] · Luke Slater |

> **Label showcases & DJ teams** (e.g. Dekmantel Soundsystem, Innervisions, M>O>S, Dial allstars, Raw Series) are kept as their own billed acts — they genuinely played that slot. They're only broken into members when the lineup actually names the DJs, as with *Highgrade* above.

---

## 3. Open questions — help us confirm

These entries are flagged but **not yet changed**, because they need a real-world identity check. If you know the answer (ideally with a Discogs / Resident Advisor / official source), please [open an issue](https://github.com/M-Igashi/berghain-database/issues/new?template=data_correction.md).

### A. Are these the same artist?

Possible duplicates that have **not** been merged. Years and venues are shown to help compare.

| Entry A | Entry B | Our current guess |
| --- | --- | --- |
| **Stephan Hill** — 2010, Panorama Bar (1) | **Stefan Hill** — 2013, Panorama Bar (1) | Possible spelling variant — unconfirmed |
| **Thomas Svensson** — 2014, Panorama Bar (1) | **Tomas Svensson** — 2013, Panorama Bar (1) | Possible spelling variant — unconfirmed |
| **Talisman** — 2019, Berghain (1) | **Talismann** — 2014 & 2023, Berghain (2) | Could be the same act or two different ones |
| **OP/H** — 2022, Berghain (1) | **OPH** — Berghain (9) | Slash-vs-no-slash; possibly the same act |
| **Ghetto** — 2015–2016, PBar (2) | **Daniel Paul aka Ghetto** — 2013, PBar (1) | We currently assume these are **different** people — please confirm |
| **Roberto** — 2014–2019, Berghain (4) | **Roberto Bosco** — 2010, Berghain (2) | We currently assume these are **different** — please confirm |

### B. Who is behind these duo / project names?

Cryptic act names where we cannot yet identify the members. If at least one member has solo history in the data, we would credit them (see section 2).

| Act | Appearances | What we know |
| --- | --- | --- |
| **I/Y** | 2013–2016, Berghain (4) | = **Irakli & Yacoub** (community) — neither has solo history here, so kept as a single act |
| **Shawn O'Sullivan cluster** | 400PPM (3) · Civil Duty (1) · An-i (1) | How do **400PPM**, **Civil Duty** and **An-i** relate to each other and to Shawn O'Sullivan? |

> ✅ **Recently solved by the community** (thanks r/Berghain_Community! 🙏):
> - *Doms & Deykers* = **Steffi** (Steffie Doms) + **Martyn** (Martijn Deykers)
> - *O/V/R* = **James Ruskin** + **Regis** · *A&S* = **Dimi Angélis** + **Jeroen Search** · *CW/A* = **Clockwork** + **Avatism** — all now credited above
> - *I/Y* identified as **Irakli & Yacoub** (kept whole — neither has solo history here)
> - **Vera ≠ Vera Logdanidi** (German vs Ukrainian DJ) and **Philippa ≠ Philippa Pacho** confirmed as **different** people — kept separate
> - *He/aT* = **Chris Finke** (former name of the UK producer; also Bodyjack) — solo act, kept as-is
> - *∑ (Summe)* = solo project of producer **Frieder Blume** (now **Struktur**) — not a duo, kept as-is
> - *M>O>S* is a **label** (M>O>S Recordings), not an artist — kept as-is
> - *Roog Unit* = **Ø [Phase]** + **Luke Slater** (their Mote-Evolver project) — now credited above

---

*Maintained by the community. See [CONTRIBUTING.md](../CONTRIBUTING.md) for how to submit corrections with sources. This is an unofficial project, not affiliated with Berghain or Ostgut Ton.*
