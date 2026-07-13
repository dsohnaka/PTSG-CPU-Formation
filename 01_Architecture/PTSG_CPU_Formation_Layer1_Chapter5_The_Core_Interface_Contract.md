# PTSG-CPU-Formation — Layer 1 Specification
# Chapter 5: The Core Interface Contract
# PTSG-CPU-Formation — 第1層仕様書
# 第5章: Coreインターフェース契約

> **License: CC0 1.0 Universal (Public Domain)**
>
> **Status: DRAFT (2026-07-13).** This chapter is the border treaty: the complete, closed list of everything that crosses between the frozen PTSG-Core and this Formation. Signal roles and laws are normative; namings, widths, and polarities are Implementation Arena unless pinned below. The chapter also maintains the **Interface Request Register** (§5.9) — the complete list of what this Formation asks of the Core repository, each item subject to the Core's own table-before-RTL process.
> **状態: ドラフト（2026-07-13）。** 本章は国境条約である: 凍結されたPTSG-Coreと本Formationの間を渡る全てのものの、完全かつ閉じた一覧。信号の役割と法は規範であり;命名・幅・極性は以下で釘付けされない限りImplementation Arenaである。本章はまた**インターフェース依頼台帳**（§5.9）——本FormationがCoreリポジトリに求める事項の完全一覧、各項目はCore自身の表先行プロセスに服する——を保守する。

---

## 5.1 Purpose — Touching Without Holding / 目的——掴まずに触れる

The Core is frozen; the Formation is alive. The interface between them must therefore be **complete** (nothing crosses the border that is not listed here), **thin** (every crossing is a value or a strobe, never shared state), and **one-way per lane** (each signal has exactly one owner). Under this treaty the Core needs no knowledge of what the Formation computes, and the Formation no knowledge of how the Core sequences — each side could be replaced by any conforming implementation without the other noticing. That mutual ignorance is the product of Chapter 1 made mechanical.

Coreは凍結され、Formationは生きている。ゆえに両者のインターフェースは**完全**（ここに列挙されないものは国境を渡らない）、**薄い**（全ての越境は値かストローブであり、共有状態では決してない）、**レーンごとに一方向**（各信号はちょうど一人の所有者を持つ）でなければならない。この条約の下、CoreはFormationが何を計算するかを知る必要がなく、FormationはCoreがどう順序付けるかを知る必要がない——どちらの側も、相手に気づかれることなく、任意の適合実装で置換されうる。この相互不知こそ、第1章の産物の機械化である。

## 5.2 The Border at a Glance / 国境一覧

| # | Lane | Direction | Kind | Law |
|---|---|---|---|---|
| 1 | Issue port {mode, sub-op, operand, band} + valid | Core → Formation | event | §5.3 |
| 2 | K tap (stay counter) | Core → Formation | continuous value | §5.4 |
| 3 | I tap (loop state) | Core → Formation | continuous value | §5.4 |
| 4 | SSS latch source (Stay Start State) + window-start event | Core → Formation | value + event | §5.4 |
| 5 | StayVal / LoopVal / JumpVal | Formation → Core | registered values | §5.5 |
| 6 | Condition lanes (ALU flags) | Formation → Core | continuous values | §5.6 |
| 7 | Commit-fire strobe (CMT at Stay-timeup) | Core → Formation | event | §5.7 |
| 8 | Formation error strobe (+ cause code, Arena) | Formation → Core | event | §5.8 |
| 9 | Clock, reset | Core domain → Formation | infrastructure | §5.10 |

Nine lanes. There is no lane 10; a design needing one is a design amending this treaty, by decision ID, in daylight.

9レーン。レーン10は存在しない;それを必要とする設計は、本条約を、決定IDにより、白昼堂々と修正する設計である。

## 5.3 The Issue Port / 発行ポート

On executing a Global instruction whose **mode field ≥ 1**, the Core presents {mode, sub-op, operand D16–D31, current band} with a valid strobe, and advances its own State Number per the common law (Ch.3 CL-2). The Core does not decode the sub-op (F-F1); it inspects only the mode field, which it must inspect anyway to delegate.

**モード域≥1**のGlobal命令の実行にあたり、Coreは{モード, サブオペ, オペランドD16–D31, 現在帯域}をvalidストローブと共に提示し、共通法（第3章CL-2）に従い自らのState Numberを進める。Coreはサブオペをデコードしない（F-F1）;検査するのはモード域のみであり、それは委譲のためにいずれにせよ検査せねばならない。

**IF-1 (the Core never waits).** There is no handshake, no ready, no stall. The Formation accepts every issue in the clock it is presented. A stall lane would let data delay time — the one corruption this architecture exists to make impossible.
**IF-1（Coreは決して待たない）。** ハンドシェイクもreadyもストールもない。Formationは全ての発行を、提示されたクロックで受理する。ストールレーンはデータに時間を遅らせることを許す——本アーキテクチャがその不可能化のために存在する、唯一の腐敗である。

**IF-2 (single-clock visible semantics).** The architectural effects of an issued instruction are visible to the next issued instruction. Internal pipelining is Arena **only if invisible** — an implementation that pipelines its multiplier must forward, or it is non-conforming. (This is CL-2 restated as a border law: the Core's SN advances every clock, so the program's view advances every clock.)
**IF-2（単一クロック可視意味論）。** 発行された命令のアーキテクチャ的効果は、次に発行される命令から可視である。内部パイプライン化は**不可視である限りにおいてのみ**Arenaである——乗算器をパイプライン化する実装はフォワーディングせねばならず、さもなくば不適合である。（これはCL-2の国境法としての再述である: CoreのSNは毎クロック進み、ゆえにプログラムの視界も毎クロック進む。）

**IF-3 (foreground silence).** Because the Core's own FG-Global exclusion (C3-F23) halts any Global encountered in the foreground, the issue port never fires with band = FG. Error E1 is thus enforced on the Core's side of the border by existing Core law; the Formation may treat a FG-banded issue as architecturally impossible.
**IF-3（前景の沈黙）。** Core自身のFG-Global排除（C3-F23）が前景で遭遇したあらゆるGlobalをHALTさせるため、発行ポートはband = FGで発火することがない。ゆえにエラーE1は国境のCore側で既存のCore法により執行され、FormationはFG帯域の発行をアーキテクチャ的に不可能として扱ってよい。

## 5.4 The Taps / タップ群

**K and I** cross as continuous values: the Formation reads them combinationally as sources (§2.6c–d); no event accompanies them. Their widths follow the Core's counters (Arena at the Core's side of the fence).

**KとI**は連続値として渡る: Formationはソースとしてこれらを組合せ的に読む（§2.6c–d）;イベントは伴わない。幅はCoreのカウンタに従う（柵のCore側のArena）。

**SSS** crosses as a value–event pair: the Core exposes its Stay Start State register (short-lived by Core law, C3-F25, "Formation may copy out"), and a **window-start event** — the execution of Stay Set — tells the Formation when to latch. The latched copy, stable for the whole window, is the SSS source of §2.6b. The Core's register may subsequently discharge or be overwritten; the Formation's copy, by construction, does not care.

**SSS**は値–イベント対として渡る: CoreはStay Start Stateレジスタ（Core法により短寿命、C3-F25、「Formationは持出可」）を露出し、**ウィンドウ開始イベント**——Stay Setの実行——がFormationにラッチの時を告げる。ラッチされた写しはウィンドウ全体にわたり安定であり、§2.6bのSSSソースである。Coreのレジスタはその後放電も上書きもされてよい;Formationの写しは、構造上、それを意に介さない。

## 5.5 The Value Registers and the One Amendment / 値レジスタ群と唯一の修正

StayVal, LoopVal, and JumpVal live on the Formation's side and are written by mode 3 (§3.5). Their **read side** is the Core's: a Stay, Loop, Jump, or Branch whose operand selects **register source** samples the corresponding register once, on arrival, frozen thereafter, read-before-write on the same-cycle conflict (F-F4). This requires exactly **one Core-side amendment — the register-source operand bit** on those instructions (Interface Request Register, INT-R1). The asymmetry is deliberate: the Formation may write at any BG clock; the Core reads at one defined instant; no lock, no ambiguity, no second amendment.

StayVal・LoopVal・JumpValはFormation側に住み、モード3により書かれる（§3.5）。その**読出し側**はCoreのものである: オペランドが**レジスタソース**を選ぶStay・Loop・Jump・Branchは、対応レジスタを到達時に一度サンプルし、以後凍結、同一サイクル衝突はread-before-write（F-F4）。これはちょうど**一つのCore側修正——当該命令群へのレジスタソース・オペランドビット**を要する（依頼台帳INT-R1）。非対称は意図的である: Formationは任意のBGクロックで書いてよく;Coreは一つの定義された瞬間に読む;ロックなし、曖昧さなし、第二の修正なし。

## 5.6 The Condition Lanes / Conditionレーン

The Formation continuously exports the sign and zero properties of Accm as Condition lanes (§2.8). Lane selection is Formation logic and may itself be driven by the Core's timing signals — the machine's output selecting its own sensory input. The Core consults the selected Condition by its own unchanged Branch law; it neither knows nor may be told that the lane carries arithmetic. **This is the only path by which datapath state may influence sequencing**, and it passes through the Core's front door at the Core's own pace.

FormationはAccmの符号とゼロの性質をConditionレーンとして連続的に公開する（§2.8）。レーン選択はFormationロジックであり、それ自体がCoreのタイミング信号で駆動されてよい——機械の出力が自らの感覚入力を選ぶ。Coreは選ばれたConditionを自らの不変のBranch法により参照する;そのレーンが算術を運ぶことをCoreは知らず、知らされてもならない。**これはデータパス状態がシーケンシングに影響しうる唯一の経路**であり、Coreの正門を、Coreのペースで通る。

## 5.7 The Commit Ride / コミットの相乗り

CMT does not own timing machinery; it **rides** the Core's single reservation register. Issued with band = Q, it is registered by the Core like any queued citizen; at Stay-timeup, on the trailing edge, the Core fires and a **commit-fire strobe** crosses the border; the Formation executes the indivisible swap (Ch.4 CMT-1/2) in that clock. Arbitration is split by ownership: **last-write-wins among CMT registrations is the reservation register's own law (Core-side); the double-booking conflict E7 (CMT vs a queued State-Number reservation) is likewise detected by the Core**, whose register it is; the Formation's only duty at the boundary is to swap when told.

CMTはタイミング機構を所有しない;Coreの単一予約レジスタに**相乗り**する。band = Qで発行されると、他のQue市民と同様にCoreに登録され;Stay-timeupの後縁でCoreが発火し、**コミット発火ストローブ**が国境を渡り;Formationはそのクロックで不可分スワップ（第4章CMT-1/2）を遂行する。調停は所有権で分割される: **CMT登録間のlast-write-winsは予約レジスタ自身の法（Core側）;二重予約衝突E7（CMT対QueされたState-Number予約）も同じくCoreが検出する**——そのレジスタの持ち主だからである;境界におけるFormationの唯一の義務は、告げられた時にスワップすることである。

## 5.8 The Error Path / エラー経路

Detection is split by knowledge: **the Core detects what only the band tells** (E1 by C3-F23, per IF-3; E7 by its reservation register), and **the Formation detects what only the sub-op and the datapath tell** — E2 (non-CMT in Q), E3 (CMT in BG), E4 (reserved IDs), E5 (ADRS range), E6 (stack over/underflow), E8 (arithmetic overflow where routed to error). Formation-detected causes cross the border as a single **error strobe** into the Core's Error HALT machinery, with a cause code whose encoding is Arena but whose presence is not: a conforming implementation must make the cause observable (Layer 4's negative tests are keyed to §3.6 row by row). The strobe rides the Core's external error/halt input — anticipated in the ecosystem's requirement lineage (the R6 line) and formalized as INT-R2.

検出は知識で分割される: **Coreは帯域だけが語ることを検出し**（E1はC3-F23により、IF-3の通り;E7は自らの予約レジスタにより）、**Formationはサブオペとデータパスだけが語ることを検出する**——E2（QでのCMT以外）、E3（BGでのCMT）、E4（予約ID）、E5（ADRS範囲）、E6（スタックオーバー/アンダーフロー）、E8（エラーに接続された算術オーバーフロー）。Formation検出の原因は、単一の**エラーストローブ**としてCoreのError HALT機構へ国境を渡る。原因コードのエンコーディングはArenaだが、その存在はArenaではない: 適合実装は原因を観測可能にせねばならない（Layer 4の負系試験は§3.6に行単位で紐づく）。ストローブはCoreの外部エラー/HALT入力に乗る——エコシステムの要求系譜（R6の系統）に予告され、INT-R2として正式化される。

## 5.9 The Interface Request Register / インターフェース依頼台帳

The complete list of what this Formation asks of the Core repository. Each item is a request, not an assumption: each is subject to the Core's own table-before-RTL process, and this Formation's Layer 4 conformance claims are conditional on their disposition.

本FormationがCoreリポジトリに求める事項の完全一覧。各項目は仮定ではなく依頼である: 各々はCore自身の表先行プロセスに服し、本FormationのLayer 4適合主張はその処置を条件とする。

| ID | Request | Consumed by |
|---|---|---|
| INT-R1 | The register-source operand bit on Stay / Loop / Jump / Branch (read side of the value registers, F-F4 latch law) | §5.5 |
| INT-R2 | An external error input into the Error HALT machinery, accepting the Formation error strobe (R6 lineage) | §5.8 |
| INT-R3 | Confirmation that the Global decoder routes mode ≥ 1 to the issue port (delegation) rather than treating nonzero modes as reserved-error — i.e., that F-F1's issue behavior is Core law, not Core amendment | §5.3 |
| INT-R4 | Confirmation that the Stay Start State register and a window-start event are externally observable for the §5.4 latch ("Formation may copy out" made pin-level) | §5.4 |

INT-R3 and INT-R4 are expected to be confirmations of existing Core law rather than amendments; they are listed because a border treaty does not rely on expectations.

INT-R3とINT-R4は修正ではなく既存Core法の確認となる見込みである;国境条約は見込みに依存しないがゆえに列挙される。

## 5.10 Clock, Reset, and the State at Birth / クロック・リセット・誕生時の状態

**IF-4 (one clock, one reset).** The Formation lives in the Core's clock domain. There is no clock-domain crossing anywhere on the border: cycle determinism is a property of a single fabric of time, and this treaty refuses to seam it.
**IF-4（一つのクロック、一つのリセット）。** FormationはCoreのクロックドメインに住む。国境のどこにもクロックドメイン交差はない: サイクル決定論は単一の時間織物の性質であり、本条約はそれに縫い目を入れることを拒む。

**IF-5 (reset state).** At reset: SP = empty (F-F11); **page 0 of PPM and RT is active** — so the consumer's power-on world is deterministic in identity even where contents are not; data contents of PPM, RT, DSTK, Accm, Temp, ADRS are Arena (documented per implementation); the value registers are unwritten, and consuming a value register before its first write is a program defect whose detection is not required of the hardware.
**IF-5（リセット状態）。** リセット時: SP＝空（F-F11）;**PPMとRTのページ0がactive**——ゆえに消費者の電源投入時の世界は、内容が未定でも同一性において決定的である;PPM・RT・DSTK・Accm・Temp・ADRSのデータ内容はArena（実装ごとに文書化）;値レジスタ群は未書込であり、初回書込前の値レジスタ消費はプログラム欠陥であって、その検出はハードウェアに要求されない。

## 5.11 What is NOT in this Chapter / 本章に含まれないもの

Signal names, widths, polarities, and pinouts (Arena; each implementation publishes its own port map against the nine lanes); the Core's internal handling of its side of each lane (the Core repository's law); the attached consumer's interface to the active PPM/RT pages (profile territory — the master defines the pages, not their reader); JTAG and live editing (Core domain, §4.8).

信号名・幅・極性・ピン配置（Arena;各実装は9レーンに対する自らのポートマップを公開する）;各レーンのCore側の内部処理（Coreリポジトリの法）;接続消費者のactive PPM/RTページへのインターフェース（プロファイルの領分——マスターはページを定義するのであって、その読者を定義するのではない）;JTAGと動作中編集（Core管轄、§4.8）。

## 5.12 Summary of Chapter 5 Decisions / 第5章決定事項のまとめ

| ID | Decision | Status |
|---|---|---|
| IF-1 | The Core never waits: no handshake, no stall on the issue port | Proposed |
| IF-2 | Single-clock visible semantics; pipelining Arena only if invisible | Proposed |
| IF-3 | FG silence of the issue port: E1 enforced by Core-native C3-F23 | Proposed |
| IF-4 | One clock domain, one reset; no CDC on the border | Proposed |
| IF-5 | Reset state: SP empty; page 0 active; contents Arena; value registers unwritten | Proposed |
| — | The nine-lane closed border (§5.2); "there is no lane 10" | Proposed |
| — | Error detection split by knowledge (§5.8); cause observability mandatory | Proposed |
| INT-R1..R4 | The Interface Request Register | Open requests to the Core repository |

## End of Chapter 5 — and of the Launch Set / 第5章の末尾——そしてローンチ一式の完結

Five chapters now stand: the philosophy (1), the nouns (2), the verbs (3), the places (4), and the border (5). Together with the worksheet they descend from, they are the whole of what a stranger — human or model — needs to rebuild this Formation and disagree with it precisely. The next words this repository speaks will be in other layers: instruction lists that compute something (Layer 3), and the first measurement, pinned to a version, of what all of this actually weighs (Layer 4).

五つの章がいま立っている: 哲学（1）、名詞（2）、動詞（3）、場所（4）、国境（5）。それらの先祖である作業物と合わせて、これは見知らぬ者——人間であれモデルであれ——が本Formationを再構築し、正確に反論するために必要な全てである。本リポジトリが次に語る言葉は別の層にある: 何かを計算する命令リスト（Layer 3）、そして、これら全てが実際にどれだけの重さなのかの、版に釘付けされた最初の実測（Layer 4）。
