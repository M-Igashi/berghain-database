# Aliases & Collective Acts

How this database handles DJs who record under multiple names, and duos / collectives whose members also play solo.

A single human can appear on a lineup under several names, and a back-to-back duo is really two soloists sharing a slot. To keep rankings, search, and per-artist histories accurate, the dataset normalises both cases. This page documents every consolidation made so far (three passes: **May 2026**, **June 2026** and **July 2026**).

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
| **R.M.K aka Roberto** | Rob Kirkaldy (London); also releases as Fossil Archive |
| **Daniel Paul Cortez aka Ghetto** | Minneapolis DJ (ex-*DJ Ghetto*, DVS1's Future Classic resident); merged the *Ghetto*, *Daniel Paul aka Ghetto* and *Daniel Paul Cortez* entries |

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
| **Civil Duty** | Shawn O'Sullivan (aka 400PPM) · Beau Wanzer |

> **Label showcases & DJ teams** (e.g. Dekmantel Soundsystem, Innervisions, M>O>S, Dial allstars, Raw Series) are kept as their own billed acts — they genuinely played that slot. They're only broken into members when the lineup actually names the DJs, as with *Highgrade* above.

---

## 3. Identity checks — resolved

Identity questions raised in [issue #1](https://github.com/M-Igashi/berghain-database/issues/1). **All items are now resolved** — kept here as a record. Spotted a mistake? Please [open an issue](https://github.com/M-Igashi/berghain-database/issues/new?template=data_correction.md).

### A. Are these the same artist?

> ✅ **All resolved (July 2026)** — thanks to community input on [issue #1](https://github.com/M-Igashi/berghain-database/issues/1):
> - **Stefan Hill → Stephan Hill** — spelling variant, merged (RA / official bill "Stephan Hill").
> - **Thomas Svensson → Tomas Svensson** — spelling variant, merged (RA canonical "Tomas Svensson"; Swedish, Berlin-based).
> - **OP/H → OPH** — slash-vs-no-slash variant of the same act, merged.
> - **Ghetto = Daniel Paul aka Ghetto = Daniel Paul Cortez** — all one person: **Daniel Paul Cortez** (Minneapolis; ex-*DJ Ghetto*; DVS1's Future Classic resident & supporting DJ). Merged as *Daniel Paul Cortez aka Ghetto* (see section 1). The 2017 Panorama Bar billing is Berghain's own wording: *"Aus Minneapolis … Daniel Paul Cortez, der bis vor kurzem noch als DJ Ghetto aufgelegt hat."* This is **not** the Berlin *Daniel Paul* (Cab Drivers / Slope / Cabinet Records), who does not appear in the data (an RA event tag links there by mistake).
> - **Talisman ≠ Talismann** — confirmed **different** acts, kept separate.

### B. Who is behind these duo / project names?

> ✅ **All resolved (July 2026)** — the **Shawn O'Sullivan cluster** (issue #1 E9):
> - **400PPM** = **Shawn O'Sullivan** (solo alias) — annotated *400PPM aka Shawn O'Sullivan*.
> - **Civil Duty** = **Shawn O'Sullivan** (aka 400PPM) + **Beau Wanzer** (The Corner) — decomposed (see section 2); Berghain's own listing states *"Civil Duty is the alias for Shawn O'Sullivan and Beau Wanzer."*
> - **An-i** = **Lee Douglas** (Cititrax; ex-NY, Berlin-based) — a **different** person, **not** part of the O'Sullivan cluster. Kept as its own act (O'Sullivan's RA aliases are Led Er Est / Further Reductions / Civil Duty / 400PPM / Vapauteen / Weld — no An-i).

> ✅ **Recently solved by the community** (thanks r/Berghain_Community! 🙏):
> - *Doms & Deykers* = **Steffi** (Steffie Doms) + **Martyn** (Martijn Deykers)
> - *O/V/R* = **James Ruskin** + **Regis** · *A&S* = **Dimi Angélis** + **Jeroen Search** · *CW/A* = **Clockwork** + **Avatism** — all now credited above
> - *I/Y* identified as **Irakli & Yacoub** (kept whole — neither has solo history here)
> - **Vera ≠ Vera Logdanidi** (German vs Ukrainian DJ) and **Philippa ≠ Philippa Pacho** confirmed as **different** people — kept separate
> - *He/aT* = **Chris Finke** (former name of the UK producer; also Bodyjack) — solo act, kept as-is
> - *∑ (Summe)* = solo project of producer **Frieder Blume** (now **Struktur**) — not a duo, kept as-is
> - *M>O>S* is a **label** (M>O>S Recordings), not an artist — kept as-is
> - *Roog Unit* = **Ø [Phase]** + **Luke Slater** (their Mote-Evolver project) — now credited above
> - **Roberto** = **R.M.K** = Fossil Archive (Rob Kirkaldy) → merged as *R.M.K aka Roberto*; confirmed **different** from **Roberto Bosco** (kept separate)

---

*Maintained by the community. See [CONTRIBUTING.md](../CONTRIBUTING.md) for how to submit corrections with sources. This is an unofficial project, not affiliated with Berghain or Ostgut Ton.*
