# Contributing / 貢献について

This is an Open Prompt repository; the contribution model follows the four-layer structure and the Core-Formation separation pattern, in the form established by [PTSG-Core's CONTRIBUTING](https://github.com/dsohnaka/PTSG-Core/blob/main/CONTRIBUTING.md). The rules below are the deltas specific to this Formation repository. / これはOpen Promptリポジトリであり、貢献モデルは[PTSG-CoreのCONTRIBUTING](https://github.com/dsohnaka/PTSG-Core/blob/main/CONTRIBUTING.md)で確立された形の四層構造とCore-Formation分離パターンに従う。以下は本Formationリポジトリ固有の差分である。

### Layer 1 (Architecture) — Table-before-RTL / 表がRTLに先行する

No instruction exists until its full Command × Phase row — all three bands, all columns — is written and ratified by the architect. Proposals for new sub-opcodes must arrive **as candidate table rows**, not as RTL. Clarifications, translations, and additional reasoning are welcome as issues/discussions. / いかなる命令も、そのCommand × Phase行（3帯域・全列）が書かれ設計者に裁定されるまで存在しない。新サブオペコードの提案はRTLではなく**候補行として**提出のこと。明確化・翻訳・追加の論証はissue/discussionで歓迎。

### Layer 2 (Reasoning Traces) — Welcome / 歓迎

Traces of your own design dialogues with this repository (any LLM, or none) are welcome under `02_Reasoning_Traces/contributed/[your-name]/`, as Markdown + JSON pairs, CC0 by submission. / 本リポジトリとのあなた自身の設計対話の軌跡（LLMは問わず）を`02_Reasoning_Traces/contributed/[あなたの名前]/`にMarkdown＋JSONペアで歓迎、提出によりCC0。

### Layer 3 (Sample Implementations) — Publish your own / 自身のリポジトリへ

If you regenerate this Formation from the architecture, the recommended path is **your own Open Prompt repository**, linked from a discussion thread — not a merge here. / 本アーキテクチャからFormationを再生成した場合、推奨経路はここへのマージではなく、**あなた自身のOpen Promptリポジトリ**の公開と議論スレッドからのリンクである。

### Layer 4 (Verification Evidence) — Welcome; negative results are first-class / 歓迎;負の結果は一級

Conformance evidence against this repository's reference implementation is welcome under `04_Verification_Evidence/contributed/[your-name]/`: VCD captures with mandatory `observation.md` verdicts, commit hash, tool versions, and (for silicon) board and bitstream checksum. A FAIL with a good observation.md is more valuable than an undocumented PASS. / 本リポジトリのリファレンス実装への適合エビデンスを歓迎: VCD＋必須`observation.md`判決、コミットハッシュ、ツール版、（実機なら）ボードとビットストリームチェックサム。良いobservation.mdを伴うFAILは文書化されないPASSより価値がある。

### Resource figures / リソース数値

Per the *Measured, not promised* rule (see README), pull requests adding forward-looking resource figures to any document will be declined. Measured figures belong in Layer 4, pinned to revision, device, toolchain, and configuration. Quoting **published, version-pinned measured figures from other repositories** (e.g., PTSG-Core's own trace) is permitted — such quotations are historical record, not forward-looking claims (ruled 2026-07-12). / *約束せず、測って刻む*規則により、前方宣言的リソース数値を文書に加えるPRは却下される。実測値はLayer 4へ、改訂・デバイス・ツールチェーン・設定に釘付けして。**他リポジトリの公開済み・版釘付けの実測値の引用**（例: PTSG-Core自身のトレース）は可——それは歴史的記録であって前方宣言ではない（2026-07-12裁定）。
