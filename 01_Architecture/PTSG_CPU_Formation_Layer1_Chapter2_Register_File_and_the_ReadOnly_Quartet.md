# PTSG-CPU-Formation — Layer 1 Specification
# Chapter 2: Register File and the Read-Only Quartet
# PTSG-CPU-Formation — 第1層仕様書
# 第2章: レジスタファイルと読出し専用カルテット

> **License: CC0 1.0 Universal (Public Domain)**
>
> **Status: DRAFT (2026-07-12).** This chapter promotes the *Registers & Interface* sheet of the deliberation worksheet (v0.3) to normative prose. Rulings F-F9 and F-F13 are Fixed; other decisions cited are Proposed pending ratification. Where this chapter and the worksheet disagree, this chapter wins once ratified; until then, discrepancies are defects to be reported.
> **状態: ドラフト（2026-07-12）。** 本章は協議作業物（v0.3）の*Registers & Interface*シートを規範文へ昇格させる。F-F9・F-F13はFixed;引用するその他の決定は裁定待ちのProposed。本章と作業物が食い違う場合、裁定後は本章が勝つ;それまでは、食い違いは報告されるべき欠陥である。

---

## 2.1 Purpose and Scope / 目的とスコープ

This chapter defines every architecturally visible register and read-only source of the Formation datapath: its access rules, its read/write semantics, and the ID spaces by which instructions name sources and destinations. It also defines what is deliberately **not** visible (the stack pointer) and what is deliberately **not** decided (widths and encodings marked Implementation Arena).

本章はFormationデータパスのアーキテクチャ的に可視な全レジスタと読出し専用ソースを定義する: そのアクセス規則、読み書き意味論、そして命令がソースと宛先を名指すID空間。同時に、意図的に**不可視**なもの（スタックポインタ）と、意図的に**未決定**なもの（Implementation Arenaと標された幅とエンコーディング）も定義する。

The guiding decision is **F-F6 (register minimalism):** the file is shaped by the dataflow of packet-parameter computation — an accumulator machine around a Horner-step MAC — and by nothing else. Registers are added only against proven need, and every addition passes through the table-before-RTL gate (F-F8).

指針となる決定は**F-F6（レジスタ極小主義）**である: ファイルはパケットパラメータ演算のデータフロー——HornerステップMACを核とするアキュムレータ機械——によって形作られ、それ以外の何によっても形作られない。レジスタは証明された必要に対してのみ追加され、全ての追加は表先行の門（F-F8）を通る。

## 2.2 The Register File at a Glance / レジスタファイル一覧

| Register / Source | Access | Width | Role |
|---|---|---|---|
| **Accm** | read / write | DSP-native (Arena) | accumulator; result of all mode-1 operations; sole PSH source and POP destination |
| **Temp** | read / write (via SWP, STA destination) | = Accm | Horner multiplicand holder; swap partner |
| **ADRS** | read / write (SAD; post-increment; STA destination per F-F13) | PPM address width (Arena) | packet-parameter-memory pointer |
| **SP** | **hidden** | DSTK depth (Arena) | data-stack pointer; managed solely by PSH/POP |
| **SN** | read-only (source ID 5) | State Number width | current State Number — *now* |
| **SSS** | read-only (source ID 6) | State Number width | Stay Start State, Formation-latched — *here* |
| **K** | read-only (source ID 3) | stay-counter width | the stay counter — *when* |
| **I** | read-only (source ID 4) | loop-counter width | loop iteration state — *which* |
| **StayVal** | write-only (WSV) | Stay operand width | data-driven stay duration |
| **LoopVal** | write-only (WLV) | Loop operand width | data-driven loop count |
| **JumpVal** | write-only (WJV) | State Number width | computed dispatch target |
| **R0–R3** | reserved | — | unimplemented until proven necessary |

The table is a map; the sections below are the law. / 表は地図であり、以下の節が法である。

## 2.3 Accm and Temp / AccmとTemp

**Accm** is the accumulator: the implicit destination of every mode-1 arithmetic instruction, the implicit left operand of ADD/SUB/MUL, the sole source of PSH and the sole destination of POP. Its width is Implementation Arena under the **Free Precision Floor** inherited from the ecosystem's house doctrine: where the DSP block provides width at no additional cost, the implementation takes it; the chosen width and Q-format are documented per implementation, never assumed by programs.

**Accm**はアキュムレータである: 全モード1算術命令の暗黙宛先、ADD/SUB/MULの暗黙左オペランド、PSH唯一のソースかつPOP唯一の宛先。その幅はエコシステムの家法**Free Precision Floor**の下でImplementation Arenaである: DSPブロックが追加コストなしに幅を提供するなら、実装はそれを取る;選ばれた幅とQフォーマットは実装ごとに文書化され、プログラムは決してそれを仮定しない。

**Temp** holds the Horner multiplicand: MAC computes Accm ← Accm × Temp + source, so that with Temp = x, a sequence of MACs over successive coefficients *is* Horner evaluation — the Maclaurin idiom on which the master's mathematical repertoire (and, in prospect, the dirt-on-coefficients idiom) is built. Temp has the same width as Accm. It is written by SWP (exchange with Accm) or as an STA destination; it is read as source ID 1 or implicitly by MAC. A dedicated load-Temp instruction (LDT) is the first candidate addition should instruction pressure prove it, and not before (F-F6).

**Temp**はHornerの被乗数を保持する: MACはAccm ← Accm × Temp + sourceを計算し、Temp = xのとき係数列にわたるMACの連なりは*そのまま*Horner評価である——マスターの数学的レパートリー（そして展望としては係数汚しイディオム）が築かれるマクローリンイディオム。TempはAccmと同幅。SWP（Accmとの交換）またはSTA宛先として書かれ、ソースID 1としてまたはMACにより暗黙に読まれる。専用ロード命令（LDT）は、命令圧が必要を証明した場合の最初の追加候補であり、それ以前ではない（F-F6）。

## 2.4 ADRS — the Pointer / ADRS——ポインタ

**ADRS** addresses the packet-parameter memory (PPM; Chapter 4). It is written absolutely by SAD, post-incremented optionally by LDM/STM (flag in the operand field), and — by ruling **F-F13 (Fixed 2026-07-11)** — it is a legal STA destination in this master: **pointer indirection is admitted.** Computed addressing, table walks, and memory-to-memory idioms (LDM post-inc → STA ADRS chains) are thereby first-class. Subtraction profiles may re-forbid the STA-to-ADRS destination; PTSG-WPMS-Formation is expected to, for purity.

**ADRS**はパケットパラメータメモリ（PPM;第4章）を指す。SADで絶対書込され、LDM/STMで任意にポストインクリメントされ（オペランド域のフラグ）、そして裁定**F-F13（2026-07-11 Fixed）**により、本マスターでは合法なSTA宛先である: **ポインタ間接参照は入場済み。** 計算されたアドレシング、テーブル巡回、メモリ間イディオム（LDMポストインクリメント→STA ADRS連鎖）は、これにより一級市民である。引き算プロファイルはSTA→ADRS宛先を再禁止してよい;PTSG-WPMS-Formationは純粋性のため禁止する見込みである。

An out-of-range ADRS access takes the Error HALT path — the machine never silently wraps an address (the C3-F24 posture, extended).

範囲外のADRSアクセスはError HALT経路を取る——機械はアドレスを黙ってラップしない（C3-F24の姿勢の拡張）。

## 2.5 SP — the Hidden Pointer / SP——隠されたポインタ

**SP** exists and is named here once, in order to be hidden: it is managed solely by PSH and POP, is not readable, not writable, not addressable, and appears in no ID space. Its reset value is the empty-stack condition; overflow and underflow take the Error HALT path (**F-F11**). Direct SP access, stack-relative addressing, and frame pointers are the subject of Tie **F-T6**, whose *deferral* was itself ratified (2026-07-11): the turnstile stays closed until a proven need, and any future opening will arrive as its own decision ID with its own Command × Phase rows.

**SP**は存在し、隠されるためにここで一度だけ名指される: PSHとPOPのみが管理し、読めず、書けず、アドレスできず、いかなるID空間にも現れない。リセット値は空スタック状態;オーバーフローとアンダーフローはError HALT経路を取る（**F-F11**）。SP直接アクセス・スタック相対アドレシング・フレームポインタはTie **F-T6**の主題であり、その*保留*自体が裁定済みである（2026-07-11）: 回転扉は証明された必要まで閉じたままであり、将来の開扉は固有の決定IDと固有のCommand × Phase行を伴って到来する。

The data stack that SP serves — its ownership, its separation from the Core's control stack, and its physical realization freedoms — is specified in Chapter 4 under doctrine **F-F9**.

SPが仕えるデータスタック——その所有権、Coreの制御スタックからの分離、物理的実現の自由——は教義**F-F9**の下、第4章で規定される。

## 2.6 The Read-Only Quartet / 読出し専用カルテット

**F-F10.** Four read-only sources expose the machine's own position and time to its arithmetic. Their normative read semantics: **each source returns its value as of the reading instruction's execution clock.** Whether an implementation realizes them as direct multiplexer inputs or as transparent capture registers is Arena, provided the semantics hold; if source-encoder depth threatens timing closure, distributing captures into the reserved registers R0–R3 is the architect's sanctioned fallback — and their most likely first proven need.

**F-F10。** 四つの読出し専用ソースが、機械自身の位置と時間を演算に開く。その規範的読出し意味論: **各ソースは、読む命令の実行クロック時点の値を返す。** 実装が直接マルチプレクサ入力として実現するか透過キャプチャレジスタとして実現するかは、意味論が保たれる限りArenaである;ソースエンコーダの深さがタイミングクロージャを脅かす場合、予約レジスタR0–R3へのキャプチャ分散が設計者公認のフォールバックであり——それが彼らの最も有望な「最初に証明された必要」である。

### 2.6a SN — *now* / SN——今

The current **State Number**. The name is kept deliberately: the foreground is a finite-state machine, and the state label is a richer truth than "address." Because background execution advances SN every clock, reading SN is self-referential by design — the value obtained is the position of the reading instruction itself. Canonical idiom: **SN + offset → WJV** yields relative computed dispatch, and with it relocatable sub-sequences.

現在の**State Number**。名は意図的に保持される: 前景は有限状態機械であり、状態ラベルは「番地」より豊かな真実である。背景実行はSNを毎クロック進めるため、SNの読出しは設計により自己言及的である——得られる値は読む命令自身の位置である。正典イディオム: **SN＋オフセット→WJV**は相対計算ディスパッチを、そしてそれと共に再配置可能サブシーケンスをもたらす。

### 2.6b SSS — *here* / SSS——ここ

The **Stay Start State**: the label of the current window. Normatively, the datapath's SSS source is a **Formation-latched copy taken at window start** — not a live tap. The Core's own Stay Start State register is short-lived by Core law: valid within its Stay cycle, discharged into the Base register by a queued Base Set, overwritten by the next Stay Set; and it is Core-invisible but explicitly copyable by the Formation. The window-start latch converts that short-lived register into a label that is stable for the whole window. Canonical idiom: **SSS-indexed parameter lookup** — one background routine serving many different Stays, parameterized by which window invoked it (window-vectored preparation).

**Stay Start State**: 現在ウィンドウのラベル。規範的に、データパスのSSSソースは**ウィンドウ開始時に取られるFormationラッチの写し**であり、生タップではない。Core自身のStay Start StateレジスタはCore法により短寿命である: 当該Stayサイクル内で有効、QueされたBase SetによりBaseレジスタへ引き継がれて放電し、次のStay Setで上書きされる;かつCore不可視だがFormationによる持出は明示的に許される。ウィンドウ開始ラッチは、その短寿命レジスタを、ウィンドウ全体にわたって安定なラベルへ変換する。正典イディオム: **SSS索引のパラメータ参照**——一つの裏ルーチンが、どのウィンドウに呼ばれたかでパラメータ化されて、多数の異なるStayに仕える（window-vectored準備）。

### 2.6c K — *when* / K——いつ

The **stay counter**, exposed as data: On-Tick, armed and zeroed by Stay Set, jitter-free by Core law. K is packet time — the k of any difference recurrence the attached hardware consumes.

**ステイカウンタ**のデータとしての露出: On-Tickで、Stay Setにより起動・零化され、Core法によりジッターフリー。Kはパケット時刻——接続ハードウェアが消費するあらゆる差分漸化式のkである。

### 2.6d I — *which* / I——どれ

The **loop iteration state**: the sweep index inside a background window, advancing at full system clock. **K and I run on different clocks of different worlds** — K on the prescaled time axis, I on the full-clock space axis — and every consumer of either must name its axis (the F-F5 discipline, absorbed here). Conflating them is the quartet's one classic trap, and it is named in the specification precisely so that it cannot be stepped into innocently.

**ループ反復状態**: 背景ウィンドウ内の掃引インデックスであり、フルシステムクロックで進む。**KとIは異なる世界の異なるクロックを走る**——Kはプリスケールされた時間軸を、Iはフルクロックの空間軸を——いずれの消費者も自らの軸を名指ししなければならない（F-F5の規律、本節に吸収）。両者の混同はカルテット唯一の古典的な罠であり、無邪気に踏めないようにするためにこそ仕様に名指しされる。

## 2.7 The Value Registers / 値レジスタ群

**StayVal, LoopVal, JumpVal** are the write side of the Core interface: mode-3 instructions (WSV/WLV/WJV) write them from Accm, and a Stay, Loop, or Jump/Branch whose operand selects **register source** consumes them. The latch rule is **F-F4**: the consuming instruction samples its value register **once, on the clock the instruction is first reached, frozen thereafter**; a same-cycle write-read conflict resolves **read-before-write** — the house style of the Core's own same-cycle hand-offs. The prescaler's sovereignty is untouched: only durations, counts, and targets become data; the time base never does.

**StayVal・LoopVal・JumpVal**はCoreインターフェースの書込側である: モード3命令（WSV/WLV/WJV）がAccmからこれらへ書き、オペランドが**レジスタソース**を選ぶStay・Loop・Jump/Branchがこれらを消費する。ラッチ規則は**F-F4**: 消費命令は値レジスタを**その命令に最初に到達したクロックで一度だけサンプルし、以後凍結**する;同一サイクルの書込-読出衝突は**read-before-write**で解決する——Core自身の同一サイクル・ハンドオフの家風である。プリスケーラの主権は不可侵のまま: データになるのは長さ・回数・行先のみであり、時間基盤は決してならない。

The **register-source operand bit** on Stay/Loop/Jump/Branch is the single Core-side amendment this Formation requests, and it is subject to the Core's own table-before-RTL process. The legality of a register-source Jump/Branch *in the foreground* is Tie **F-T5** — the second of the two named CPU-attractor gates, still closed.

Stay/Loop/Jump/Branchへの**レジスタソース・オペランドビット**は本Formationが要請する唯一のCore側修正であり、Core自身の表先行プロセスに服する。レジスタソースJump/Branchの*前景における*合法性はTie **F-T5**——名指しされた二つのCPUアトラクター門の第二であり、なお閉じている。

## 2.8 ALU Flags → Condition Lanes / ALUフラグ→Conditionレーン

The datapath maintains **no flags register**. The sign and zero properties of Accm are exported continuously as external **Condition lanes**; selection among lanes is Formation logic (and may itself be driven by the Core's timing signals — the machine's output selecting its own sensory input). The Core consults them exactly as it consults any Condition, by its own unchanged creed of data-blindness. This is the front door through which the computational loop closes; no back-door coupling between datapath state and Core sequencing exists or may be added.

データパスは**フラグレジスタを持たない**。Accmの符号とゼロの性質は外部**Conditionレーン**として連続的に公開される;レーン間の選択はFormationロジックである（そしてそれ自体がCoreのタイミング信号で駆動されてよい——機械の出力が自らの感覚入力を選ぶ）。Coreはそれを、データ盲目という自らの不変の信条により、他のあらゆるConditionと全く同様に参照する。これが計算ループの閉じる正門であり、データパス状態とCoreシーケンシングの裏口結合は存在せず、追加されてもならない。

## 2.9 Source and Destination ID Spaces / ソースおよび宛先ID空間

The **assignments** below are ISA-normative; the **encodings** of these IDs into the operand field D16–D31 are Implementation Arena.

以下の**割当**はISA規範である;これらIDのオペランド域D16–D31への**エンコーディング**はImplementation Arenaである。

**Source IDs** (LDA and the source operand of ADD/SUB/MUL/MAC):

| ID | Source |
|---|---|
| 0 | Immediate |
| 1 | Temp |
| 2 | PPM_active[ADRS] |
| 3 | K |
| 4 | I |
| 5 | SN |
| 6 | SSS |
| 7 | reserved |
| 8–B | R0–R3 (reserved) |
| C–F | reserved |

**Destination IDs** (STA):

| ID | Destination |
|---|---|
| 0 | Temp |
| 1 | PPM_shadow[ADRS] |
| 2–5 | R0–R3 (reserved) |
| 6 | ADRS (legal in this master per F-F13; profiles may re-forbid) |
| 7–F | reserved |

Reserved IDs are exactly that: an instruction presenting a reserved ID takes the Error HALT path. Silence is never a semantics.

予約IDは文字通り予約である: 予約IDを提示した命令はError HALT経路を取る。沈黙は決して意味論ではない。

## 2.10 R0–R3 — the Reserved Registers / R0–R3——予約レジスタ

Four general-purpose registers are **named and unimplemented**. They exist in the ID spaces so that their eventual activation changes no encoding, and they remain unimplemented under F-F6 until a need is proven. Two candidate needs are already on record: the capture-distribution fallback of §2.6, and whatever the WPMS profile's parameter choreography demonstrates. Activation, when it comes, is a table row and a decision ID first — never an RTL patch.

四本の汎用レジスタは**名指しされ、未実装**である。ID空間には存在する——将来の起動がエンコーディングを一切変えないために——そしてF-F6の下、必要が証明されるまで未実装に留まる。候補となる必要は既に二件記録されている: §2.6のキャプチャ分散フォールバック、そしてWPMSプロファイルのパラメータ振付が示すであろうもの。起動は、来るならば、まず表の行と決定IDである——決してRTLパッチではない。

## 2.11 What is NOT in this Chapter / 本章に含まれないもの

The instruction set itself and its band legality (Chapter 3); the memories the registers address — PPM pages, routing tables, the data stack — and the commit protocol (Chapter 4); the signal-level Core interface (Chapter 5); all numeric widths not fixed above (Implementation Arena, documented per implementation).

命令セットそのものと帯域合法性（第3章）;レジスタが指すメモリ群——PPMページ・ルーティングテーブル・データスタック——とコミットプロトコル（第4章）;信号レベルのCoreインターフェース（第5章）;上で確定しなかった全ての数値幅（Implementation Arena、実装ごとに文書化）。

## 2.12 Summary of Chapter 2 Decisions / 第2章決定事項のまとめ

| ID | Decision | Status |
|---|---|---|
| F-F4 | Value-register latch rule: sample once on arrival, frozen; read-before-write | Proposed |
| F-F5 | Two time axes (K vs I), absorbed into §2.6d | Proposed |
| F-F6 | Register minimalism; R0–R3 named-and-unimplemented | Proposed |
| F-F10 | The quartet; normative read semantics; realization Arena; capture fallback | Proposed |
| F-F11 | Hidden SP; stack overflow/underflow → Error HALT | Proposed |
| F-F13 | ADRS as STA destination (pointer indirection) in this master | **Fixed (2026-07-11)** |
| — | Source/destination ID assignments (§2.9) ISA-normative; encodings Arena | Proposed |
| — | Reserved IDs → Error HALT ("silence is never a semantics") | Proposed |
| F-T5 | Register-source Jump/Branch in FG | Tie, open |
| F-T6 | SP visibility / stack-relative addressing | Tie, deferral ratified |

## End of Chapter 2 / 第2章の末尾

The registers are the nouns of this machine. Chapter 3 supplies the verbs — the normative Command × Phase rows — and with them the grammar of band legality under which every sentence of a Formation program is either well-formed or halts.

レジスタはこの機械の名詞である。第3章が動詞——規範Command × Phase行——を、そしてそれと共に、Formationプログラムの全ての文が整形式であるかHALTするかを定める帯域合法性の文法を供給する。
