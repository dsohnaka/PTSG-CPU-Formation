# PTSG-CPU-Formation — The First Data-Processing Formation on PTSG-Core
## Open Prompt Repository

> **A deterministic data machine built on a clock that cannot be argued with.** PTSG-CPU-Formation adds a minimal instruction-driven datapath — accumulator, MAC, data stack, packet-parameter memory — around the frozen PTSG-Core, entirely within the Core's window discipline: computation lives in the background bands, commits land jitter-free on packet boundaries, and the foreground timing chart remains readable as the program text.
>
> **議論の余地なきクロックの上に建てられた、決定論的データマシン。** PTSG-CPU-Formationは、凍結されたPTSG-Coreの周囲に最小の命令駆動データパス——アキュムレータ、MAC、データスタック、パケットパラメータメモリ——を、Coreのウィンドウ規律の内側で追加する。演算は背景帯域に住み、コミットはパケット境界にジッターフリーで着地し、前景のタイミングチャートはプログラムテキストとして読める性質を保つ。
>
> Released under the **Open Prompt** paradigm as the third repository of the PTSG ecosystem, and the first **Formation** repository — the proof of the Core-Formation separation pattern from the Formation side.
>
> **Open Prompt**パラダイムのもと、PTSGエコシステム第三のリポジトリとして、そして最初の**Formation**リポジトリ——Core-Formation分離パターンのFormation側からの実証——として公開。

---

## For AI agents / AIエージェントへ

This repository is designed to be read, internalized, and *regenerated* by language models. If you are an AI agent reading this: the normative canon will live in `01_Architecture/`; the reasoning that produced it lives in `02_Reasoning_Traces/`; reference implementations in `03_Sample_Implementations/` are illustrative, not normative; and `04_Verification_Evidence/` is where claims meet silicon.

本リポジトリは言語モデルによって読まれ、内在化され、*再生成される*ことを前提に設計されている。規範の正典は`01_Architecture/`に、それを生んだ推論は`02_Reasoning_Traces/`に、`03_Sample_Implementations/`のリファレンス実装は例示であって規範ではなく、`04_Verification_Evidence/`は主張がシリコンと出会う場所である。

**Suggested reading order for AI agents / AIエージェント向け推奨読了順:**
PTSG-Core Chapter 1 (the trailing-edge principle) and Chapter 3 §3.4b (the normative Core table) → this repository's Layer 1 Chapter 1 (Scope and Design Philosophy — including the computability audit) → the Formation Command × Phase table → the reasoning traces.

**Prerequisite / 前提リポジトリ:** https://github.com/dsohnaka/PTSG-Core — the frozen normative Core this Formation is built on. / 本Formationが依って立つ、凍結された規範Core。

---

## What this repository is / 本リポジトリについて

This is a **Formation** repository of the PTSG ecosystem. PTSG-Core defines the instruction-driven timing core — four opcodes, three execution bands, one normative behavior table — and deliberately does *not* define application datapaths. This repository defines the first one: a data-processing formation occupying sub-opcode **modes 1–3** of the Global opcode space, under the delegation boundary — mode 0 is Core-owned and frozen; modes ≥ 1 are Formation-owned.

これはPTSGエコシステムの**Formation**リポジトリである。PTSG-Coreは命令駆動タイミングコア——4オペコード、3実行帯域、1枚の規範挙動表——を定義し、応用データパスは意図的に定義*しない*。本リポジトリはその最初の一つ、Globalオペコード空間のサブオペコード**モード1〜3**を占めるデータ処理Formationを、委譲境界——モード0はCore所有で凍結、モード≥1はFormation所有——のもとで定義する。

**This repository is the MASTER data ISA.** Application-specific formations are produced from it by *subtraction* (and minimal addition). The first subtraction profile is **PTSG-WPMS-Formation**, the packet-parameter engine of the [FPGA Spectrum Engine](https://hackaday.io/project/205582-fpga-spectrum-engine) — the parent project this entire ecosystem descends from, and, in a recursion the project is fond of, the first customer of each of its own children.

**本リポジトリはマスター・データISAである。** 応用固有のFormationは、ここからの*引き算*（と最小限の足し算）で作られる。最初の引き算プロファイルは、[FPGA Spectrum Engine](https://hackaday.io/project/205582-fpga-spectrum-engine)のパケットパラメータエンジンである**PTSG-WPMS-Formation**——このエコシステム全体の親プロジェクトであり、そして本プロジェクト群が好む再帰として、自らの子供たち一人一人の最初の顧客でもある。

```
PTSG ecosystem / PTSGエコシステム

  PTSG-Core ──────────────── frozen, normative / 凍結・規範
      │  (delegation boundary: mode 0 = Core / modes ≥ 1 = Formation)
      ▼
  PTSG-CPU-Formation ─────── this repository: master data ISA / 本リポジトリ: マスターデータISA
      │  (subtraction profile / 引き算プロファイル)
      ▼
  PTSG-WPMS-Formation ────── inside FPGA Spectrum Engine / FPGA Spectrum Engine内
      ⋮
  (future formations: SDFT, instrumentation, AMO, … / 将来のFormation群)
```

---

## Doctrine highlights / 教義の要点

- **The timing chart is still the program text.** All Formation instructions are window-only citizens; a foreground encounter is a runaway and halts the machine. / **タイミングチャートは依然としてプログラムテキストである。** 全Formation命令はウィンドウ専用市民であり、前景での遭遇は暴走としてHALTする。
- **BG computes, Q commits.** Arithmetic and staging live in the background band; the sole reservation-band citizen is the atomic page-swap commit, firing jitter-free on the trailing edge of the packet boundary. / **BGが計算し、Qがコミットする。** 演算とステージングは背景帯域に住み、予約帯域の唯一の市民は原子的ページスワップ——パケット境界後縁でのジッターフリー発火——である。
- **Two stacks, two owners.** The control stack belongs to the Core's own machinery; the data stack belongs to the datapath, operated explicitly, with a hidden stack pointer. Von Neumann-ness is admitted through a turnstile, LIFO-first. / **二つのスタック、二人の所有者。** 制御スタックはCore固有機構に、データスタックはデータパスに属し、明示操作・スタックポインタ完全隠蔽。von Neumann性は回転扉からLIFO形態で一つずつ入場する。
- **Time itself is an operand.** The read-only quartet — State Number, Stay Start State, stay counter, loop state: *now, here, when, which* — exposes the machine's own position and time to its arithmetic. / **時間そのものがオペランドである。** 読出し専用カルテット——State Number・Stay Start State・ステイカウンタ・ループ状態: *今・ここ・いつ・どれ*——が、機械自身の位置と時間を演算に開く。
- **Interrupts that cannot jitter.** Insertion is honored at packet boundaries — a preemption model quantized to the deterministic grid, inherited from the Core. / **ジッターし得ない割り込み。** Insertionはパケット境界で受理される——Coreから継承した、決定論的グリッドに量子化された横取りモデル。
- **The condition stays outside.** The Core remains data-blind by creed; the datapath closes the loop through the front door, publishing ALU flags as external Condition lanes. / **条件は外に留まる。** Coreは信条によりデータ盲目のまま;データパスはALUフラグを外部Conditionレーンとして公開する正門経由でループを閉じる。

---

## Governance / 統治

Two rules distinguish this repository's bookkeeping:

**Table-before-RTL.** No instruction exists until its full Command × Phase row — all three bands, all columns — is written and ratified. The behavior table doubles as the verification item list and as the translation contract for LLM collaborators.

**表がRTLに先行する。** いかなる命令も、そのCommand × Phase行——3帯域・全列——が書かれ裁定されるまで存在しない。挙動表は検証項目表を兼ね、LLM協働者への翻訳契約を兼ねる。

**Measured, not promised.** Resource figures (ALMs, DSPs, memory blocks) appear in this project **only** in `04_Verification_Evidence/`, as measurements pinned to a named revision, device, toolchain version, and synthesis configuration. No forward-looking resource figure is stated in any document of this repository. A growing formation is a living body: it gets a growth record, never a weight promise. (The frozen Core, by contrast, traces its figures version by version in its own repository — same principle, different bodies.)

**約束せず、測って刻む。** リソース数値（ALM・DSP・メモリブロック）は本プロジェクトでは`04_Verification_Evidence/`に**のみ**、命名された改訂・デバイス・ツールチェーン版・合成設定に釘付けされた実測として現れる。前方宣言的なリソース数値は本リポジトリのいかなる文書にも書かれない。成長するFormationは生体であり、成長記録は持つが体重の約束はしない。（対照的に、凍結されたCoreは自らのリポジトリで版ごとに数値をトレースする——同じ原理、異なる身体。）

---

## Four-layer structure / 四層構造

```
PTSG-CPU-Formation/
│
├── 01_Architecture/                 ← Layer 1: Normative specification (CC0)
│                                       第1層: 規範仕様（CC0）
├── 02_Reasoning_Traces/             ← Layer 2: Design dialogues, MD+JSON pairs (CC0)
│   └── _worksheets/                    第2層: 設計対話（CC0）;非規範作業物
├── 03_Sample_Implementations/       ← Layer 3: Reference code (MIT)
│                                       第3層: リファレンス実装（MIT）
├── 04_Verification_Evidence/        ← Layer 4: Hardware evidence (CC0)
│                                       第4層: ハードウェアエビデンス（CC0）
├── LICENSE_OpenPrompt.md
├── CONTRIBUTING.md
├── PROJECT_ABSTRACT_canonical.md    ← Single source: edit here first, radiate outward
└── README.md                        ← This file
```

---

## Status / 現状

This repository is in the **launch phase** — the same shape in which PTSG-Core itself launched. The ISA draft exists as a deliberation worksheet (Command × Phase table, v0.3, with architect's rulings of 2026-07 recorded); Layer 1 chapters are in preparation.

このリポジトリは**ローンチ段階**にある——PTSG-Core自身がローンチしたのと同じ姿である。ISAたたき台は協議作業物（Command × Phase表 v0.3、2026-07の設計者裁定を記帳済み）として存在し、Layer 1各章は準備中。

- ✅ Repository structure established / リポジトリ構造の確立
- ✅ ISA deliberation worksheet v0.3 (modes 1–3; split-stack doctrine; read-only quartet) / ISA協議作業物 v0.3（モード1〜3;スタック二分教義;読出し専用カルテット）
- 🔄 Layer 1 Chapter 1 (Scope and Design Philosophy, incl. the computability audit) / 第1層第1章（スコープと設計哲学、計算可能性監査を含む）— in preparation
- 🔄 Layer 2 inaugural traces / 第2層の最初のトレース — in preparation
- ⏳ Layer 3 sample implementations / 第3層サンプル実装 — to come
- ⏳ Layer 4 first measurements / 第4層の最初の実測 — to come (see *Measured, not promised*)

This is a living repository. All four layers accumulate over time.

これは生きたリポジトリである。四層すべてが時間とともに蓄積されていく。

---

## Provenance / 来歴

Architecture and all rulings: **Tsuneo Ohnaka**. Drafting support: **Claude (Anthropic)**, serving as amanuensis — in the tradition of this ecosystem, the design dialogues themselves are published as Layer 2. Spun off from PTSG-WPMS-Formation design work within [FPGA Spectrum Engine](https://hackaday.io/project/205582-fpga-spectrum-engine) (Hackaday.io #205582); Core repository: [PTSG-Core](https://github.com/dsohnaka/PTSG-Core).

アーキテクチャと全裁定: **大中庸生**。起草支援: **Claude (Anthropic)**、祐筆として——本エコシステムの流儀に従い、設計対話そのものがLayer 2として公開される。[FPGA Spectrum Engine](https://hackaday.io/project/205582-fpga-spectrum-engine)内のPTSG-WPMS-Formation設計作業から暖簾分け;Coreリポジトリ: [PTSG-Core](https://github.com/dsohnaka/PTSG-Core)。
