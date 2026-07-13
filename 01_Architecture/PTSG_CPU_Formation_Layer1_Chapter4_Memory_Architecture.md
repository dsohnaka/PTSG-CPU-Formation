# PTSG-CPU-Formation — Layer 1 Specification
# Chapter 4: Memory Architecture
# PTSG-CPU-Formation — 第1層仕様書
# 第4章: メモリアーキテクチャ

> **License: CC0 1.0 Universal (Public Domain)**
>
> **Status: DRAFT (2026-07-13).** This chapter gives the normative architecture of the Formation's memories: the packet-parameter memory and its atomic commit, the routing tables, and the data stack. It introduces one new proposal (F-F14, post-commit shadow inheritance) explicitly marked for ratification. Rulings F-F9, F-F13 are Fixed; other decisions cited are Proposed.
> **状態: ドラフト（2026-07-13）。** 本章はFormationのメモリ群——パケットパラメータメモリとその原子的コミット、ルーティングテーブル、データスタック——の規範アーキテクチャを与える。裁定対象として明示された新規提案一件（F-F14、コミット後shadow継承）を含む。F-F9・F-F13はFixed;引用するその他の決定はProposed。

---

## 4.1 Purpose and Scope / 目的とスコープ

Chapter 3 defined the verbs; this chapter defines the places they act. Three memory classes belong to the Formation — the packet-parameter memory (PPM), the routing tables (RT), and the data stack (DSTK) — and two more are named only to mark boundaries: the control stack (the Core's) and the instruction memory (nobody's inside the machine). Geometry — depths, widths, page sizes — is Implementation Arena throughout, documented per implementation; **what is normative here is law, not size**: who may read, who may write, when contents change, and what can never happen.

第3章は動詞を定義した;本章は動詞が作用する場所を定義する。三つのメモリクラスがFormationに属する——パケットパラメータメモリ（PPM）、ルーティングテーブル（RT）、データスタック（DSTK）——さらに二つが境界標identとしてのみ名指される: 制御スタック（Coreのもの）と命令メモリ（機械の内側では誰のものでもない）。形状——深さ・幅・ページサイズ——は全てImplementation Arenaであり実装ごとに文書化される;**ここで規範なのは大きさではなく法である**: 誰が読めるか、誰が書けるか、内容はいつ変わるか、そして何が決して起こり得ないか。

## 4.2 The Memory Map at a Glance / メモリ地図一覧

| Memory | Owner | Datapath read | Datapath write | Contents change at |
|---|---|---|---|---|
| **PPM active page** | Formation | LDM (source ID 2) | — (never) | CMT only (page swap) |
| **PPM shadow page** | Formation | — (never) | STM; STA (dest ID 1) | any BG write; CMT (swap) |
| **RT active** | Formation | consumed by attached hardware | — (never) | CMT only (same swap) |
| **RT shadow** | Formation | — | RTW | any BG write; CMT (swap) |
| **DSTK** | Formation | POP | PSH | any BG PSH/POP |
| Control stack | **Core** | — | — | Core machinery only (§4.7) |
| Instruction memory | **outside** | — | — | external owner only (§4.8) |

The map's asymmetries are the architecture: no cell of this table may be altered by an implementation. / 地図の非対称性こそがアーキテクチャである: 本表のいかなるセルも実装が変更してはならない。

## 4.3 PPM — the Packet-Parameter Memory / PPM——パケットパラメータメモリ

The PPM is a **paged pair**: at every instant exactly one page is *active* and the other is *shadow*. The attached consumer (in the first profile, a hardwired pipeline; in general, whatever the Formation drives) reads the active page continuously; the datapath reads the active page through LDM and writes **only** the shadow page through STM or STA. The two roles swap atomically at CMT (§4.4).

PPMは**ページ対**である: いかなる瞬間もちょうど一方のページが*active*、他方が*shadow*である。接続された消費者（最初のプロファイルではハードワイヤパイプライン;一般にはFormationが駆動する何であれ）はactiveページを連続的に読む;データパスはLDMでactiveページを読み、STMまたはSTAで**shadowページにのみ**書く。二つの役割はCMTで原子的に交代する（§4.4）。

Three laws follow, and together they are the glitch-freedom theorem of this machine:

三つの法が従い、合わせてこの機械のグリッチフリー定理を成す:

**PPM-1.** The active page is read-only to the datapath. A parameter the consumer is using cannot be touched — **mid-packet tearing is structurally impossible**, not procedurally forbidden (F-F7).
**PPM-1。** activeページはデータパスに対し読出し専用である。消費者が使用中のパラメータには触れ得ない——**パケット途中の裂けは構造的に不可能**であり、手続き的に禁止されているのではない（F-F7）。

**PPM-2.** The shadow page is write-only territory for staging: its contents are not observable by the datapath (no shadow-read source exists in §2.9), so no program can come to depend on staging order.
**PPM-2。** shadowページはステージングのための書込専用領域である: その内容はデータパスから観測不能であり（§2.9にshadow読出しソースは存在しない）、ゆえにいかなるプログラムもステージング順序に依存し得ない。

**PPM-3.** ADRS addresses one page-sized space; the active/shadow distinction is carried by the instruction (LDM vs STM), never by the address. An out-of-range ADRS is error E5.
**PPM-3。** ADRSは1ページ分の空間を指す;active/shadowの区別は命令（LDM対STM）が運び、アドレスは決して運ばない。範囲外ADRSはエラーE5である。

## 4.4 The Atomic Commit / 原子的コミット

**CMT** is registered during the background scan and fires at Stay-timeup, on the trailing edge of the prescale period, via the Core's single reservation register. Its normative semantics:

**CMT**は背景スキャン中に登録され、Stay-timeupにおいてプリスケール周期の後縁で、Coreの単一予約レジスタを介して発火する。その規範的意味論:

**CMT-1 (scope).** One CMT swaps the PPM pages **and** the RT pages as a single indivisible event. There is no partial commit and no separate routing commit: parameters and the routing that interprets them change worlds together.
**CMT-1（範囲）。** 一つのCMTはPPMページ**と**RTページを単一の不可分事象として交代させる。部分コミットも独立のルーティングコミットも存在しない: パラメータと、それを解釈するルーティングは、共に世界を移る。

**CMT-2 (observable atomicity).** The consumer observes, on every clock, either the entire old world or the entire new world — never a mixture. Realization by page-pointer flip is the recommended Arena choice; realization by copying is non-conforming unless it can preserve this observability, which at full consumer bandwidth it generally cannot.
**CMT-2（観測可能な原子性）。** 消費者は毎クロック、旧世界の全体か新世界の全体かのいずれかを観測する——混合は決して観測しない。ページポインタ反転による実現が推奨されるArena選択である;コピーによる実現は、この観測可能性を保てない限り不適合であり、消費者の全帯域下では一般に保てない。

**CMT-3 (timing).** The swap lands jitter-free on the packet boundary — the trailing edge at Stay-timeup — inheriting the Core's queued-firing law. This, and nothing else, is why the Formation needs no timing machinery of its own: **the Trailing-Edge Doctrine is the commit protocol.**
**CMT-3（タイミング）。** スワップはパケット境界——Stay-timeupの後縁——にジッターフリーで着地し、CoreのQue発火法を継承する。これこそが、そしてこれのみが、Formationが固有のタイミング機構を必要としない理由である: **後縁主義がコミットプロトコルである。**

**CMT-4 (arbitration).** Among multiple CMT registrations in one window, last-write-wins (the reservation register's own law). A CMT registration conflicting with a queued State-Number reservation is error E7: the program has asked the boundary to do two things, and the machine refuses to guess which.
**CMT-4（調停）。** 一ウィンドウ内の複数CMT登録間ではlast-write-wins（予約レジスタ自身の法）。QueされたState-Number予約と衝突するCMT登録はエラーE7である: プログラムは境界に二つの仕事を頼んだのであり、機械はどちらかを推測することを拒む。

**F-F14 (NEW, proposed) — post-commit shadow inheritance.** After a commit, **the new shadow holds the former active page's contents** (the natural consequence of a pointer flip, here made normative). Consequence: **delta-programming is first-class** — a program may rewrite only the parameters that change between packets and commit, because everything it does not touch is, by law, the value the consumer was just using. Without this guarantee every window would need a full-page rewrite, and the minimum packet length (Ch.1, F-T1 context) would be set by page size rather than by the *change set*. Rationale for making it law rather than Arena: programs written against inheritance are incorrect on a copy-and-clear implementation, so the property must be pinned on one side or the other; the delta-friendly side is the one the packet-parameter dataflow actually wants.
**F-F14（新規・提案）——コミット後shadow継承。** コミット後、**新しいshadowは直前のactiveページの内容を保持する**（ポインタ反転の自然な帰結を、ここで規範に昇格させる）。帰結: **差分プログラミングが一級市民になる**——プログラムはパケット間で変化するパラメータだけを書き換えてコミットしてよい。触れなかった全ては、法により、消費者が直前まで使っていた値だからである。この保証なしには全ウィンドウがページ全書換を要し、最小パケット長（第1章、F-T1文脈）はページサイズが決めることになる——*変更集合*ではなく。Arenaでなく法とする論拠: 継承を前提に書かれたプログラムはcopy-and-clear実装上で不正になるため、この性質はどちらかの側に釘付けされねばならず、パケットパラメータのデータフローが実際に望むのは差分親和の側である。

## 4.5 RT — the Routing Tables / RT——ルーティングテーブル

The routing tables are the PPM's interpreter: each entry maps an output of the attached consumer to the parameter slot(s) it perturbs in the next world — **packet free-routing**. Like the PPM they are a paged pair: RTW writes the shadow; the active table is consumed by the attached hardware; the same CMT swaps both (CMT-1). Boundary-synchronous routing change is therefore not a feature but a corollary: **the routing graph itself is a modulation target** (mode 3 made durations and targets data; RT makes topology data), and it can never change mid-packet for the same structural reason a parameter cannot tear. Entry format and table depth are Arena.

ルーティングテーブルはPPMの解釈者である: 各エントリは、接続消費者の出力を、それが次の世界で摂動するパラメータスロットへ写像する——**パケット自由ルーティング**。PPMと同様にページ対であり: RTWがshadowに書き、activeテーブルは接続ハードウェアが消費し、同一のCMTが両者を交代させる（CMT-1）。ゆえに境界同期のルーティング変更は機能ではなく系である: **ルーティンググラフ自体が変調対象であり**（モード3は長さと行先をデータにした;RTはトポロジーをデータにする）、パラメータが裂け得ないのと同じ構造的理由により、パケット途中では決して変わり得ない。エントリ形式とテーブル深さはArenaである。

## 4.6 DSTK — the Data Stack / DSTK——データスタック

Ownership and separation are doctrine **F-F9 (Fixed)**: the data stack belongs to the datapath, holds accumulator words, and is operated only by PSH/POP through the hidden SP (§2.5); it is architecturally distinct from the Core's control stack, though the two may share physical memory — one block in two regions, the two ports of a dual-port block, or parallel blocks, all Arena, with dual-port and parallel exploitation explicitly endorsed by the architect.

所有権と分離は教義**F-F9（Fixed）**である: データスタックはデータパスに属し、アキュムレータ語を保持し、隠されたSP（§2.5）を通じPSH/POPのみで操作される;Coreの制御スタックとはアーキテクチャ的に別物であるが、物理メモリの共有はよい——1ブロック2領域、デュアルポートの2ポート、並列ブロック、いずれもArenaであり、デュアルポート・並列の活用は設計者が明示支持している。

**DSTK-1 (persistence).** The stack persists across windows: what one window pushes, a later window may pop. Cross-window balance is the program's business, not the hardware's — the only hardware law is E6 (overflow/underflow → Error HALT). This is stated because silence is never a semantics: persistence is *legal*, and profiles that want boundary-balanced stacks must legislate it themselves.
**DSTK-1（持続性）。** スタックはウィンドウを跨いで持続する: あるウィンドウが積んだものを、後のウィンドウが降ろしてよい。ウィンドウ横断の均衡はプログラムの責務でありハードウェアの責務ではない——唯一のハードウェア法はE6（オーバー/アンダーフロー→Error HALT）である。沈黙は決して意味論ではないがゆえに明記する: 持続は*合法*であり、境界均衡スタックを望むプロファイルは自ら立法せねばならない。

**DSTK-2 (equipment).** In this master the data stack is mandatory equipment. A subtraction profile may omit it under the Core's stack-absence clause, documenting the omission and thereby forfeiting PSH/POP (whose encounter then falls under E4's reserved-ID law for that profile).
**DSTK-2（装備）。** 本マスターにおいてデータスタックは必須装備である。引き算プロファイルはCoreのスタック不在条項の下でこれを省略してよく、省略を文書化し、それによってPSH/POPを放棄する（当該プロファイルではその遭遇はE4の予約ID法に落ちる）。

## 4.7 The Control Stack — a Boundary Marker / 制御スタック——境界標

The control stack — holding-register contexts of {SN return address, loop counter, base address}, extended by the Core's external stack bus — is named here **only** to state that this chapter does not touch it. Its policies are the Core's open questions; its depth is Formation-provided equipment; nothing in the Formation instruction set reads or writes it. Two stacks, two owners (F-F9): this section is the fence between them.

制御スタック——{SN戻り番地・ループカウンタ・ベースアドレス}の保持レジスタコンテキスト、Coreの外部スタックバスによる拡張——は、本章がそれに触れないことを述べるため**のみ**にここで名指される。その方針はCoreの未決問題であり;その深さはFormationが供する装備であり;Formation命令セットの何もそれを読み書きしない。二つのスタック、二人の所有者（F-F9）: 本節は両者の間の柵である。

## 4.8 The Instruction Memory — Outside the Machine / 命令メモリ——機械の外

The instruction memory completes the map by absence: no Formation write path to it exists, may exist, or may be added (Ch.1 §1.5c — the retained impossibility, Core law). Its owner stands outside the machine; live editing is the Core's own affair. The tines of the music box are planted from without.

命令メモリは不在によって地図を完成させる: そこへのFormation書込経路は存在せず、存在してはならず、追加されてもならない（第1章§1.5c——保持された不可能、Core法）。その所有者は機械の外に立つ;動作中編集はCore自身の管轄である。オルゴールの櫛歯は外から植えられる。

## 4.9 Error Causes of this Chapter / 本章のエラー原因

E5 (ADRS out of range, §4.3), E6 (DSTK overflow/underflow, §4.6), E7 (CMT vs State-Number reservation conflict, §4.4) — all connecting to the Core's Error HALT machinery under CL-4, none downgradable.

E5（ADRS範囲外、§4.3）、E6（DSTKオーバー/アンダーフロー、§4.6）、E7（CMT対State-Number予約衝突、§4.4）——全てCL-4の下でCoreのError HALT機構に接続し、いずれも格下げ不能。

## 4.10 What is NOT in this Chapter / 本章に含まれないもの

Instruction semantics (Chapter 3); register and ID law (Chapter 2); the signal-level contract by which the consumer reads the active page and the Core's reservation register carries CMT (Chapter 5); all geometries, formats, and depths (Arena, documented per implementation); all application meaning of parameters and routings (profiles).

命令意味論（第3章）;レジスタとIDの法（第2章）;消費者がactiveページを読みCoreの予約レジスタがCMTを運ぶ信号レベル契約（第5章）;全ての形状・形式・深さ（Arena、実装ごとに文書化）;パラメータとルーティングの全ての応用的意味（プロファイル）。

## 4.11 Summary of Chapter 4 Decisions / 第4章決定事項のまとめ

| ID | Decision | Status |
|---|---|---|
| F-F7 | Paged PPM/RT; active read-only, shadow write-only (PPM-1/2/3); glitch-freedom structural | Proposed |
| F-F9 | DSTK ownership and separation; realization freedoms (§4.6) | **Fixed 2026-07-11** |
| F-F11 | E6 law (overflow/underflow → Error HALT) | Proposed |
| **F-F14** | **Post-commit shadow inheritance; delta-programming first-class** | **NEW — proposed for ratification** |
| — | CMT-1..CMT-4 (scope, observable atomicity, timing, arbitration) | Proposed |
| — | DSTK-1 persistence across windows; balance is program discipline | Proposed (new clarification) |
| — | DSTK-2 mandatory in master; omittable-with-documentation in profiles | Proposed |

## End of Chapter 4 / 第4章の末尾

One chapter remains in the launch set: the contract at the border — the issue port, the taps, the value registers, the Condition lanes — where this Formation and the frozen Core touch without ever holding each other.

ローンチ一式に残る章は一つ: 境界の契約——発行ポート、タップ、値レジスタ群、Conditionレーン——本Formationと凍結されたCoreが、決して互いを掴むことなく触れ合う場所である。
