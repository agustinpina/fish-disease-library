# Structural & Nomenclature Audit — Fish Disease Library

**Scope:** all 25 disease chapters, the 4 root-level notes, `README.md`,
`CONTRIBUTING.md`, `.github/pull_request_template.md`, `publish.css`, and the 5 open
`draft/*` branches. **Nothing in this PR modifies a chapter.** It adds this report, a
naming/structure standard (`CONVENTIONS.md`), and non-destructive tooling
(`tools/check-links.py`, `tools/rename-map.tsv`, `tools/apply-renames.sh`).

## 1. Executive summary

- **Four incompatible file-naming patterns coexist** (disease name / disease+acronym /
  disease+pathogen / bare pathogen), because no naming rule was ever written down.
- **`README.md`'s own framework uses "Symptoms," not "Clinical Signs."** Veterinary
  medicine doesn't use "symptoms" for animals — this is the single highest-leverage fix
  and it's a spec error, not just a content error.
- **Three files document one condition** (winter ulcer disease) and **contradict each
  other** on its economic impact ($750M vs NOK 100M).
- **A second, subtler duplication case — Piscine Reovirus vs. HSMI — is not a duplicate
  at all**, and a naive merge would delete content that exists nowhere else in the
  library (see §4.2). This is the case that shows why the naming fix alone isn't enough.
- The vault has **5 broken internal links, 3 published-site-breaking `obsidian://` URIs,
  no central index**, and widespread tag-vocabulary typos (`Salmonoids` in 20 of 25
  files).

## 2. Method

Every `.md` file in `Fish Disease Library/` was read or exhaustively grepped for: H1/H2/H3
structure, frontmatter keys, wikilink targets, italicisation and locale of scientific
names, and citation format. `git log`, `git branch -a`, `README.md`, `CONTRIBUTING.md`
and the PR template were read to establish what conventions are actually documented
today (answer: almost none).

**Every relationship claimed between two chapters below was established by reading both
chapters in full — not by matching titles or grepping a single "caused by" line.** This
matters concretely: an earlier pass at this audit flagged Piscine Reovirus and HSMI as a
simple duplicate to merge, based on a keyword match. Reading both files end to end showed
that conclusion was wrong (§4.2) — merging them as planned would have silently deleted
content on two diseases (jaundice syndrome, EIBS) that exist nowhere else in the vault.
Every count below is reproducible with the shell command shown next to it.

## 3. Evaluation

### 3.1 Nomenclature — four competing patterns

| Pattern | Count | Example |
|---|---|---|
| Disease name only | 11 | `Vibriosis`, `Yersiniosis`, `Pasteurellosis` |
| Disease + acronym | 8 | `Bacterial Kidney Disease (BKD)` |
| Disease + pathogen | 2 | `Furunculosis (Aeromonas spp.)` |
| **Pathogen as title** | 3 | `Moritella Viscosa`, `Piscine Reovirus (PRV)`, `Gyrodactylus salaris` |
| **Vernacular only** | 1 | `Winter Wounds` |

The three chapters currently in progress on unmerged branches
(`draft/columnaris-disease`, `draft/bacterial-gill-disease`, `draft/flavobacteriosis`)
all independently converged on the disease+pathogen pattern — new contributor instinct
already points where this audit recommends going.

Specific defects found:

- **`Moritella Viscosa`** — violates binomial nomenclature. The species epithet must be
  lowercase and italicised: *Moritella viscosa*. The error is in the filename, the
  frontmatter `title`, and every cross-link to this file in the vault; only the running
  body text gets it right.
- **`Furunculosis (Aeromonas spp.).md`** has `title: Furunculosis (Aeromonas
  salmonicida)` in its own frontmatter — filename and title already disagree.
- **`Heart and Muscle Inflammation (HSMI)`** — HSMI expands to Heart and **Skeletal**
  Muscle Inflammation. The filename and frontmatter `title` both drop "Skeletal," while
  the body, the `description` field, and the frontmatter tag
  `HeartandSkeletalMuscleInflammation` all include it. This is a factual error sitting in
  a title, not a style nit.
- `Tenacibaculosis (Tenacibaculum spp)` is missing the period on `spp.` — a small
  inconsistency, but it is the direct cause of two broken links elsewhere (§3.4).

### 3.2 "Symptoms" vs. "Clinical Signs"

```
grep -rlc '^## Symptoms' "Fish Disease Library"/*/*.md | wc -l   # → 21
grep -rlc '^## Clinical Signs' "Fish Disease Library"/*/*.md | wc -l  # → 4
```

`README.md`'s stated framework itself says "Symptoms." Fixing this means updating the
spec, not just the chapters — otherwise every future contributor copies the wrong term
right back in.

A related structural bug in the same spec: `README.md` nests Causes, Diagnosis,
Treatment, and Case Studies **under** Symptoms. Aetiology is not a subtype of clinical
presentation. This is visible in the files as inconsistent heading depth: 19 chapters
correctly promote these to `###` under a `##` Symptoms/Clinical Signs, while 6 chapters
(`Piscine Reovirus`, `Paranucleosporosis`, `Proliferative Gill Disease`, `Hemorrhagic
Diathesis`, `Nephrocalcinosis`, `Gyrodactylus salaris`) promote them all the way to `##`,
correctly ignoring a spec that doesn't match how the content actually relates.

### 3.3 Duplication and overlap

#### 3.3.1 Winter Wounds / Moritella Viscosa / Vintersår — genuine duplicate, should merge

Both chapters were read in full.

| | `Moritella Viscosa.md` | `Winter Wounds.md` |
|---|---|---|
| Length | 324 lines | 175 lines |
| Citations | 40, mostly APA with DOIs | 6, bare trade-press URLs |
| Countries covered | Canada, Faroe Isles, Norway, Scotland | Norway only |
| Last updated | 2026-04-11 | 2024-08-14 (stale) |

They **contradict each other** on economic impact: Winter Wounds cites "$750 million
annually" from an IntraFish headline; Moritella Viscosa cites "NOK 100 million" (2009,
from an EPO patent document) — different currencies, different years, no reconciliation.
Each chapter's footer lists the other as a *sibling disease*, presenting one condition as
two on the published site. `Vintersår.md`, the Norwegian name for the same condition, is
a **0-byte file at the vault root**, created as an accidental side effect of commit
`883c570` (an unrelated Moritella reference edit); nothing links to it.

**Recommendation:** merge into one chapter, `Winter Ulcer Disease (Moritella viscosa)`,
keeping Moritella Viscosa's body and citations (the deeper, current source), folding in
anything from Winter Wounds not already covered, reconciling the economic figure by
citing both sources with their context (Norway-specific vs. global), and listing
`Winter Wounds`, `Winter Ulcer`, and `Vintersår` as `aliases`. Delete the two source
files after the merge.

#### 3.3.2 Piscine Reovirus (PRV) vs. HSMI — not a duplicate; do not merge

Both chapters were read in full. **PRV is one agent that causes several distinct,
differently-named diseases in different hosts** — the two chapters are in a
one-to-many relationship, not a one-to-one duplicate:

| Genotype | Disease | Host | Documented in |
|---|---|---|---|
| PRV-1 | HSMI | Atlantic salmon | both files |
| PRV-1 | Jaundice syndrome | Chinook salmon | only `Piscine Reovirus (PRV).md:22,37-40` |
| PRV-2 | EIBS / jaundice-anaemia | Coho salmon (Japan) | only `Piscine Reovirus (PRV).md:104` |
| PRV-3 | named, disease not described | — | `Piscine Reovirus (PRV).md:56` |

Merging PRV into HSMI, as a title-matching pass would suggest, would **delete the
jaundice-syndrome and EIBS material — content that exists nowhere else in this
library.** The real defect is that the PRV chapter is a disease-chapter template wrapped
around a pathogen that doesn't fit it:

- `Piscine Reovirus (PRV).md:26-40` — its `## Common Symptoms` section is literally a
  list of **two different diseases** as bullet headers ("Heart and Skeletal Muscle
  Inflammation (HSMI):", "Jaundice Syndrome:"), not a list of clinical signs. It's a
  disease index dressed as a clinical section.
- The two chapters **contradict each other** on first detection date: HSMI.md line 18
  says "First detected in Norway in 1999"; PRV.md line 22 says "First identified in
  Norway in the 1990s."
- Both files carry separate, disagreeing Norway sections (PRV.md:130-146 vs.
  HSMI.md:105-126) — and PRV's Norway section is really *about HSMI outbreaks*, not
  about PRV as a pathogen.
- **Clinical error:** PRV.md:145 lists Norway's PRV treatments as "freshwater baths and
  hydrogen peroxide" — these are sea-lice/AGD treatments — which directly contradicts
  PRV.md:88, in the same file, stating "There are no specific antiviral treatments for
  PRV."
- **Off-topic references:** PRV.md's "Latest Research Findings" (lines 155-162) cites a
  2021 *pancreas disease* vaccine study and a 1976 general virology review — neither is
  about PRV. Meanwhile HSMI.md:133-144 carries three genuine, on-topic PRV papers
  (Wessel 2015, Kongtorp 2004, Di Cicco 2017). The chapter named after the pathogen has
  worse pathogen science than the chapter named after one of its diseases.
- PRV.md's citation `[7]` is listed but never cited in the body.
- Each chapter's footer lists the other as a sibling disease — an agent and one of the
  diseases it causes, presented as peers.

**Recommendation (see §5 and the "PRV split" table below):** adopt a `type: pathogen`
page kind, separate from `type: disease`, for exactly this situation — an agent with
more than one named disease. Split PRV into a pathogen profile plus the disease chapters
its content actually supports.

### 3.4 Broken and fragile links

```
python3 tools/check-links.py
```

| Broken target | Where | Cause |
|---|---|---|
| `[[Tenacibaculum (Tenacibaculosis)]]` | `Furunculosis (Aeromonas spp.).md:212`, `Salmonid Rickettsial Septicaemia (SRS).md:343` | file was renamed to `Tenacibaculosis (Tenacibaculum spp)`, links never updated |
| `[[Salmon Lice]]` | `Gyrodactylus salaris.md:164` | real file is named `Sea Lice` |
| `[[Proliferative Kidney Disease (PKD)]]` | `Gyrodactylus salaris.md:166` | no such file exists; likely meant `Proliferative Gill Disease` |
| `[[bacterial kidney disease (BKD)]]` | `Nephrocalcinosis.md:87` | case-only mismatch; Obsidian resolves it, but it's inconsistent |
| `https://fishdiseases.manolinaqua.com/.html` | `Vibriosis.md:341` | dead URL with an empty slug |

A past rename is what broke the first two links — direct evidence that hand-editing
links during a rename is unreliable, and the reason `tools/apply-renames.sh` exists
instead of doing renames by hand.

Also fragile: three hardcoded `obsidian://open?vault=Fish%20Disease%20Library&file=Winter%20Wounds`
URIs in `Moritella Viscosa.md` (lines 156, 172, 179) — these are dead links on the
published site *today*, not just a future rename risk. `Vibriosis.md` is the only
chapter that links to sibling diseases via absolute `fishdiseases.manolinaqua.com` URLs
instead of `[[wikilinks]]`, which is both fragile and the direct cause of its own dead
link above.

**Navigation:** there is no central index anywhere in the vault. `Welcome.md` has no
table of contents and explicitly defers to the sidebar and search ("explore our list of
top diseases featured... use the search function"). The de-facto navigation is a
hand-maintained `##### Other <Category> Diseases` footer repeated in every chapter, and
no bacterial-disease footer lists all 9 bacterial chapters. All six viral chapters head
theirs `##### Viral Diseases` instead of `##### Other Viral Diseases`, unlike every other
category.

### 3.5 Metadata hygiene

```
grep -rl 'Salmonoids' "Fish Disease Library"/*/*.md | wc -l   # → 20
```

- `Salmonoids` — a misspelling of "Salmonids" — is used as a tag in **20 of 25**
  chapters. It's not a one-off typo; it's the majority spelling.
- Tag typos: `VibioAnguillarum`, `VibioHarveyi` (missing the "r" in Vibrio),
  `HemmorrhagicSmoltSyndrome` (double m).
- Tag-category casing is inconsistent for the same concept: `BacterialDiseases` /
  `ParasiticDiseases` / `parasiticdisease` / `environmentalconditions`.
- `Paranucleosporosis.md`'s frontmatter tags **begin with `Parvicapsulosis`** — a
  copy-paste artifact naming the wrong disease.
- `Pasteurellosis.md` has no `tags:` key at all.
- Every chapter maintains **two separate, unsynchronised tag systems**: the YAML
  `tags:` block and an inline `**Tags:**` line near the footer. They drift apart in
  practice (e.g. `Pasteurellosis` has the inline line but no YAML tags).
- No chapter's frontmatter has a `pathogen`, `aliases`, or `category` key — all three are
  needed to execute the rename table below without losing searchability.

### 3.6 Style and locale

- **Locale is a genuine hybrid**, not a simple US-vs-UK split:
  `anemia` 83 / `anaemia` 17, `hemorrhag*` 87 / `haemorrhag*` 13, `behavior` 53 /
  `behaviour` 5 — all US-leaning — but `septicaemia` 56 / `septicemia` 19 is UK-leaning.
  The collision surfaces **in filenames**: `Infectious Salmon Anemia (ISA)` (US) sits
  next to `Salmonid Rickettsial Septicaemia (SRS)` (UK) in the same folder.
- Italic markers for scientific names split almost evenly: 192 uses of `*…*` vs. 190 of
  `_…_`; **10 files mix both styles internally**, including `Moritella Viscosa.md` and
  `Vibriosis.md`.
- Several chapters never italicise a pathogen name at all: `Winter Wounds` (0 of 10
  genus mentions), `Sea Lice` (0 of 9 — *Lepeophtheirus salmonis* is never italicised),
  `Cardiomyopathy Syndrome`, `Heart and Muscle Inflammation`, `Proliferative Gill
  Disease`.
- Within the viral category specifically, `Heart and Muscle Inflammation (HSMI).md:18`
  writes `(Salmo salar)`, `(Oncorhynchus mykiss)`, `(Oncorhynchus kisutch)` all
  un-italicised, while `Piscine Reovirus (PRV).md:22` italicises the same binomials —
  inconsistent even between two chapters about the same virus.
- Reference-block labels have three variants: `**Citations:**` (22 files),
  `**References:**` (`Pasteurellosis.md`, the only one with a plain numbered list
  instead of brackets), and `**Resources:**` (`Infectious Salmon Anemia (ISA).md`, which
  additionally runs a separate numbered APA list *and* the vault's only real Markdown
  footnote — three citation mechanisms in one file).
- Heading typos: "What is **Nephocalcinosis**?" (missing r), "What is ISA" / "What is
  Pancreas Disease (PD)" (both missing the closing "?"), "## **Treatments** and
  Prevention" (Gyrodactylus salaris, plural where every sibling uses singular).
- Footer metadata drift specific to HSMI: `Heart and Muscle Inflammation (HSMI).md:158`
  carries a stray `**Title:**` line duplicating the frontmatter title, and uses
  `**Date:**` where 22 other chapters use `**Last Modified:**`; its tag line is missing
  commas between several tags (`#Salmonoids #Salmon`, `#CohoSalmon #PiscineOrthoreovirus`).
- **A `### Call to Action` newsletter signup sits inside 20 of 25 clinical chapters.**
  It's marketing copy embedded in a veterinary reference document.
- `Saprolegniasis` is filed under *Parasitic Diseases*, but *Saprolegnia* is an
  **oomycete**, not a parasite in the taxonomic sense — the chapter's own frontmatter
  already tags it `Oomycete`.
- `CONTRIBUTING.md` line 1 is corrupted: `exam# Instructions for Contributing`.

## 4. Diagnosis (root causes)

1. **No naming rule was ever written down.** `README.md` and `CONTRIBUTING.md` specify
   citation style and a section framework, but nothing about what a chapter should be
   titled. Four patterns emerged independently because there was nothing to converge on.
2. **The stated content framework itself has two errors** — "Symptoms" instead of
   "Clinical Signs," and Causes/Diagnosis/Treatment nested under it — so contributors
   who followed the spec exactly produced the wrong structure, and contributors who
   deviated (correctly) produced an inconsistent one.
3. **No page-type distinction exists between a disease and a pathogen.** This is what
   actually broke PRV/HSMI: a template built for "one disease, one chapter" was applied
   to an agent that causes three.
4. **No link validation exists**, so renames silently break links (as already happened
   twice) and nobody notices until a reader clicks through.
5. **Two unsynchronised tag systems per file** guarantee metadata drift over time.
6. **Navigation is hand-maintained per file** instead of generated from a single source,
   so it is already out of date and will only get more so as chapters are renamed.

## 5. Recommended improvements, prioritised

### P0 — no debate, no rename required
- Delete the 0-byte `Vintersår.md`.
- Fix the 5 broken wikilinks and the dead `Vibriosis.md` URL listed in §3.4.
- Fix heading typos: "Nephocalcinosis," the two missing question marks, "Treatments and
  Prevention" → "Treatment and Prevention."
- Fix `Salmonoids` → `Salmonids` across all 20 affected files, plus the 3 other tag
  typos (`VibioAnguillarum`, `VibioHarveyi`, `HemmorrhagicSmoltSyndrome`).
- Give `Pasteurellosis.md` a `tags:` key; fix `Paranucleosporosis.md`'s wrong-disease
  tags.
- Fix `CONTRIBUTING.md` line 1.

### P1 — needs team agreement, still no rename
- Merge `draft/disease-chapter-template` (it already renames Symptoms → Clinical Signs
  and fixes several other heading names — build on it, don't compete with it).
- Adopt "Clinical Signs" and un-nest Causes/Diagnosis/Treatment/Case Studies from under
  it, per `CONVENTIONS.md` §3.
- Merge the winter-ulcer trio (§3.3.1) and resolve the economic-figure contradiction.
- Fix PRV's two content defects regardless of the split timeline: the sea-lice
  treatments at `Piscine Reovirus (PRV).md:145` and the off-topic pancreas-disease/1976
  citations at lines 155-162. These are wrong on their own terms, independent of any
  restructuring decision.
- Pick one locale, one italic marker, one reference-block label (`CONVENTIONS.md`
  proposes British English for veterinary terms, since "septicaemia" already dominates
  filenames — open to the team's preference).
- Remove `### Call to Action` from clinical chapters.

### P2 — structural, needs the rename tooling
- Adopt `CONVENTIONS.md`'s `type: disease | pathogen` rule and execute the PRV split
  (table below).
- Apply the rename table (below) via `tools/apply-renames.sh`.
- Add `aliases`, `pathogen`, `category` to every chapter's frontmatter.
- Replace the hand-maintained footer navigation with a real index in `Welcome.md`.
- Add a "Fungal & Oomycete Diseases" category for Saprolegniasis.
- Wire `tools/check-links.py` into CI so a broken link fails a PR instead of shipping.

## 6. Rename table

Every causative agent below was read out of the chapter's own "Causes" section — none
were assumed from memory. Full detail and every row (including unchanged rows and their
reasons) is in `tools/rename-map.tsv`.

| Current | Proposed |
|---|---|
| `Bacterial Kidney Disease (BKD)` | `Bacterial Kidney Disease (Renibacterium salmoninarum)` |
| `Furunculosis (Aeromonas spp.)` | `Furunculosis (Aeromonas salmonicida)` |
| `Moritella Viscosa` + `Winter Wounds` + `Vintersår` | **merge →** `Winter Ulcer Disease (Moritella viscosa)` |
| `Pasteurellosis` | `Pasteurellosis (Photobacterium damselae subsp. piscicida)` |
| `Salmonid Rickettsial Septicaemia (SRS)` | `Salmonid Rickettsial Septicaemia (Piscirickettsia salmonis)` |
| `Tenacibaculosis (Tenacibaculum spp)` | `Tenacibaculosis (Tenacibaculum spp.)` |
| `Vibriosis` | `Vibriosis (Vibrio and Aliivibrio spp.)` |
| `Yersiniosis` | `Yersiniosis (Yersinia ruckeri)` |
| `Cardiomyopathy Syndrome (CMS)` | `Cardiomyopathy Syndrome (Piscine myocarditis virus)` |
| `Heart and Muscle Inflammation (HSMI)` | `Heart and Skeletal Muscle Inflammation (Piscine orthoreovirus-1)` |
| `Infectious Pancreatic Necrosis (IPN)` | unchanged — exception 2 |
| `Infectious Salmon Anemia (ISA)` | unchanged, pending locale decision — exception 2 |
| `Pancreas Disease (PD)` | `Pancreas Disease (Salmonid alphavirus)` |
| `Piscine Reovirus (PRV)` | **split, see below** |
| `Amoebic Gill Disease (AGD)` | `Amoebic Gill Disease (Neoparamoeba perurans)` *(verify genus)* |
| `Gyrodactylus salaris` | `Gyrodactylosis (Gyrodactylus salaris)` |
| `Paranucleosporosis` | `Paranucleosporosis (Paranucleospora theridion)` *(verify epithet)* |
| `Parvicapsulosis` | `Parvicapsulosis (Parvicapsula pseudobranchicola)` |
| `Proliferative Gill Disease` | unchanged — exception 3 |
| `Saprolegniasis` | `Saprolegniasis (Saprolegnia spp.)`, moved to Fungal & Oomycete category |
| `Gas Bubble Disease`, `Hemorrhagic Diathesis`, `Nephrocalcinosis` | unchanged — exception 1 |

Two agents are flagged as **open items, not assertions**: current taxonomic usage should
be re-checked before renaming — *Neoparamoeba* vs. *Paramoeba perurans* for AGD, and the
species epithet used for *Paranucleospora* in Paranucleosporosis.

### The PRV split

Proposed, not executed. Resolves PRV into four pages so no content is lost:

| Page | `type` | Content source |
|---|---|---|
| `Heart and Skeletal Muscle Inflammation (Piscine orthoreovirus-1)` | disease | `HSMI.md` + the HSMI-specific material currently in `PRV.md`; reconcile the 1999-vs-1990s detection date |
| `Jaundice Syndrome (Piscine orthoreovirus-1)` | disease | rescued from `PRV.md:22,37-40` — **has no chapter today** |
| `Erythrocytic Inclusion Body Syndrome (Piscine orthoreovirus-2)` | disease | rescued from `PRV.md:104` — **has no chapter today** |
| `Piscine orthoreovirus` | pathogen | genotypes, host range, geographic distribution, plus a "Diseases caused" table linking the three chapters above |

This is the single largest editorial recommendation in this audit and the one most
likely to warrant discussion with the wider manolinaqua team before execution — it is
presented here as a proposal backed by the evidence in §3.3.2, not as a decision already
made.

## 7. Risks

- **Renaming changes published URLs** on fishdiseases.manolinaqua.com. Obsidian Publish
  has no built-in redirect mechanism, so any external link or search-engine result
  pointing at an old URL will 404 until re-indexed. Consider a communicated cutover
  rather than a silent rename, and batch renames into a single release rather than many
  small ones.
- **`draft/flavobacteriosis` is far behind `main`** (11 files changed, +706/−824) and
  would **delete `Gyrodactylus salaris.md` (−204 lines) and `attachments/favicon.png`**
  if merged as-is. This is unrelated to the rename work here but should be resolved
  (rebased or re-scoped) before any renames land, or that merge will silently undo them.
- The two taxonomy items flagged in §6 need a subject-matter check before the rename PR
  ships — this audit surfaces them as questions, not settled facts.

## 8. Appendix — per-chapter conformance snapshot

| Chapter | Naming pattern | Symptoms/Signs | Call to Action | Ref. label |
|---|---|---|---|---|
| Bacterial Kidney Disease (BKD) | disease+acronym | Symptoms | yes | Citations |
| Furunculosis (Aeromonas spp.) | disease+pathogen | Clinical Signs | no | Citations |
| Moritella Viscosa | **pathogen-as-title** | Symptoms | yes | Citations |
| Pasteurellosis | disease-only | Symptoms | yes | **References** |
| Salmonid Rickettsial Septicaemia (SRS) | disease+acronym | Clinical Signs | no | Citations |
| Tenacibaculosis (Tenacibaculum spp) | disease+pathogen | Symptoms | yes | Citations |
| Vibriosis | disease-only | Clinical Signs | no | Citations |
| Winter Wounds | **vernacular-only** | Symptoms | yes | Citations |
| Yersiniosis | disease-only | Symptoms | yes | Citations |
| Cardiomyopathy Syndrome (CMS) | disease+acronym | Symptoms | yes | Citations |
| Heart and Muscle Inflammation (HSMI) | disease+acronym (incomplete) | Symptoms | yes | Citations |
| Infectious Pancreatic Necrosis (IPN) | disease+acronym | Symptoms | yes | Citations |
| Infectious Salmon Anemia (ISA) | disease+acronym | Symptoms | yes | **Resources** |
| Pancreas Disease (PD) | disease+acronym | Symptoms | yes | Citations |
| Piscine Reovirus (PRV) | **pathogen-as-title** | Symptoms (disease index in disguise) | yes | Citations |
| Amoebic Gill Disease (AGD) | disease+acronym | Symptoms | yes | Citations |
| Gyrodactylus salaris | **pathogen-as-title (bare binomial)** | Symptoms | no | Citations |
| Paranucleosporosis | disease-only | Symptoms | yes | Citations |
| Parvicapsulosis | disease-only | Symptoms | yes | Citations |
| Proliferative Gill Disease | disease-only | Symptoms | yes | Citations |
| Saprolegniasis | disease-only (mis-categorised) | Clinical Signs | no | Citations |
| Sea Lice | disease-only | Symptoms | yes | Citations |
| Gas Bubble Disease | disease-only | Symptoms | yes | Citations |
| Hemorrhagic Diathesis | disease-only | Symptoms | yes | Citations |
| Nephrocalcinosis | disease-only | Symptoms | yes | Citations |
