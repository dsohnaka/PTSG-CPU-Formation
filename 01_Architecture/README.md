# Layer 1 — Normative Architecture / 規範アーキテクチャ

The normative specification of PTSG-CPU-Formation. **CC0.** Nothing outside this directory is normative; the deliberation worksheet in `02_Reasoning_Traces/_worksheets/` is its ancestor, not its canon. / PTSG-CPU-Formationの規範仕様。**CC0。** 本ディレクトリの外に規範はない;`02_Reasoning_Traces/_worksheets/`の協議作業物は先祖であって正典ではない。

## Planned chapters / 章計画

| Chapter | Working title | Status |
|---|---|---|
| 1 | Scope and Design Philosophy — the delegation boundary, the computability audit (what a PTSG machine can never be, and why that is the product), Measured-not-promised / スコープと設計哲学——委譲境界、計算可能性監査（PTSG機械が決してなり得ないもの、そしてそれこそが製品である理由）、実測主義 | in preparation / 準備中 |
| 2 | Register File and the Read-Only Quartet — Accm, Temp, ADRS, hidden SP; SN/SSS/K/I read semantics; source/destination ID spaces / レジスタファイルと読出し専用カルテット | planned / 予定 |
| 3 | The Formation Command × Phase Table — modes 1–3, normative rows, decision register (F-F*/F-T*) / Formation規範表と決定台帳 | planned / 予定 |
| 4 | Memory Architecture — packet-parameter memory pages, atomic commit, routing tables, the data stack / メモリアーキテクチャ | planned / 予定 |
| 5 | Core Interface Contract — issue port, taps (K/I/SSS latch), value registers, Condition lanes / Coreインターフェース契約 | planned / 予定 |

Chapter numbering mirrors the Core repository's rhythm where it helps the reader; divergence is allowed where the Formation's subject demands it. / 章番号は読者の助けになる範囲でCoreリポジトリの律動を映すが、Formation固有の主題が要求する場合の逸脱は許される。
