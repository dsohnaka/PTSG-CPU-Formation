# PTSG-CPU-Formation — Layer 1 Specification
# Chapter 1: Scope and Design Philosophy
# PTSG-CPU-Formation — 第1層仕様書
# 第1章: スコープと設計哲学

> **License: CC0 1.0 Universal (Public Domain)**
> This is the architectural specification of PTSG-CPU-Formation — the first data-processing formation on the frozen PTSG-Core. Read it, redistribute it, build on it, regenerate from it.
>
> **ライセンス: CC0 1.0 Universal（パブリックドメイン）**
> これはPTSG-CPU-Formation——凍結されたPTSG-Core上の最初のデータ処理Formation——のアーキテクチャ仕様書である。読み、再配布し、その上に構築し、再生成してよい。
>
> **Status: DRAFT (2026-07-12).** Rulings F-F9, F-F12, F-F13 are Fixed; other decisions cited here are Proposed pending ratification. This chapter is normative in intent and provisional in status.
> **状態: ドラフト（2026-07-12）。** 裁定F-F9・F-F12・F-F13はFixed;本章が引くその他の決定は裁定待ちのProposed。本章は意図において規範、地位において暫定である。

---

## 1.1 Purpose of this Specification / 本仕様書の目的

This document is the first chapter of the PTSG-CPU-Formation Layer 1 specification. It establishes the **scope** of what this Formation is, the **design philosophy** that shapes it — centrally, a computability audit conducted by negation — and the **governance** under which the specification grows.

本文書はPTSG-CPU-Formation第1層仕様の最初の章である。本Formationとは何かの**スコープ**、それを形作る**設計哲学**——中心には否定形で行われた計算可能性監査がある——そして仕様が成長する際の**統治**を確立する。

A reader is assumed to have read PTSG-Core Chapter 1 (the trailing-edge principle, §1.4a) and Chapter 3 §3.4b (the normative Core behavior table). This Formation does not restate the Core; it stands on it.

読者はPTSG-Core第1章（後縁主義、§1.4a）と第3章§3.4b（規範Core挙動表）を読了しているものとする。本FormationはCoreを再述しない;その上に立つ。

## 1.2 What PTSG-CPU-Formation Is / PTSG-CPU-Formationとは何か

PTSG-CPU-Formation is a minimal instruction-driven datapath attached to the frozen PTSG-Core through the delegation boundary: an accumulator machine with a Horner-step MAC, an explicit data stack, packet-parameter memory with atomic boundary commit, and read-only taps into the Core's own position and time. It occupies sub-opcode modes 1–3 of the Global opcode space. Its defining property is negative: **computation cannot disturb timing, by construction.** Arithmetic is background-only; the sole reservation-band citizen is the atomic commit, firing jitter-free on the trailing edge of the packet boundary; interrupts arrive only as the Core's Insertion, quantized to the deterministic grid; and the foreground timing chart remains readable as the program text.

PTSG-CPU-Formationは、委譲境界を通じて凍結されたPTSG-Coreに接続される最小の命令駆動データパスである: HornerステップMACを持つアキュムレータ機械、明示データスタック、原子的境界コミット付きパケットパラメータメモリ、そしてCore自身の位置と時間への読出し専用タップ。Globalオペコード空間のサブオペコードモード1〜3を占める。その定義的性質は否定形である: **構造上、演算はタイミングを乱し得ない。** 算術は背景専用;予約帯域唯一の市民は原子的コミットであり、パケット境界の後縁にジッターフリーで発火する;割り込みはCoreのInsertionとしてのみ、決定論的グリッドに量子化されて到着する;そして前景タイミングチャートはプログラムテキストとして読める性質を保つ。

It is the **master data ISA** of the PTSG ecosystem (F-F12): application formations are produced from it by subtraction and minimal addition. Its first subtraction profile is PTSG-WPMS-Formation, inside FPGA Spectrum Engine.

本ISAはPTSGエコシステムの**マスターデータISA**である（F-F12）: 応用Formationは引き算と最小限の足し算でここから作られる。最初の引き算プロファイルは、FPGA Spectrum Engine内のPTSG-WPMS-Formationである。

## 1.3 What PTSG-CPU-Formation Is Not / PTSG-CPU-Formationでないもの

It is not a general-purpose CPU, and §1.5 specifies exactly why — not as apology but as product definition. It is not a modification of PTSG-Core: no Core RTL changes, no mode-0 semantics changes; the single Core-side amendment it requests (one "register source" operand bit on Stay/Loop/Jump/Branch) is itself subject to the Core's own table-before-RTL process. It is not an application: waveform mathematics, bin recurrences, and audio doctrine belong to profiles such as WPMS-Formation, not to this master.

これは汎用CPUではない——§1.5がその理由を、弁明としてではなく製品定義として、正確に規定する。PTSG-Coreの改変でもない: Core RTLの変更なし、モード0意味論の変更なし;要請する唯一のCore側修正（Stay/Loop/Jump/Branchへの「レジスタソース」オペランドビット1個）自体、Core側の表先行プロセスに服する。応用でもない: 波形数学・ビン漸化式・音響教義はWPMS-Formation等のプロファイルに属し、本マスターには属さない。

## 1.4 Design Philosophy — The Delegation Boundary / 設計哲学——委譲境界

**F-F1.** Mode 0 of the Global opcode space is Core-owned and frozen at Core v1.1. Modes ≥ 1 are Formation-owned. On encountering a mode ≥ 1 instruction, the Core does not decode it: it issues {mode, sub-op, operand D16–D31, current band} to the attached execution unit and advances the State Number per the Formation table's rows. This is the Core-Formation separation pattern applied recursively to the ISA itself.

**F-F1。** Globalオペコード空間のモード0はCore所有であり、Core v1.1で凍結されている。モード≥1はFormation所有。モード≥1命令に遭遇したCoreはそれをデコードせず、{モード, サブオペ, オペランドD16–D31, 現在帯域}を接続実行ユニットへ発行し、Formation表の行に従いState Numberを進める。これはCore-Formation分離パターンのISA自身への再帰適用である。

The boundary pays three ways. Semantically: a future formation (SDFT, instrumentation, AMO) may assign entirely different meanings to the same mode space. Forensically: the Core's resource figures remain stable and traceable in the Core's own repository however this Formation grows — the boundary is a resource-accounting firewall. Institutionally: it is the mechanism of the master/profile succession (§1.9).

境界は三通りに配当を払う。意味論的に: 将来のFormation（SDFT・計測・AMO）は同じモード空間に全く別の意味を与えうる。会計的に: 本Formationがどれだけ育っても、Coreのリソース数値はCore自身のリポジトリで安定にトレースされ続ける——境界はリソース会計の防火壁である。制度的に: それはマスター/プロファイル継承の機構である（§1.9）。

## 1.5 Design Philosophy — The Computability Audit / 設計哲学——計算可能性監査

This Formation was founded on an inverted question: not "what shall we add?" but "**what can this machine never be — not inefficiency, impossibility?**" The audit that answered it, including its own corrections, is this chapter's core. (The full dialogue is preserved as the inaugural Layer 2 trace.)

本Formationは反転された問いの上に設立された:「何を足すか」ではなく「**この機械が決してなり得ないもの——非効率ではなく不可能——は何か**」。それに答えた監査は、その訂正も含めて、本章の核である。（対話の全容は最初のLayer 2軌跡として保存されている。）

### 1.5a The five impossibilities, as first audited / 最初に監査された五つの不可能

Audited from the behavior table alone, the machine could never: **(i)** branch natively on its own data — no flags register, no compare instruction; the Core is data-blind by creed; **(ii)** capture a return address — no subroutines, no recursion; **(iii)** read its own writes within a window — the shadow-only write discipline; **(iv)** load, generate, or modify its own program — the program memory's owner stands outside the machine; **(v)** be interrupted — preemption is jitter incarnate. And the audit observed a correspondence: the essence of a CPU — *decide, remember the way back, rewrite yourself, be interrupted* — maps one-to-one onto devices that destroy cycle determinism. General-CPU-hood and jitter-freedom trade off structurally, not incidentally.

挙動表のみから監査された機械は、決して: **(i)** 自らのデータでネイティブに分岐できない——フラグレジスタも比較命令もなく、Coreは信条によりデータ盲目;**(ii)** 戻り番地を捕獲できない——サブルーチンも再帰もない;**(iii)** ウィンドウ内で自らの書き込みを読み返せない——shadow専用書込規律;**(iv)** 自らのプログラムをロード・生成・改変できない——プログラムメモリの所有者は機械の外に立つ;**(v)** 割り込まれ得ない——横取りはジッターの化身である。そして監査は対応を観察した: CPUの本質——*決断し、帰路を記憶し、自己を書き換え、中断される*——は、サイクル決定論を破壊する装置と一対一対応する。汎用CPU性とジッターフリー性は、偶然ではなく構造的にトレードオフする。

### 1.5b The correction — what the Core already held / 訂正——Coreが既に持っていたもの

Re-audited against the Core repository, two impossibilities fell. **(ii) was false:** the Core's internal information-holding register auto-saves {SN return address, loop counter, base address} on Branch-taken, Sub-sequence Call, Base Set, and Insertion; Return restores; the external stack bus delegates nesting depth to the Formation. **(v) was false:** Insertion exists, and its acceptance is deferred to Stay-timeup — interrupts are not absent but **quantized to the deterministic grid**. Further, **(i)** has a front-door resolution that needs no Core change at all: the datapath exports its ALU flags as external Condition lanes, and the Core consults them exactly as it consults any Condition — the creed of data-blindness is preserved while the loop closes outside. And **(iii)** turned out to be Formation *doctrine*, not Core law: a profile may choose shadow discipline (WPMS does, for audio) or direct scratch semantics.

Coreリポジトリに照らした再監査で、二つの不可能が倒れた。**(ii)は誤り:** Coreの内部情報保持レジスタはBranch成立・Sub-sequence Call・Base Set・Insertionで{SN戻り番地・ループカウンタ・ベースアドレス}を自動退避し、Returnが復元し、外部スタックバスがネスト深度をFormationへ委譲する。**(v)は誤り:** Insertionは存在し、その受理はStay-timeupまで遅延される——割り込みは不在ではなく**決定論的グリッドに量子化**されている。さらに**(i)**にはCore変更を一切要しない正門解がある: データパスがALUフラグを外部Conditionレーンとして公開し、Coreはそれを他のあらゆるConditionと同様に参照する——データ盲目の信条は保存されたまま、ループは外で閉じる。そして**(iii)**はCore法ではなくFormation*教義*だった: プロファイルはshadow規律（WPMSは音響のためにこれを選ぶ）も直読直書き意味論も選べる。

### 1.5c What remains impossible — and why that is the product / なお不可能なもの——それが製品である理由

One impossibility is Core law and is kept deliberately: **(iv) the program's owner stands outside the machine.** No datapath write path to instruction memory exists; loading, self-hosting, and self-modification are permanently excluded. The tines of the music box are planted from outside; the planting is itself the performance. And one impossibility survives in transformed shape: interrupts exist but cannot jitter — **a preemption model quantized to packet boundaries**, which for deterministic real-time work is not a concession but the selling point. What separates this machine from CPU-hood is therefore not missing hardware but retained doctrine: the machine approaches CPU-hood only through the turnstile (§1.7), one shape at a time, each admission on the record.

一つの不可能はCore法であり、意図的に保持される: **(iv) プログラムの所有者は機械の外に立つ。** データパスから命令メモリへの書込経路は存在せず、ロード・自己ホスティング・自己改変は恒久的に排除される。オルゴールの櫛歯は外から植えられ、植えること自体が演奏である。そしてもう一つの不可能は姿を変えて生き残る: 割り込みは存在するがジッターし得ない——**パケット境界に量子化された横取りモデル**であり、決定論的リアルタイム作業にとってこれは譲歩ではなく売り物である。ゆえにこの機械をCPU性から隔てるのは欠けたハードウェアではなく保持された教義である: 機械は回転扉（§1.7）を通ってのみ、一度に一つの形で、各入場を記録に残しながらCPU性に近づく。

**A note on computability proper:** across windows, with state carried through commits, the machine is computationally general in the ordinary informal sense. The impossibilities above are architectural semantics — statements about *how* the machine may compute, never about *what* is computable. Nothing in this chapter should be read as a formal computability theorem.

**計算可能性そのものについての注:** ウィンドウを跨ぎ、コミット越しに状態を運べば、機械は通常の非形式的な意味で計算汎用である。上の不可能群はアーキテクチャ意味論——機械が*いかに*計算してよいかの言明——であり、*何が*計算可能かの言明では決してない。本章のいかなる記述も形式的な計算可能性定理として読まれてはならない。

## 1.6 Design Philosophy — BG Computes, Q Commits / 設計哲学——BGが計算し、Qがコミットする

**F-F2 (window-only citizenship).** Every Formation instruction is a window-only citizen; a foreground encounter is a runaway and takes the Error HALT path (extending the Core's FG-Global exclusion, C3-F23). Consequently the foreground of a disciplined program remains a bare sequence of Stay durations — the timing chart remains the program text — and the extended-operand field D16–D31 is unambiguous wherever Formation instructions are legal.

**F-F2（ウィンドウ専用市民権）。** 全Formation命令はウィンドウ専用市民であり、前景での遭遇は暴走としてError HALT経路を取る（CoreのFG-Global排除C3-F23の拡張）。帰結として、規律あるプログラムの前景は裸のStay並びのまま——タイミングチャートはプログラムテキストのまま——であり、拡張オペランド域D16–D31はFormation命令が合法な全所で一義である。

**F-F3 (the band doctrine).** The background band is the compute band: all arithmetic, staging, and stack traffic runs there at full system clock. The reservation band is the commit band: its sole Formation citizen is the atomic page-swap commit, registered during the scan and firing at Stay-timeup on the trailing edge. Double-buffered, boundary-synchronous parameter commitment therefore falls out of the Core's existing band semantics with **zero new timing machinery** — the Trailing-Edge Doctrine, inherited, becomes the glitch-freedom guarantee.

**F-F3（帯域教義）。** 背景帯域は演算帯である: 全ての算術・ステージング・スタック交通がフルシステムクロックでそこを走る。予約帯域はコミット帯である: その唯一のFormation市民は原子的ページスワップコミットであり、スキャン中に登録され、Stay-timeupの後縁で発火する。ゆえにダブルバッファの境界同期パラメータ確定は、Coreの既存帯域意味論から**新規タイミング機構ゼロ**で得られる——継承された後縁主義が、そのままグリッチフリー保証になる。

**The Stay-length mystery (architect's doctrine).** Stay Set carries no parameter: it arms the stay counter and defines only when the stay began; the duration is revealed only upon reaching the Stay instruction — after all next-packet housekeeping is done. A minimum packet length therefore exists, set by the clock cost of the next-packet program; the mechanism for packets below it is deliberately deferred (Tie F-T1).

**Stay長のミステリー（設計者教義）。** Stay Setは無パラメータである: ステイカウンタを起動し、いつ始まったかだけを定義する;長さはStay命令に到達して初めて——次パケットの世話を全部焼き終えてから——明かされる。ゆえに最小パケット長が存在し、次パケットプログラムのクロックコストがそれを定める;それ未満のパケットの機構は意図的に保留される（Tie F-T1）。

## 1.7 Design Philosophy — Two Stacks, Two Owners; the Turnstile / 設計哲学——二つのスタック、二人の所有者;回転扉

**F-F9 (Fixed).** The control stack — holding-register contexts of {SN return address, loop counter, base address} — belongs entirely to the Core's own machinery; its push/pop policy remains the Core's open question and is not preempted here. The data stack is new, datapath-owned, holding accumulator words, operated explicitly by PSH/POP with a **hidden stack pointer**; overflow and underflow take the Error HALT path (F-F11). A unified stack was considered and declined: it would couple the Core's save/restore protocol to program data discipline and make the timing chart's readability depend on runtime stack contents. Physical realization — one memory in two regions, dual ports, parallel blocks — is Implementation Arena.

**F-F9（Fixed）。** 制御スタック——{SN戻り番地・ループカウンタ・ベースアドレス}の保持レジスタコンテキスト——は完全にCore固有機構に属し、そのpush/pop方針はCore側の未決問題のままここでは先取りしない。データスタックは新設・データパス所有で、アキュムレータ語を保持し、PSH/POPで明示操作され、**スタックポインタは隠蔽**される;オーバーフロー/アンダーフローはError HALT経路を取る（F-F11）。統合スタックは検討の上、不採用とされた: Coreの退避復元プロトコルをプログラムのデータ規律に結合させ、チャートの可読性を実行時スタック内容に依存させるからである。物理的実現——1メモリ2領域・デュアルポート・並列ブロック——はImplementation Arenaである。

**Von Neumann-ness at the turnstile.** Read-your-own-writes is granted in its weakest useful form first: LIFO (what a window pushes, the same window may pop). Pointer indirection — a computed store destination — was admitted by explicit ruling for this master (F-F13, Fixed), with the profile escape hatch preserved. Random-access scratch, SP visibility, and frame pointers wait outside (Tie F-T6, deferral ratified). Each admission of CPU-hood receives its own decision ID and its own behavior-table rows before any RTL exists. The machine approaches CPU-hood asymptotically, and on the record.

**回転扉のvon Neumann性。** read-your-own-writesはまず最弱の有用形態——LIFO（ウィンドウが積んだものは同じウィンドウが降ろせる）——で許可される。ポインタ間接参照——計算されたストア宛先——は本マスターに対し明示裁定で入場した（F-F13、Fixed）。プロファイル退避路は保存される。ランダムアクセススクラッチ・SP可視化・フレームポインタは外で待つ（Tie F-T6、保留裁定済）。CPU性の各入場は、RTLが存在する前に、固有の決定IDと固有の挙動表行を受け取る。機械は記録の上で、漸近的にCPU性へ近づく。

## 1.8 Design Philosophy — The Read-Only Quartet / 設計哲学——読出し専用カルテット

**F-F10.** Four read-only sources expose the machine's own position and time to its arithmetic — *now, here, when, which*: **SN**, the current State Number (self-position of the reading instruction; the name is kept — the foreground is an FSM, and the state label is the richer truth than "address"); **SSS**, the Stay Start State as the label of the current window — normatively a Formation-latched copy taken at window start, because the Core's own register is short-lived and Core-invisible but explicitly copyable; **K**, the stay counter, On-Tick and jitter-free — packet time; **I**, the loop iteration state — the sweep index. K and I run on different clocks of different worlds (time axis vs space axis); every consumer names its axis. Read semantics are normative — each source returns its value at the reading instruction's execution clock — while realization is Arena, with capture-distribution into the reserved registers as the sanctioned fallback. Two idioms are canonical: SN + offset → Jump-value = relative computed dispatch (relocatable sub-sequences); SSS-indexed parameter lookup = one background routine serving many windows.

**F-F10。** 四つの読出し専用ソースが機械自身の位置と時間を演算に開く——*今・ここ・いつ・どれ*: **SN**、現在のState Number（読んだ命令の自己位置;名は保持される——前景はFSMであり、状態ラベルは「番地」より豊かな真実である）;**SSS**、現在ウィンドウのラベルとしてのStay Start State——規範的にはウィンドウ開始時にFormationがラッチした写しである。Core自身のレジスタは短寿命かつCore不可視だが持出可と明記されているからである;**K**、ステイカウンタ、On-Tickかつジッターフリー——パケット時刻;**I**、ループ反復状態——掃引インデックス。KとIは異なる世界の異なるクロックを走る（時間軸対空間軸）;全ての消費者は自らの軸を名指しする。読出し意味論は規範——各ソースは読んだ命令の実行クロック時点の値を返す——であり、実現はArena、予約レジスタへのcapture分散が公認フォールバックである。二つのイディオムが正典である: SN＋オフセット→Jump値＝相対計算ディスパッチ（再配置可能サブシーケンス）;SSS索引パラメータ参照＝一つの裏ルーチンが多数のウィンドウに仕える。

The condition, meanwhile, stays outside: the datapath publishes its ALU flags as external Condition lanes — the front door — and the Core consults them by its own unchanged creed.

一方、条件は外に留まる: データパスはALUフラグを外部Conditionレーンとして公開し——正門——Coreは自らの不変の信条によってそれを参照する。

## 1.9 Master and Profiles / マスターとプロファイル

**F-F12 (Fixed).** This repository is the master data ISA. Application formations are produced from it by **subtraction** and minimal addition, each with its own decision register keyed to this master's IDs. A profile may re-forbid what the master admits (WPMS-Formation is expected to re-forbid pointer indirection and to retain the shadow-commit overlay as audio doctrine) and may omit equipment the master mandates where the Core's own optionality clauses permit, documenting the omission. The first profile is PTSG-WPMS-Formation, produced inside FPGA Spectrum Engine — the parent project of this entire ecosystem and, by design rather than accident, the first customer of each of its own children.

**F-F12（Fixed）。** 本リポジトリはマスターデータISAである。応用Formationは**引き算**と最小限の足し算でここから作られ、各々が本マスターのIDに紐づく固有の決定台帳を持つ。プロファイルはマスターが入場させたものを再禁止してよく（WPMS-Formationはポインタ間接参照を再禁止し、shadowコミットオーバーレイを音響教義として保持する見込み）、Core自身の任意条項が許す範囲でマスターが必須とする装備を省略してよい（省略は文書化される）。最初のプロファイルはFPGA Spectrum Engine内で作られるPTSG-WPMS-Formationである——本エコシステム全体の親プロジェクトであり、偶然ではなく設計により、自らの子供たち一人一人の最初の顧客である。

## 1.10 Governance / 統治

**Table-before-RTL (F-F8).** No instruction exists until its full Command × Phase row — all three bands, all columns — is written and ratified. The behavior table is simultaneously the specification, the verification item list, and the translation contract for LLM collaborators. Decision bookkeeping follows the Core's conventions: F-F* for Fixed/Proposed decisions, F-T* for Ties; Ties are resolved only by proven need, never by convenience; Implementation Arena marks freedoms deliberately left to implementations.

**表がRTLに先行する（F-F8）。** いかなる命令も、そのCommand × Phase行——3帯域・全列——が書かれ裁定されるまで存在しない。挙動表は同時に、仕様であり、検証項目表であり、LLM協働者への翻訳契約である。決定記帳はCoreの慣例に従う: Fixed/Proposed決定はF-F*、TieはF-T*;Tieは証明された必要によってのみ解決され、便宜によっては決して解決されない;Implementation Arenaは実装に意図的に残された自由を標す。

**Measured, not promised.** Resource figures appear in this repository **only** in Layer 4, as measurements pinned to a named revision, device, toolchain version, and synthesis configuration. No forward-looking resource figure is stated in any document. A growing formation is a living body: it gets a growth record, never a weight promise — while the frozen Core traces its own figures version by version in its own repository. Quoting published, version-pinned measured figures from other repositories is permitted — historical record, not forward-looking claim (ruled 2026-07-12). Two rules, one principle: numbers exist only pinned to measured versions.

**約束せず、測って刻む。** リソース数値は本リポジトリでは**Layer 4にのみ**、命名された改訂・デバイス・ツールチェーン版・合成設定に釘付けされた実測として現れる。前方宣言的なリソース数値はいかなる文書にも書かれない。成長するFormationは生体である: 成長記録は持つが、体重の約束はしない——一方、凍結されたCoreは自らのリポジトリで版ごとに数値をトレースする。他リポジトリの公開済み・版釘付け実測値の引用は可——歴史的記録であって前方宣言ではない（2026-07-12裁定）。二つの規則、一つの原理: 数字は測られた版に釘付けされた形でしか存在しない。

**AI-affinity as a ruling touchstone.** Inherited from the Core's design philosophy and adopted here as an explicit weight on every Tie: the specification must remain regenerable — a reader, human or model, holding only this repository, should be able to rebuild the Formation and arrive somewhere recognizably equivalent.

**裁定の試金石としてのAI親和性。** Coreの設計哲学から継承し、全Tieへの明示的な重みとしてここに採択する: 仕様は再生成可能であり続けねばならない——本リポジトリのみを手にした読者は、人間であれモデルであれ、Formationを再構築し、認識可能な程度に等価な地点へ到達できるべきである。

## 1.11 Relationship to PTSG-Core and Reading Order / PTSG-Coreとの関係と読了順

The Core is prerequisite, frozen, and never restated. The interface between the two is a short contract (Chapter 5): the issue port; the K/I taps and the SSS window-start latch; the value registers with the single register-source operand bit; the Condition lanes; and the shared single reservation register for the commit. Recommended order: Core Chapters 1 and 3 → this chapter → the Formation behavior table (Chapter 3 of this specification, currently the v0.3 worksheet) → Chapters 2, 4, 5 as they mature.

Coreは前提であり、凍結されており、決して再述されない。両者のインターフェースは短い契約である（第5章）: 発行ポート;K/Iタップとウィンドウ開始時のSSSラッチ;単一のレジスタソースオペランドビットを伴う値レジスタ群;Conditionレーン;コミットのための共有単一予約レジスタ。推奨読了順: Core第1章・第3章→本章→Formation挙動表（本仕様第3章、現状はv0.3作業物）→第2・4・5章は成熟の都度。

## 1.12 What is NOT in this Chapter / 本章に含まれないもの

Register widths, ID encodings, and memory geometries (Chapter 2 and Implementation Arena); the normative Command × Phase rows (Chapter 3); memory architecture and the commit protocol in detail (Chapter 4); the Core interface contract in signal-level detail (Chapter 5); all application mathematics (profiles).

レジスタ幅・IDエンコーディング・メモリ形状（第2章およびImplementation Arena）;規範Command × Phase行（第3章）;メモリアーキテクチャとコミットプロトコルの詳細（第4章）;信号レベルのCoreインターフェース契約（第5章);全ての応用数学（プロファイル）。

## 1.13 Summary of Chapter 1 Decisions / 第1章決定事項のまとめ

| ID | Decision | Status |
|---|---|---|
| F-F1 | Delegation boundary: mode 0 Core-frozen; modes ≥ 1 Formation-owned, issued undecoded | Proposed |
| F-F2 | FG-Formation exclusion: window-only citizenship; FG encounter → Error HALT | Proposed |
| F-F3 | BG computes, Q commits; CMT sole Q citizen | Proposed |
| F-F8 | Table-before-RTL governance | Proposed |
| F-F9 | Split-stack doctrine; hidden SP | **Fixed (2026-07-11)** |
| F-F10 | Read-only quartet SN/SSS/K/I; SSS as Formation-latched copy | Proposed |
| F-F12 | Master/profile succession; WPMS-Formation first subtraction profile | **Fixed (2026-07-11)** |
| F-F13 | Pointer indirection legal in the CPU profile; profiles may re-forbid | **Fixed (2026-07-11)** |
| — | Measured, not promised (governance) | Ratified in session; codified here |
| F-T1 | Sub-minimum (1-wave) packet mechanism | Tie, deferred |
| F-T5 | Register-source Jump/Branch legality in FG | Tie, open |
| F-T6 | SP visibility / random-access scratch | Tie, deferral ratified |

## End of Chapter 1 / 第1章の末尾

The next chapters descend from philosophy to structure: the register file and the quartet (Chapter 2), the normative table (Chapter 3), memory and commit (Chapter 4), the Core contract (Chapter 5). Nothing in them may contradict the impossibilities retained here — they are the product.

続く章は哲学から構造へ降りる: レジスタファイルとカルテット（第2章）、規範表（第3章）、メモリとコミット（第4章）、Core契約（第5章）。いずれも、ここで保持された不可能群と矛盾してはならない——それこそが製品である。
