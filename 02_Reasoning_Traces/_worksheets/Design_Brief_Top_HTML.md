# Design Brief — PTSG-CPU-Formation Top HTML
*Prepared by the amanuensis for hand-off to Claude Design · 2026-07-12 · non-normative worksheet (CC0)*

## 1. What is being commissioned

A single static HTML page serving as the visual front door of the **PTSG-CPU-Formation** GitHub repository (GitHub Pages compatible; a sibling page is being commissioned for PTSG-Core, so a shared family look between the two is desirable, with this page clearly the "Formation" member of the family).

## 2. Single source of truth

All copy derives from `PROJECT_ABSTRACT_canonical.md`. Do not paraphrase the fixed-vocabulary pillars; quote them. The one-liner is the hero line:

> **EN:** A deterministic data machine built on a clock that cannot be argued with.
> **JA:** 議論の余地なきクロックの上に建てられた、決定論的データマシン。

The page is bilingual EN/JA throughout, in the repository's established style (EN first, JA following; no language toggle needed — both visible).

## 3. Hard constraints (non-negotiable)

- **No resource figures anywhere.** No ALM/LE/DSP/BRAM counts, no "tiny/few-hundred" quantifiers, no percentages of a device. This is the project's *Measured, not promised* governance rule. Qualitative smallness may be expressed only through the Core's published vocabulary by reference ("built on the compact PTSG-Core"), never as a number or a forward-looking claim about this Formation.
- **No performance promises** (no MHz targets, no latency figures). Determinism is the claim, not speed.
- Static, self-contained HTML (inline CSS; no external JS frameworks, no web fonts required for correctness — graceful fallback if used).
- Dark/light must both remain legible if a scheme toggle is attempted; a single well-chosen scheme is acceptable.
- All links absolute, from the link inventory below only. Never invent URLs.

## 4. Content skeleton (suggested order)

1. **Hero** — project name, one-liner (EN+JA), one-sentence positioning: "The first data-processing formation on the frozen PTSG-Core, released as an Open Prompt repository."
2. **The ecosystem diagram** — PTSG-Core (frozen, normative) → PTSG-CPU-Formation (master data ISA, THIS PAGE) → PTSG-WPMS-Formation (first subtraction profile, inside FPGA Spectrum Engine) → future formations. This lineage is the single most important visual on the page.
3. **The delegation boundary** — a visual of the opcode space: mode 0 (Core-owned, rendered as sealed/frozen) vs modes 1–F (Formation-owned, rendered as open shelves; modes 1–3 occupied, others reserved).
4. **Doctrine strip** — the six pillars from the canonical abstract, each as a short card. If band colors are used: background band = the compute band, reservation band = the commit band; a red accent is reserved exclusively for Error HALT semantics (matching the behavior-table convention: red rows = HALT). Do not use red decoratively.
5. **The read-only quartet** — SN / SSS / K / I with their epithets *now / here / when / which*. A compact four-cell motif; this is the page's signature opportunity.
6. **Four layers** — the Open Prompt structure with licenses (CC0 / CC0 / MIT / CC0), linking into the directories.
7. **Status + provenance** — launch phase; architecture and rulings by Tsuneo Ohnaka; drafting support by Claude (Anthropic) as amanuensis; design dialogues published as Layer 2.

## 5. Tone

Technical-poetic, in the house style already visible in the READMEs: precise nouns, short declaratives, the occasional aphorism ("Two stacks, two owners", "Measured, not promised"). No marketing superlatives; the page should read like a specification that learned to stand up straight.

## 6. Visual motifs available (choose, don't use all)

- The three execution bands (FG / BG / Q) as horizontal strata — FG kept visually "clean" (bare stay durations: the timing chart as program text), computation living visibly in the lower strata.
- The turnstile (von Neumann-ness admitted one shape at a time) — a good footer-note motif, not a hero.
- The trailing edge — commits landing exactly on a boundary line.
- Timing-chart aesthetics generally (grid, edges, labels) are on-brand; oscilloscope-green nostalgia is acceptable if restrained.

## 7. Link inventory (complete; use only these)

- This repository: https://github.com/dsohnaka/PTSG-CPU-Formation  *(confirmed 2026-07-12)*
- PTSG-Core: https://github.com/dsohnaka/PTSG-Core
- FPGA Spectrum Engine (parent project): https://hackaday.io/project/205582-fpga-spectrum-engine
- PTSG-Core Hackaday project: https://hackaday.io/project/205720
- PTSG-CPU-Formation Hackaday project: *(dedicated project ruled 2026-07-12; URL to be added on creation — leave a visually complete but link-free mention until then)*

## 8. Out of scope

No download buttons, no metrics badges, no CI shields (nothing that implies numbers under the governance rule), no cookie/analytics scripts, no logos of third parties.

---
*Canon note: this brief is a Layer-2 worksheet. If it conflicts with `PROJECT_ABSTRACT_canonical.md`, the abstract wins.*
