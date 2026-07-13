# PTSG-CPU-Formation — Layer 1 Specification
# Chapter 3: The Formation Command × Phase Table
# PTSG-CPU-Formation — 第1層仕様書
# 第3章: Formation Command × Phase表

> **License: CC0 1.0 Universal (Public Domain)**
>
> **Status: DRAFT (2026-07-13).** This chapter promotes the *Command × Phase* sheet of the deliberation worksheet (v0.3) to normative prose. Rulings F-F9, F-F12, F-F13 are Fixed; other decisions are Proposed pending ratification. Where this chapter and the worksheet disagree, this chapter wins once ratified; until then, discrepancies are defects to be reported.
> **状態: ドラフト（2026-07-13）。** 本章は協議作業物（v0.3）の*Command × Phase*シートを規範文へ昇格させる。F-F9・F-F12・F-F13はFixed;その他はProposed。本章と作業物の食い違いは、裁定後は本章が勝ち、それまでは報告されるべき欠陥である。

---

## 3.1 Purpose — One Table, Three Contracts / 目的——一枚の表、三つの契約

This chapter is the instruction set. Under **F-F8 (table-before-RTL)**, no Formation instruction exists until its full Command × Phase row — all three execution bands, all columns — is written and ratified; conversely, everything written and ratified here *is* the instruction set, exhaustively. The table serves three masters at once: it is the **specification** (the only normative statement of instruction behavior), the **verification item list** (every row is a conformance test waiting to be run; Layer 4's matrix is keyed to it), and the **translation contract** for LLM collaborators (a behavior description is compilable to an instruction list exactly insofar as this table defines the vocabulary).

本章が命令セットである。**F-F8（表がRTLに先行する）**の下、いかなるFormation命令も、その完全なCommand × Phase行——全3実行帯域・全列——が書かれ裁定されるまで存在しない;逆に、ここに書かれ裁定された全てが、命令セットの*全部*である。表は同時に三人の主に仕える: **仕様**（命令挙動の唯一の規範的言明）であり、**検証項目表**（全ての行は実行を待つ適合試験である;Layer 4のマトリクスはこれに紐づく）であり、LLM協働者への**翻訳契約**（振る舞い記述が命令リストへ翻訳可能なのは、本表が語彙を定義する限りにおいてである）。

## 3.2 The Common Law / 共通法

All Formation instructions share an invariant legal frame. Stating it once here compresses the table without losing a cell: **the full cell-by-cell expansion (as rendered in the v0.3 worksheet) is regenerable from this common law plus the per-instruction rows of §§3.3–3.5**, and a conforming implementation may be audited against either form.

全Formation命令は不変の法的枠組みを共有する。ここで一度述べることで、表は一セルも失わず圧縮される: **セル単位の完全展開（v0.3作業物の表現形）は、本共通法と§§3.3–3.5の命令別行から再生成可能**であり、適合実装はいずれの形式に対しても監査されうる。

**CL-1 (citizenship).** Every Formation instruction is a window-only citizen. A **foreground encounter is a runaway**: the State Number holds and the machine takes the Error HALT path (F-F2, extending C3-F23). In the FG band, for every instruction of every mode: stay counter untouched, timing signals Held, State Number Held, legality HALT.

**CL-1（市民権）。** 全Formation命令はウィンドウ専用市民である。**前景での遭遇は暴走**である: State Numberは保持され、機械はError HALT経路を取る（F-F2、C3-F23の拡張）。FG帯域では、全モードの全命令について: ステイカウンタ不変、タイミング信号Held、State Number Held、合法性HALT。

**CL-2 (background execution).** In the BG band a legal instruction executes at full system clock: the prescaler tick is ignored, the stay counter continues counting On-Tick without reset, the timing signals are Held (hence D16–D31 is unambiguously the operand), and **the State Number increments on the next clock, simultaneously performing the operation**. All determinations land on the trailing edge; this ISA introduces no leading-edge exceptions (Core Ch1 §1.4a).

**CL-2（背景実行）。** BG帯域で合法な命令はフルシステムクロックで実行される: プリスケーラティックは無視され、ステイカウンタはリセットなしにOn-Tickで数え続け、タイミング信号はHeldであり（ゆえにD16–D31は一義にオペランドである）、**State Numberは次クロックで増加し、同時に操作を遂行する**。全ての確定は後縁に着地する;本ISAは先端例外を導入しない（Core第1章§1.4a）。

**CL-3 (the reservation band).** The Q band is the commit band. Exactly one Formation instruction is legal there — **CMT** — and no Formation arithmetic or staging is (F-F3): a queued data operation adds nothing that earlier BG execution cannot, and the single reservation register (C3-F26) must remain free for the boundary commit. A Formation instruction other than CMT encountered in the Q band takes the Error HALT path.

**CL-3（予約帯域）。** Q帯域はコミット帯である。そこで合法なFormation命令はちょうど一つ——**CMT**——であり、Formationの算術・ステージングは合法でない（F-F3）: QueされたデータオペレーションはBG実行が先にできること以上の何も加えず、単一予約レジスタ（C3-F26）は境界コミットのために空けておかねばならない。Q帯域でCMT以外のFormation命令に遭遇した場合、Error HALT経路を取る。

**CL-4 (errors).** No Formation error is ever a silent wraparound. Every error cause enumerated in §3.6 connects to the Core's Error HALT machinery (C3-F24).

**CL-4（エラー）。** いかなるFormationエラーも黙ったラップアラウンドには決してならない。§3.6に列挙される全エラー原因はCoreのError HALT機構（C3-F24）に接続する。

**CL-5 (identity).** An instruction is identified by {mode, sub-op}; mnemonics are documentation, not architecture. ID assignments in operands follow Chapter 2 §2.9; encodings are Implementation Arena.

**CL-5（同一性）。** 命令は{モード, サブオペ}で同定される;ニーモニックは文書であってアーキテクチャではない。オペランド内のID割当は第2章§2.9に従う;エンコーディングはImplementation Arenaである。

Given CL-1..CL-5, the normative rows below record only: the operation performed in the band(s) where the instruction is legal, and any deviation from the common law. **Absence of deviation is itself normative.**

CL-1〜CL-5を所与として、以下の規範行が記録するのは: 命令が合法な帯域で遂行される操作、および共通法からの逸脱のみである。**逸脱の不在それ自体が規範である。**

## 3.3 Mode 1 — Datapath ALU / MAC / モード1——データパスALU/MAC

Compute-band citizens: legal in BG only. / 演算帯市民: BGでのみ合法。

| Sub-op | Mnemonic | BG operation (normative) |
|---|---|---|
| 1·0 | LDA | Accm ← source. Full source family per §2.9 (immediate, Temp, PPM_active[ADRS], K, I, SN, SSS; R0–R3 reserved). |
| 1·1 | STA | destination ← Accm. Destination family per §2.9; PPM destination is always the shadow page; ADRS destination legal in this master (F-F13). |
| 1·2 | ADD | Accm ← Accm + source. Overflow behavior: saturating vs wrapping is Arena, but the overflow event itself must be routed to the error machinery or to a Condition lane — never lost. |
| 1·3 | SUB | Accm ← Accm − source. Same overflow discipline as ADD. Sign/zero of the result feed the Condition lanes (§2.8). |
| 1·4 | MUL | Accm ← Accm × source. One DSP multiplication; result alignment via SFT; Q-format is Arena under the Free Precision Floor. |
| 1·5 | MAC | Accm ← Accm × Temp + source. The Horner step: with Temp = x, MAC over successive coefficients is polynomial evaluation. The master's mathematical workhorse. |
| 1·6 | SWP | Accm ↔ Temp. The canonical route for loading Temp. |
| 1·7 | SFT | Accm ← Accm arithmetically shifted by the signed amount in the operand. Q-format alignment after MUL/MAC. |

**Notes.** Reading SN yields the reading instruction's own position (self-referential by design; the relative-dispatch idiom pairs it with WJV). Reading SSS yields the Formation-latched window label (§2.6b). Reserved source or destination IDs take the Error HALT path (§2.9).

**注。** SNの読出しは読む命令自身の位置を与える（設計により自己言及的;相対ディスパッチのイディオムはこれをWJVと対にする）。SSSの読出しはFormationラッチされたウィンドウラベルを与える（§2.6b）。予約ソース/宛先IDはError HALT経路を取る（§2.9）。

## 3.4 Mode 2 — Memory, Data Stack & Routing / モード2——メモリ・データスタック・ルーティング

| Sub-op | Mnemonic | Band | Operation (normative) |
|---|---|---|---|
| 2·0 | SAD | BG | ADRS ← operand (absolute set of the PPM pointer). |
| 2·1 | LDM | BG | Accm ← PPM_active[ADRS]; optional post-increment of ADRS (operand flag). Reads come from the **active** page only. |
| 2·2 | STM | BG | PPM_shadow[ADRS] ← Accm; optional post-increment. Writes land on the **shadow** page only; the active page is read-only to the datapath (F-F7 as master default; see note). |
| 2·3 | RTW | BG | RT_shadow[operand] ← Accm. Routing-table staging; effective only at CMT. |
| 2·4 | CMT | **Q only** | Registered during the scan; at Stay-timeup, on the trailing edge, **atomically swaps the PPM shadow/active pages and the routing tables**. The sole Formation citizen of the reservation band. Shares the single reservation register (C3-F26): last-write-wins among CMT registrations; conflict with a queued State-Number reservation → Error HALT. **CMT in BG is Error HALT**: a mid-window page swap tears the packet the attached hardware is consuming. |
| 2·5 | PSH | BG | DSTK[SP] ← Accm; SP advances (hidden, §2.5). Data stack only — entirely distinct from the Core's control stack (F-F9). Overflow → Error HALT (F-F11). |
| 2·6 | POP | BG | Accm ← DSTK, SP retreats. Underflow → Error HALT (F-F11). LIFO read-back is the turnstile's first admission of von Neumann-ness (Ch.1 §1.7). |

**Note on the shadow discipline.** In this master, STM's shadow-only rule and CMT's page swap together make mid-packet parameter tearing structurally impossible — glitch-freedom is architecture, not programmer discipline. A future pure-scratch memory class (direct read-your-own-writes) is F-T6 territory and would arrive, if ever, as new rows — never as a reinterpretation of STM.

**shadow規律についての注。** 本マスターでは、STMのshadow専用規則とCMTのページスワップが相まって、パケット途中のパラメータ裂けを構造的に不可能にする——グリッチフリーはアーキテクチャであり、プログラマの規律ではない。将来の純スクラッチメモリクラス（直接のread-your-own-writes）はF-T6の領分であり、来るとしても新しい行として来る——STMの再解釈としては決して来ない。

## 3.5 Mode 3 — Core-Parameter Write / モード3——Coreパラメータ書込

All BG-only. Queued (Q-band) mode-3 writes are Error HALT under Tie **F-T2** (a boundary-timed write has ambiguous window ownership; forbidden until a use case forces definition).

全てBG専用。Que（Q帯域）でのモード3書込はTie **F-T2**の下Error HALTである（境界時刻の書込はウィンドウ所有権が曖昧;使用例が定義を強いるまで禁止）。

| Sub-op | Mnemonic | BG operation (normative) |
|---|---|---|
| 3·0 | WSV | StayVal ← Accm. Consumed by the next Stay selecting register source; latch rule F-F4 (§2.7). |
| 3·1 | WLV | LoopVal ← Accm. Data-driven iteration counts. Latch rule F-F4. |
| 3·2 | WJV | JumpVal ← Accm. Computed dispatch; with the SN source, relative computed dispatch. FG legality of the consuming register-source Jump/Branch is Tie F-T5. |

## 3.6 The Error Taxonomy / エラー分類

Every entry connects to the Core's Error HALT machinery (C3-F24); none may be downgraded to a warning or a silent default by an implementation.

全項目はCoreのError HALT機構（C3-F24）に接続する;いかなる実装もこれを警告や黙ったデフォルトに格下げしてはならない。

| # | Cause | Law |
|---|---|---|
| E1 | Formation instruction encountered in FG | CL-1 (F-F2) |
| E2 | Formation instruction other than CMT in Q | CL-3 (F-F3) |
| E3 | CMT encountered in BG | §3.4 (mid-window swap) |
| E4 | Reserved source/destination/sub-op ID presented | §2.9 ("silence is never a semantics") |
| E5 | ADRS out of range | §2.4 |
| E6 | Data-stack overflow / underflow | F-F11 |
| E7 | CMT registration conflicting with a queued State-Number reservation | §3.4, C3-F26 |
| E8 | Arithmetic overflow, where the implementation routes it to error rather than a Condition lane | §3.3 ADD/SUB note (Arena choice, but never lost) |

Unassigned sub-ops within modes 1–3, and all of modes 4–F, are reserved: encountering them is E4. / モード1〜3内の未割当サブオペ、およびモード4〜Fの全ては予約である: その遭遇はE4である。

## 3.7 The Decision Register / 決定台帳

The canonical register of this Formation's architectural decisions. Chapter texts cite these IDs; this table is where their status lives.

本Formationのアーキテクチャ決定の正典台帳。各章本文はこれらIDを引用し、その地位は本表に住む。

| ID | Decision | Status |
|---|---|---|
| F-F1 | Delegation boundary: mode 0 Core-frozen; modes ≥ 1 Formation-owned, issued undecoded | Proposed |
| F-F2 | FG-Formation exclusion (window-only citizenship) | Proposed |
| F-F3 | BG computes, Q commits; CMT sole Q citizen | Proposed |
| F-F4 | Value-register latch rule: sample once on arrival, frozen; read-before-write | Proposed |
| F-F5 | Two time axes (K vs I); absorbed into Ch.2 §2.6d | Proposed |
| F-F6 | Register minimalism; R0–R3 named-and-unimplemented | Proposed |
| F-F7 | Shadow-only writes to PPM/RT; commit-at-boundary (master default) | Proposed |
| F-F8 | Table-before-RTL governance | Proposed |
| F-F9 | Split-stack doctrine; hidden SP; unified stack declined | **Fixed 2026-07-11** |
| F-F10 | Read-only quartet SN/SSS/K/I; normative read semantics; SSS as Formation latch | Proposed |
| F-F11 | Stack overflow/underflow → Error HALT; SP reset = empty stack | Proposed |
| F-F12 | Master/profile succession; WPMS-Formation the first subtraction profile | **Fixed 2026-07-11** |
| F-F13 | ADRS as STA destination (pointer indirection) legal in this master | **Fixed 2026-07-11** |
| — | *Measured, not promised* (+ quotation clause for other repositories' published, version-pinned figures) | Ratified 2026-07-11 / 2026-07-12 |
| F-T1 | Sub-minimum (1-wave) packet mechanism | Tie — deferred |
| F-T2 | Queued (Q-band) mode-3 writes | Tie — open (forbidden meanwhile) |
| F-T3 | Q-band arithmetic beyond the commit family | Tie — open (excluded by F-F3 meanwhile) |
| F-T5 | Register-source Jump/Branch legality in FG | Tie — open |
| F-T6 | SP visibility / stack-relative addressing / random-access scratch | Tie — deferral ratified 2026-07-11 |

(F-T4 was resolved into F-F13 and is retired as an ID; it remains in the worksheet's history. / F-T4はF-F13へ解決され、IDとしては退役した;作業物の履歴には残る。)

## 3.8 What is NOT in this Chapter / 本章に含まれないもの

Register semantics and ID spaces (Chapter 2); the memories themselves — PPM geometry, routing-table format, data-stack realization — and the commit protocol in signal detail (Chapter 4); the issue port and taps (Chapter 5); all mnemonics-to-bits encodings (Implementation Arena); all application programs (profiles and Layer 3).

レジスタ意味論とID空間（第2章）;メモリそのもの——PPM形状・ルーティングテーブル形式・データスタック実現——と信号詳細のコミットプロトコル（第4章）;発行ポートとタップ（第5章）;全てのニーモニック→ビット割当（Implementation Arena）;全ての応用プログラム（プロファイルとLayer 3）。

## 3.9 Summary / まとめ

Eighteen instructions across three modes; one common law of citizenship; one green cell in the reservation band; eight named error causes; zero leading-edge exceptions; and a decision register in which every liberty and every prohibition has a name, a date, or an open Tie. The verbs of this machine are few because each one had to survive the table first.

3モード18命令;市民権の共通法一つ;予約帯域の緑セル一つ;名指しされた8つのエラー原因;先端例外ゼロ;そして、あらゆる自由とあらゆる禁止が名前と日付か未決Tieを持つ決定台帳。この機械の動詞が少ないのは、どの一つも先に表を生き延びねばならなかったからである。

## End of Chapter 3 / 第3章の末尾

Chapter 4 descends into the memories these verbs act upon: the packet-parameter pages and their atomic swap, the routing tables, and the data stack whose pointer no program will ever see.

第4章は、これらの動詞が作用するメモリ群へ降りる: パケットパラメータページとその原子的スワップ、ルーティングテーブル、そしてそのポインタをいかなるプログラムも決して見ることのないデータスタックへ。
