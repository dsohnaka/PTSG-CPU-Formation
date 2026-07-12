# PTSG-CPU-Formation — Canonical Project Abstract / 正典アブストラクト (2026-07-12)
*Single source for README / Hackaday summary / docs top page / top HTML. Edit here first, radiate outward.*
*README・Hackaday要約・docsトップ・トップHTMLの単一情報源。まずここを直し、外へ放射する。*

**EN (short, ~65 words):** PTSG-CPU-Formation is the first data-processing formation on the frozen PTSG-Core: a minimal instruction-driven datapath — accumulator, Horner-step MAC, explicit data stack, packet-parameter memory with atomic boundary commit — living entirely inside the Core's window discipline. Computation cannot disturb timing by construction: arithmetic is background-only, commits land jitter-free on packet boundaries, interrupts are quantized to the deterministic grid, and the foreground timing chart remains the program text.

**EN (one line):** A deterministic data machine built on a clock that cannot be argued with.

**JA (短):** PTSG-CPU-Formationは、凍結されたPTSG-Core上の最初のデータ処理Formation——アキュムレータ、HornerステップMAC、明示データスタック、原子的境界コミット付きパケットパラメータメモリからなる最小の命令駆動データパスを、Coreのウィンドウ規律の完全な内側に置く。構造上、演算はタイミングを乱し得ない: 算術は背景専用、コミットはパケット境界にジッターフリーで着地、割り込みは決定論的グリッドに量子化され、前景タイミングチャートはプログラムテキストであり続ける。

**JA (一行):** 議論の余地なきクロックの上に建てられた、決定論的データマシン。

**Pillars (fixed vocabulary / 語彙を固定):**
1. Delegation boundary: mode 0 is Core-owned and frozen; modes ≥ 1 are Formation-owned — the Core-Formation separation applied recursively to the ISA itself / 委譲境界: モード0はCore所有・凍結、モード≥1はFormation所有——Core-Formation分離のISA自身への再帰適用
2. BG computes, Q commits; the FG-Formation exclusion — the timing chart remains the program text / BGが計算しQがコミットする;FG-Formation排除——タイミングチャートはプログラムテキストのまま
3. Two stacks, two owners: control stack to the Core, data stack to the datapath, SP hidden; von Neumann-ness admitted through a turnstile, LIFO-first / 二つのスタック、二人の所有者: SPは隠蔽;von Neumann性は回転扉からLIFO形態で入場
4. The read-only quartet SN / SSS / K / I — now, here, when, which: time itself as an operand; the condition stays outside (flags → Condition lanes, the front door) / 読出し専用カルテット——今・ここ・いつ・どれ: 時間そのものがオペランド;条件は外に（フラグ→Conditionレーンの正門）
5. Measured, not promised: resource figures exist only as Layer-4 measurements pinned to a named revision, device, toolchain, and configuration — never as forward-looking claims / 約束せず、測って刻む: リソース数値は改訂・デバイス・ツールチェーン・設定に釘付けされたLayer 4実測としてのみ存在する
6. Master and profiles: this ISA is the master; application formations are made by subtraction — PTSG-WPMS-Formation is the first / マスターとプロファイル: 本ISAがマスター、応用Formationは引き算で作られ、その最初がPTSG-WPMS-Formation
