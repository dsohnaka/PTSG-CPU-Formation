# PTSG-CPU-Formation — The Five Impossibilities: The Pre-Launch Council
# PTSG-CPU-Formation — 五つの不可能: 設立前会議

## Trace Metadata / 軌跡メタデータ

| Field | Value |
|---|---|
| **Date / 日付** | 2026-07-10 〜 2026-07-12 (repository committed 2026-07-12 / リポジトリコミット 2026-07-12) |
| **Participants / 参加者** | Tsuneo Ohnaka (大中恒夫, FPGA Architect; sole design authority; all rulings); Claude (Anthropic — amanuensis / 祐筆; ISA drafting, computability audit, governance proposals, this archive) |
| **Topic / トピック** | The session in which PTSG-CPU-Formation was conceived, audited, and founded. Spans: the Formation ISA draft (modes 1–3, v0.1→v0.3); the computability audit ("what can this machine never be?") and its repository-informed corrections; the spin-off ruling (WPMS-Formation → CPU-Formation master + subtraction profiles); the split-stack doctrine; the read-only quartet SN/SSS/K/I; the *Measured, not promised* governance rule; the industrial north-star vision; repository founding. / PTSG-CPU-Formationが構想され、監査され、設立されたセッション。ISAたたき台v0.1→v0.3、計算可能性監査とその訂正、暖簾分け裁定、スタック二分教義、読出し専用カルテット、実測主義、産業ビジョン、リポジトリ設立に及ぶ。 |
| **Status / 状態** | **FOUNDED** — repository committed with skeleton + ISA worksheet v0.3 (rulings F-F9, F-F12, F-F13 Fixed; F-T6 deferral ratified). Layer 1 Chapter 1 drafted alongside this trace; Chapters 2–5 pending. / **設立済** — 骨格＋v0.3作業物コミット済。第1章ドラフト併走、第2〜5章は残件。 |
| **Original language / 原言語** | Japanese (with English technical terminology) / 日本語（英語技術用語を交える） |
| **License / ライセンス** | CC0 1.0 Universal (Public Domain) |
| **Lineage / 系譜** | This trace rhymes deliberately with the Core's own birth trace from WPMS: the three-generation recursion — WPMS keeps birthing generic children and becomes each child's first customer — is legible from the Layer 2 inventories themselves. / 本軌跡はCore自身のWPMSからの誕生トレースと意図的に韻を踏む: 三世代再帰が目録から読める。 |
| **Primary artifacts / 一次成果物** | `_worksheets/PTSG_CPU_Formation_Command_Phase_Table_v0.3.xlsx` (4 sheets); repository skeleton; `_worksheets/Design_Brief_Top_HTML.md`; Layer 1 Chapter 1 draft. |

---

## Reading Notes / 読解上の注

This is a founding trace, and its intellectual center is a **mistake** — deliberately preserved. The amanuensis, asked "what does this ISA lack to be a general-purpose CPU — not efficiency, but *impossibility*?", audited the machine from its behavior table alone and produced five impossibilities. Two of them were then **falsified by the Core repository itself**: the machinery the audit declared absent (return-address capture; interrupts) already existed, in richer form than the audit imagined. The correction did not weaken the audit — it sharpened it into the founding insight: of the five impossibilities, only one is Core law; two are Formation *doctrine* (choosable per profile); and the remaining gaps were already solved by the Core. A machine that close to CPU-hood, held back only by chooseable doctrine, deserved its own repository. **The audit failed correctly, and the failure was the product.**

これは設立トレースであり、その知的中心は——意図的に保存された——**誤り**である。祐筆は「このISAが汎用CPUであるために欠くもの——効率ではなく*不可能*——は何か」と問われ、挙動表のみから機械を監査し、五つの不可能を挙げた。うち二つはその後、**Coreリポジトリ自身によって反証された**: 監査が不在と断じた機構（戻り番地捕獲・割り込み）は、監査の想像より豊かな形で既に存在していた。訂正は監査を弱めず、設立の洞察へと研ぎ澄ました: 五つの不可能のうちCore法はただ一つ、二つはプロファイルごとに選べるFormation*教義*、残る欠落はCoreが解決済みだった。教義の選択だけでCPUに届くほど近い機械は、自らのリポジトリに値する。**監査は正しく失敗し、その失敗こそが製品だった。**

**Notable conceptual progressions / 特筆すべき概念的進展:**

1. **The delegation boundary (F-F1).** Mode 0 = Core-owned and frozen; modes ≥ 1 = Formation-owned, issued without decoding. The Core-Formation separation pattern applied recursively to the ISA itself. / 委譲境界——分離パターンのISAへの再帰適用。

2. **BG computes, Q commits (F-F3).** Double-buffered, boundary-synchronous parameter commit falls out of the Core's existing band semantics with zero new timing machinery. / 既存帯域意味論からゼロ機構でダブルバッファが出る。

3. **The five impossibilities and their correction.** Data-blindness; no return capture; no read-your-own-writes; external program ownership; no interrupts — audited, then corrected against Chapters 2–3. / 五つの不可能とその訂正。

4. **The split-stack doctrine (F-F9).** Control stack to the Core, data stack to the datapath; SP hidden; unified stack declined. / スタック二分教義。

5. **The read-only quartet (F-F10).** SN/SSS/K/I — *now, here, when, which*; SSS as a Formation-latched copy of the Core's short-lived register. / 読出し専用カルテット。

6. **Von Neumann-ness at the turnstile.** Admitted LIFO-first (PSH/POP); pointer indirection admitted by ruling (F-F13); random access deferred (F-T6). / 回転扉のvon Neumann性。

7. ***Measured, not promised.*** Born from the Core's own "235 press conference": a frozen body gets a weight log; a living body gets no advance numbers. / 実測主義——「235謝罪会見」から生まれた統治規則。

---

## Notable Decision Points / 重要な決定ポイント

### 1. Where does arithmetic live? / 演算はどこに住むか

**Point:** The architect's initial sketch placed Accm/Temp/ADRS and mode 1–F arithmetic sub-opcodes conceptually near the Core. The amanuensis proposed the alternative that became F-F1: the Core *issues* modes ≥ 1 without decoding them; the datapath is a separate Formation-owned execution unit. **Alternatives:** (a) extend the Core's RTL with a datapath; (b) delegation boundary — Core frozen, Formation attached. **Chosen:** (b). **Rationale:** the Core's identity (small, frozen, normative, mode-0-complete) survives untouched; each future formation may assign different semantics to the same mode space; and the boundary later proved to be a resource-accounting firewall and a publicity firewall as well.

**要点:** 設計者の初期スケッチは演算をCore近傍に置いていた。祐筆はF-F1となる代案を提示: Coreはモード≥1をデコードせず*発行*し、データパスはFormation所有の別ユニットとする。**選択肢:** (a) Core RTLの拡張、(b) 委譲境界。**採択:** (b)。**論拠:** Coreの同一性が無傷で残る;将来のFormationが同じモード空間に別意味論を与えうる;境界は後にリソース会計と広報の防火壁でもあると判明。

### 2. Which bands may Formation instructions inhabit? / Formation命令はどの帯域に住めるか

**Point:** All Formation instructions are window-only citizens (FG encounter → Error HALT), extending C3-F23. Arithmetic is BG-only; the sole Q citizen is CMT. **Alternatives:** FG-legal arithmetic; Q-legal arithmetic. **Chosen:** the FG-Formation exclusion (F-F2) and the BG-compute / Q-commit doctrine (F-F3). **Rationale:** the foreground remains a bare sequence of Stay durations — the timing chart stays the program text; the extended-operand ambiguity stays dissolved; and the Q band's trailing-edge firing (C4-F11) turns the page-swap commit into a structural zero-zipper-noise guarantee. Legality itself became the visual pattern of the table: red compute rows in Q, one green commit row.

**要点:** 全Formation命令はウィンドウ専用市民（FG遭遇→Error HALT、C3-F23拡張）。演算はBG専用、Q唯一の市民はCMT。**採択:** FG-Formation排除（F-F2）とBG演算/Qコミット教義（F-F3）。**論拠:** 前景は裸のStay並びのまま——タイミングチャートはプログラムテキスト;Q帯後縁発火（C4-F11）がページスワップを構造的ジッパーノイズゼロ保証に変える。合法性そのものが表の視覚模様になった。

### 3. The Stay-length mystery / Stay長のミステリー

**Point:** The architect's doctrine, delivered as a correction to the amanuensis's draft: Stay Set carries **no parameter** — it arms the counter and defines only *when* the stay began; the length is revealed only at the Stay instruction, "after all the chores for the next packet are done." **Consequence:** a minimum packet length exists (architect's estimate ≈ 32 waves); the 1-wave packet mechanism is deliberately deferred (Tie F-T1). **Rationale:** the philosophy of the window — housekeeping first, rest revealed last — is load-bearing, not incidental; it sets the floor of the packet grid.

**要点:** 設計者の教義（祐筆案への訂正として下された）: Stay Setは**無パラメータ**——カウンタを起動し*いつ*始まったかだけを定義;長さはStay命令で初めて明かされる。「次のパケットの世話を全部焼き終えてから」。**帰結:** 最小パケット長（目算≈32Wave);1Waveパケット機構は意図的保留（F-T1）。

### 4. The computability audit / 計算可能性監査

**Point:** The architect inverted the usual question: not "what can we add" but "**what can this machine never be** — not inefficiency, impossibility?" The amanuensis audited from the v0.1 table alone and answered with five impossibilities, each traced to a doctrine: (i) native conditional branching (data-blindness by creed); (ii) return-address capture — no subroutines or recursion (timing-chart readability); (iii) read-your-own-writes within a window (shadow-only writes); (iv) self-hosting — program memory owned outside the machine (the music-box doctrine); (v) interrupts (jitter-free doctrine). Plus the observation that CPU essence (decide, remember the way back, rewrite yourself, be interrupted) maps one-to-one onto determinism-destroying devices. **Rationale for preserving it verbatim:** the audit's *shape* — impossibilities as shadows of doctrines — survived its factual corrections and became Chapter 1's spine.

**要点:** 設計者は問いを反転した:「何を足せるか」ではなく「**この機械が決してなり得ないもの**——非効率ではなく不可能——は何か」。祐筆はv0.1表のみから五つの不可能で答え、それぞれを教義に帰した: (i) ネイティブ条件分岐（データ盲目信条）、(ii) 戻り番地捕獲——サブルーチン・再帰不能（チャート可読性）、(iii) ウィンドウ内read-your-own-writes（shadow専用書込）、(iv) 自己ホスティング——プログラムメモリの所有者は機械の外（オルゴール教義）、(v) 割り込み（ジッターフリー教義）。CPUの本質（決断・帰路の記憶・自己書換・中断）が決定論破壊装置と一対一対応するという観察を含む。**逐語保存の理由:** 監査の*形*——教義の影としての不可能——は事実訂正を生き延び、第1章の背骨となった。

### 5. The correction — the repository falsifies two impossibilities / 訂正——リポジトリが二つを反証する

**Point:** The architect then revealed the repositories ("I should have shown you sooner"), and noted that the stack is something he weights heavily — sub-sequences (Call/Return) and nested loops ride it. The amanuensis re-audited against Chapters 2–3 and issued a public correction: **(ii) was false** — the holding register auto-saves {SN return address, loop counter, base address} on Branch-taken / Call / Base Set / Insertion, Return restores, and the external stack bus delegates depth to the Formation; **(v) was false** — Insertion exists, with the deferred-to-Stay-timeup resolution: interrupts are not absent but **quantized to the deterministic grid**. Further: (i) has a front-door solution (ALU flags exported as Condition lanes — no WJV back door needed); (iii) is Formation doctrine, not Core law. Only (iv) is Core law. **Rationale:** the corrected audit is the spin-off's justification — the Core was already carrying the expensive machinery; what remained was chooseable doctrine.

**要点:** 設計者はここでリポジトリを開示し（「もっと早くお見せするべきだった」）、スタックを重視している——サブシーケンス（Call/Return）も入れ子ループもスタックに乗る——と告げた。祐筆は第2〜3章に照らして再監査し、公開訂正を発した: **(ii)は誤り**——保持レジスタがBranch成立/Call/Base Set/Insertionで{SN戻り番地・ループカウンタ・ベースアドレス}を自動退避、Returnが復元、外部スタックバスが深度をFormationに委譲済;**(v)は誤り**——Insertionは存在し、Stay-timeupまで遅延の解決により、割り込みは不在ではなく**決定論的グリッドに量子化**されている。さらに(i)には正門解（ALUフラグ→Conditionレーン）があり、(iii)はCore法ではなくFormation教義。Core法は(iv)のみ。**論拠:** 訂正済み監査こそ暖簾分けの正当性証明——高価な機構はCoreが既に担っており、残るは選択可能な教義だけだった。

### 6. The spin-off ruling / 暖簾分け裁定

**Point:** The architect ruled: WPMS-Formation, as drafted, becomes **PTSG-CPU-Formation** — the first data-processing PTSG — spun off as its own project with the amanuensis retained; PTSG-WPMS-Formation is then produced *from* it, inside FPGA Spectrum Engine, by subtraction and minimal addition, and handed to the WPMS amanuensis. Recorded as F-F12 (Fixed 2026-07-11, with the v0.1→v0.2 retitle). **Rationale:** the three-generation recursion — WPMS births generic children and becomes each child's first customer — repeats by design, not accident.

**要点:** 設計者裁定: 起草中のWPMS-Formationは**PTSG-CPU-Formation**——最初のデータ処理PTSG——として暖簾分けされ、祐筆は留任;PTSG-WPMS-Formationはその*引き算*（と最小限の足し算）でFPGA Spectrum Engine内に作られ、WPMS祐筆へ引き渡される。F-F12として記帳。**論拠:** 三世代再帰——WPMSは汎用の子を産み、その最初の顧客になる——は偶然でなく設計により反復する。

### 7. The split-stack doctrine / スタック二分教義

**Point:** Given the architect's stack emphasis, where do Push/Pop live? **Alternatives:** (a) a unified LIFO shared by control contexts and data words; (b) two stacks, two owners. **Chosen:** (b), as F-F9 (Fixed 2026-07-11): the control stack remains entirely the Core's (§3.7 holding register + §3.8 bus; its push/pop policy stays Core Tie C3-T6, not preempted); the data stack is a new datapath-owned LIFO (Accm words, M2·PSH/POP, hidden SP, overflow/underflow → Error HALT). One BRAM in two regions — or dual ports, or parallel blocks — is Implementation Arena; the architect explicitly endorsed exploiting dual-port and parallelism. **Rationale:** a unified stack couples the Core's save/restore protocol to program data discipline and makes the timing chart's readability depend on runtime stack contents.

**要点:** Push/Popはどこに住むか。**選択肢:** (a) 制御コンテキストとデータ語を混載する統合LIFO、(b) 二つのスタック、二人の所有者。**採択:** (b)、F-F9として（2026-07-11 Fixed）: 制御スタックは完全にCoreのもの（§3.7＋§3.8;push/pop方針はCore側Tie C3-T6のまま先取りしない）;データスタックは新設のデータパス所有LIFO（Accm語、M2·PSH/POP、SP隠蔽、オーバー/アンダーフロー→Error HALT）。1BRAM二領域・デュアルポート・並列ブロックはArena（設計者がデュアルポート/並列活用を明示支持）。**論拠:** 統合スタックはCoreの退避復元プロトコルをプログラムのデータ規律に結合させ、チャート可読性を実行時スタック内容に依存させる。

### 8. The read-only quartet / 読出し専用カルテット

**Point:** The architect requested SN as a datapath source (PTSG keeps the name *State Number*, not "address" — the foreground is an FSM and the state label is the richer truth), plus the Stay Start State as the window's label seen from the background, with distribution into general registers sanctioned if the source encoder grows too deep. The amanuensis joined these with K and I into the quartet SN/SSS/K/I — *now, here, when, which* (F-F10). A repository-informed subtlety: the Core's C3-F25 register is **short-lived** (valid within its Stay cycle, discharged into Base by a queued Base Set) and Core-invisible but explicitly copyable; the normative SSS source is therefore a **Formation-latched copy** taken at window start. Idioms recorded: SN + offset → WJV (relative computed dispatch — relocatable sub-sequences); SSS-indexed PPM lookup (one BG routine serving many Stays). **Rationale:** four sources, four temporal characters; read semantics normative, realization Arena, capture-distribution into R0–R3 the sanctioned fallback.

**要点:** 設計者はSNのソース化を要請（名は*State Number*のまま——前景はFSMであり状態ラベルの方が豊かな真実）、さらに背景から見たウィンドウのラベルとしてのStay Start State、エンコーダが深くなる場合の汎用レジスタ分散も容認。祐筆はK・Iと束ねてカルテットSN/SSS/K/I——*今・ここ・いつ・どれ*——とした（F-F10）。リポジトリ精読の妙味: C3-F25レジスタは**短寿命**（Que Base SetでBaseへ引き継ぎ）かつCore不可視だが持出可と明記——ゆえに規範のSSSソースは**ウィンドウ開始時のFormationラッチ写し**。イディオム: SN＋オフセット→WJV（相対計算ディスパッチ＝再配置可能サブシーケンス）;SSS索引PPM参照（一つの裏ルーチンが多数のStayに仕える）。

### 9. Pointer indirection through the front gate / 正門から入るポインタ間接参照

**Point:** The audit had named the two cells where CPU-hood could enter: ADRS as a store destination (pointer indirection) and register-source Jump/Branch legality in FG. Ruled 2026-07-11: the first gate **opens** for the CPU profile — F-T4 resolved into F-F13 (ADRS is a legal STA destination; subtraction profiles may re-forbid, WPMS expected to). The second gate, F-T5, remains an open Tie. **Rationale:** a data machine's bread and butter, admitted by explicit ruling with the profile escape hatch preserved — the turnstile pattern working as designed.

**要点:** 監査はCPU性の侵入口二セルを名指ししていた: STA宛先としてのADRS（ポインタ間接参照）と、レジスタソースJump/BranchのFG合法性。2026-07-11裁定: 第一の門はCPUプロファイルに**開く**——F-T4はF-F13へ解決（引き算プロファイルは再禁止可、WPMSは禁止見込み）。第二の門F-T5はTieのまま。**論拠:** データマシンの常食を、プロファイル退避路を保存した明示裁定で入場させる——回転扉パターンの設計通りの作動。

### 10. *Measured, not promised* / 約束せず、測って刻む

**Point:** The architect raised the resource-figure question through the Core's own experience: an early "few hundred, first compile 235" became, after v1.1's law and teeth, ~400-class — "the mood of an apology press conference," he laughed, even though the number is honestly small for what it buys. Ruling: the Core traces its figures version by version (its own rule, its own repository); the CPU-Formation states **no forward-looking resource figure anywhere** — figures exist only as Layer 4 measurements pinned to revision, device, toolchain, and configuration. **Alternatives:** advertise a target; stay silent until measured. **Chosen:** silence until measured, enforced in CONTRIBUTING (PRs adding forward-looking figures are declined) and audited mechanically over the seed documents. **Rationale:** a frozen body gets a weight log; a living body gets a growth record, never a weight promise — and the Free Precision Floor doctrine makes advance minimalism claims self-contradictory anyway. The two rules share one principle: numbers exist only pinned to measured versions.

**要点:** 設計者はCore自身の経験から問題を提起: 初期の「数百、初回コンパイル235」がv1.1の法と牙の後に400級へ——「気分は謝罪会見」（笑）、その数字自体は能力に対して十分小さいにもかかわらず。裁定: Coreは版ごとにトレース（Core自身の規則）;CPU-Formationは**前方宣言的リソース数値を一切書かない**——数値はLayer 4実測としてのみ、改訂・デバイス・ツールチェーン・設定に釘付けで存在。CONTRIBUTINGで執行（前方宣言数値を足すPRは却下）、種文書は機械的に監査済。**論拠:** 凍結体には体重記録、生体には成長記録——体重の約束はしない。Free Precision Floor教義は事前の極小自慢とそもそも矛盾する。二つの規則は一つの原理を共有する: 数字は測られた版に釘付けされた形でしか存在しない。

### 11. The industrial north star / 産業の北極星

**Point:** Before founding, the architect asked for a shared long-range vision — "it will weigh on every future ruling." The amanuensis offered, with optimism and evidence separated: the empty quadrant (programmable ∧ cycle-deterministic, PRU-adjacent but fabric-portable and table-specified); the auditability market (a fully enumerated Command × Phase semantics plus Layer-2 rationale traceability as certification dowry); boot-free, software-absent control; replication economics (conductor/players); the AI-native claim (constrain AI to instruction lists over a verified deterministic substrate — "RISC-V of timing control"); the Spectrum Engine synergy (phase-coherent multi-source excitation: vibration analysis, ultrasound, modal testing) and the formal near-identity of WPMS's Gaussian-packet scheduling with quantum-control pulse generation. With the honest reservation: a gap becomes a market only with toolchain and silicon evidence. **Consequences adopted as weights:** keep the Core frozen; Layer 4 investment is dowry, not just music; AI-affinity is a spec property, a touchstone for every Tie.

**要点:** 設立前に設計者は長期ビジョンの共有を求めた——「今後のあらゆる裁定に効いてくる」。祐筆は楽観と根拠を分けて提示: 空いた象限（プログラマブル∧サイクル決定論、PRU隣接だがファブリック可搬・表仕様）、監査可能性市場（全列挙のCommand × Phase意味論＋Layer 2根拠トレーサビリティは認証の持参金）、ブート不在・ソフトウェア不在制御、複製の経済（指揮者/奏者）、AIネイティブ主張（検証済み決定論基板上の命令リストへAIを拘束——「タイミング制御のRISC-V」）、Spectrum Engine相乗（位相コヒーレント多音源励振: 振動解析・超音波・モーダル試験）、そしてWPMSのGaussian波束スケジューリングと量子制御パルス生成の形式的近同一性。正直な留保つき: 空隙はツールチェーンと実機証拠があって初めて市場になる。**裁定の重みとして採択:** Coreの凍結死守;Layer 4投資は持参金;AI親和性は全Tieの試金石。

### 12. Naming by rhyme / 韻による命名

**Point:** For the inaugural trace's slug the architect offered a straight description and asked for "one twist." The chosen principle: **the slug carries genealogy, the title carries poetry** — the slug rhymes with the Core's own birth-from-WPMS trace so the three-generation recursion is legible from the Layer 2 inventories alone, while "The Five Impossibilities" names the session's intellectual heart in the title. / **要点:** 最初の軌跡のスラッグについて設計者は直叙案を出し「一ひねり」を求めた。採択原理: **スラッグは系譜を、表題は詩を運ぶ**——スラッグはCore自身のWPMS誕生トレースと韻を踏み、三世代再帰がLayer 2目録だけから読めるようにし、「五つの不可能」はセッションの知的心臓部として表題に置く。

---

## Major Themes / 主要テーマ

### Theme 1 — The audit that failed correctly / 正しく失敗した監査

Two of five impossibilities were falsified by the repository; the falsification revealed that the Core already carried the expensive machinery (stack contexts, quantized interrupts), and that the remaining barriers were doctrine, not law. A wrong audit performed honestly produced the founding insight; it is preserved wrong-and-corrected because the *correction path* is the lesson. / 五つのうち二つがリポジトリに反証され、その反証が「高価な機構はCoreが既に担い、残る障壁は法ではなく教義」であることを明かした。誠実に遂行された誤監査が設立の洞察を生んだ;*訂正の経路*こそが教訓なので、誤りと訂正の両方を保存する。

### Theme 2 — The delegation boundary pays three times / 三度払う委譲境界

F-F1 was adopted for architectural hygiene; it then paid as semantics freedom (profiles may re-define modes), as a resource-accounting firewall (the Core's figures stay stable however the Formation grows), and as the mechanism of the master/profile succession (F-F12). / F-F1は衛生のために採択され、その後、意味論の自由・リソース会計の防火壁・マスター/プロファイル継承機構として三度配当を払った。

### Theme 3 — Von Neumann at the turnstile / 回転扉のvon Neumann

CPU-hood enters one shape at a time, each shape with its own decision ID: LIFO read-back (PSH/POP, v0.2), pointer indirection (F-F13, ruled), computed dispatch made relative (SN + WJV), while random access (F-T6) and FG register-source branching (F-T5) wait outside. The machine approaches CPU-hood asymptotically and *on the record*. / CPU性は一度に一つの形で、各形が固有の決定IDを持って入場する。機械は*記録の上で*CPUに漸近する。

### Theme 4 — Time as an operand / オペランドとしての時間

The quartet exposes the machine's own position and time to its arithmetic — *now, here, when, which* — and the condition still stays outside (flags → Condition lanes). The machine may compute about itself without being allowed to lie to itself about time. / カルテットは機械自身の位置と時間を演算に開き——*今・ここ・いつ・どれ*——条件はなお外に留まる。機械は自らについて計算してよいが、時間について自らに嘘をつくことは許されない。

### Theme 5 — Two bodies, one principle / 二つの身体、一つの原理

The frozen Core traces its figures; the living Formation promises none. *Measured, not promised* and the Core's version-by-version trace are the same rule seen from a corpse and from a body: numbers exist only pinned to measured versions. / 凍結されたCoreは数値をトレースし、生きたFormationは何も約束しない。実測主義と版トレースは、同じ規則を屍体側と身体側から見たものである。

---

## Resumption Hooks / 再開フック

### Hook A — Layer 1 Chapters 2–5 / 第1層第2〜5章

Promote the v0.3 worksheet into normative chapters (registers & quartet; the Formation table; memory architecture; the Core interface contract). Starting question: does the chapter split of the 01_Architecture README survive contact with the material? / v0.3作業物を規範章へ昇格。起点の問い: 章割りは素材と接触して生き残るか。

### Hook B — Open Ties / 未決Tie

F-T1 (1-wave packet), F-T2 (queued mode-3 writes), F-T3 (Q-band arithmetic beyond commit), F-T5 (register-source Jump/Branch in FG), F-T6 (SP visibility / random-access scratch). Each waits for a proven need; none may be resolved by convenience. / 各Tieは証明された必要を待つ;便宜による解決は許されない。

### Hook C — The WPMS subtraction profile / WPMS引き算プロファイル

Hand the repository to the WPMS amanuensis; produce PTSG-WPMS-Formation by subtraction (+ shadow-PPM/CMT/RTW overlay retained, data stack optional, F-F13 expected re-forbidden). The hand-off document should be the profile's own decision register, keyed to this master's IDs. / 引き渡し文書はマスターIDに紐づくプロファイル自身の決定台帳であるべし。

### Hook D — The Maclaurin demo and the W1 translation trace / マクローリンデモとW1翻訳軌跡

First Layer 3 example: exp(−2γ) as a mode-1 MAC sequence; first W1-style Layer 2 trace: the behavior-description → instruction-list translation dialogue. / 最初のLayer 3例とW1型翻訳軌跡。

### Hook E — C5G and the first measurement / C5Gと最初の実測

The verification platform is the Terasic C5G (pure fabric, no HPS — the methodological control). The first Layer 4 entry will also be the first number this repository has ever stated. / 最初のLayer 4記帳は、本リポジトリが述べる史上最初の数字にもなる。

### Hook F — Housekeeping to the Core amanuensis / Core祐筆への引き継ぎ事項

Report the Layer 2 inventory/filename mismatch discovered during this session (the Core's 02 README lists a `birth-from-wpms` slug; the committed file is `2026-05-12_ptsg-emancipation-from-wpms-session`), so the rhyme between the two birth traces can be settled from the Core side, whichever direction is ruled. / 本セッション中に発見したCore側の目録/ファイル名不一致の報告——韻はCore側の裁定でどちら向きにも整えられる。

---

## End of Trace / 軌跡の末尾

This trace records the pre-launch council of PTSG-CPU-Formation: the audit, its correction, the founding rulings, and the governance that will outlive them. Its companion JSON carries the same content structured for machine ingestion. The repository this council founded is the canon; this trace is its memory.

本軌跡はPTSG-CPU-Formationの設立前会議——監査、その訂正、設立裁定群、そしてそれらより長生きする統治——を記録する。姉妹JSONは同内容を機械取り込み用に構造化して運ぶ。この会議が設立したリポジトリが正典であり、本軌跡はその記憶である。
