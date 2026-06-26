# bdd-kit

[![CI](https://github.com/Pound79/bdd-kit/actions/workflows/ci.yml/badge.svg)](https://github.com/Pound79/bdd-kit/actions/workflows/ci.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](./LICENSE)
[![npm](https://img.shields.io/npm/v/@pound79/bdd-traceability.svg)](https://www.npmjs.com/package/@pound79/bdd-traceability)
![Node](https://img.shields.io/badge/node-%3E%3D24-brightgreen)

A framework-agnostic BDD behavior-test generation kit. It separates a proven
"spec ↔ impl ↔ feature" traceability-driven BDD flow into three reusable layers
so the same workflow works across repositories (web / Flutter), wired together
by a single `bdd-kit.config.yaml` in the consumer repo.

## What's inside (3 layers)

1. **Methodology (Claude Code plugin)** — the `bdd-*` skills under
   `plugins/bdd-kit/skills/` (bootstrap / new-feature / sync / implement).
   Framework-agnostic and config-driven.
2. **Traceability engine** — `@pound79/bdd-traceability`
   (`packages/traceability/`): a dependency-free, pure-Node tool that detects
   drift via SHA-256 file hashes of spec / impl / feature.
3. **Scaffold templates** — `templates/playwright/` (web) and
   `templates/flutter/` (bdd_widget_test + Patrol), unpacked by the
   `bdd-kit init` CLI (`cli/`).

## Key concepts

- **Spec** — an authoritative `##`-heading section in an in-repo markdown file.
- **Feature** (`.feature`) — a black-box Gherkin description of *observable*
  behavior only (no selectors, URLs, or internal APIs).
- **Drift** — a deterministic, AI-free SHA-256 diff between a blessed baseline
  and the current spec / impl / feature.

See [`CONTEXT.md`](./CONTEXT.md) for the full glossary.

## Prerequisites

- Node.js **>= 24** (see `.nvmrc`).
- For the plugin flow: [Claude Code](https://www.anthropic.com/claude-code).
- Specs are expected to live as in-repo markdown `##` headings. Drift cannot
  track specs that live only in external tools (e.g. Notion / Confluence).

## Quick start (Claude Code plugin)

```text
# 1. Register the marketplace (from GitHub)
/plugin marketplace add Pound79/bdd-kit
# 2. Install the plugin
/plugin install bdd-kit@bdd-kit
# 3. Drop a bdd-kit.config.yaml at your repo root
#    (see templates/playwright/ for a worked example)
# 4. Run the single entry-point skill
/bdd-kit
```

## Traceability CLI (usable independently of the plugin)

```bash
npm i -D @pound79/bdd-traceability
npx bdd-traceability-check --json   # auto-discovers bdd-kit.config.yaml / traceability.yaml
npx bdd-traceability-update         # bless hashes (single link: --link-id <id>)
npx bdd-traceability-list           # registered domains + bootstrap candidates
npx bdd-traceability-stats          # scenario census (automated / @fixme / @skip)
```

> Note: the npm packages are published as part of the first public release.
> Until then, install from a local clone — see the Japanese section below.

## Contributing & license

- Contributions are welcome — see [CONTRIBUTING.md](./CONTRIBUTING.md) and
  [CODE_OF_CONDUCT.md](./CODE_OF_CONDUCT.md).
- Security policy: [SECURITY.md](./SECURITY.md).
- Licensed under the [MIT License](./LICENSE).

---

# 日本語

Framework-agnostic な BDD 振る舞いテスト生成キット。実プロジェクトで実証された
「spec ↔ impl ↔ feature」のトレーサビリティ駆動 BDD フローを、他リポ（web / Flutter）でも
再利用できるよう 3 層に分離したもの。

## 3 層構成

1. **方法論（Claude Code plugin）** — `plugins/bdd-kit/skills/` の `bdd-*` skill 群
   （bootstrap / new-feature / sync / drift / implement）。framework 非依存・config 駆動。
2. **トレーサビリティ・エンジン** — `@pound79/bdd-traceability`(`packages/traceability/`)。
   spec/impl/feature のファイルハッシュで drift を検知する純 Node ツール。依存ゼロ。
3. **足場テンプレート** — `templates/playwright/`（v1, web）、`templates/flutter/`（段階 2,
   bdd_widget_test + Patrol）。`cli/`（`bdd-kit init`）が展開する。

これらを consumer リポ側の 1 枚の `bdd-kit.config.yaml` が束ねる。skill はその config を
実行時に読んでフレームワーク固有値（コマンド・パス・タグ・Gherkin 言語）を解決する。

## 使い方（Claude Code plugin）

```text
# 1. marketplace を登録（GitHub から）
/plugin marketplace add Pound79/bdd-kit
# 2. plugin を導入
/plugin install bdd-kit@bdd-kit
# 3. consumer リポのルートに設定を置く（playwright 例は templates/playwright/ を参照）
#    bdd-kit.config.yaml
# 4. skill を実行（単一入口）
/bdd-kit            # 導入・継続の入口（adapter/モード検出 → 内部ムーブメント駆動）
/bdd-sync <link>    # drift 同期
/bdd-new-feature ... / /bdd-bootstrap ... / /bdd-implement ...   # 内部ムーブメント（単体起動も可）
# drift 検知（read-only）は CLI: npx -y -p @pound79/bdd-traceability bdd-traceability-check
```

トレーサビリティ CLI（plugin とは独立に利用可）:

```bash
# npm 公開後（予定）:
npm i -D @pound79/bdd-traceability
npx bdd-traceability-check --json    # bdd-kit.config.yaml / traceability.yaml を自動探索
npx bdd-traceability-update          # ハッシュを bless（単一リンクは --link-id <id>）
npx bdd-traceability-list            # 登録ドメイン + bootstrap 候補
npx bdd-traceability-stats           # シナリオ census（automated/@fixme/@skip・完了レポート）

# 公開前（現状）はリポを clone してローカルパスで install:
#   git clone https://github.com/Pound79/bdd-kit
#   npm i -D /ABS/PATH/TO/bdd-kit/packages/traceability
# （monorepo のサブパッケージのため `github:Pound79/bdd-kit` 直 install は不可）
```

## ステータス

- [x] リポ骨格
- [x] Phase 0: methodology / adapter-contract / config schema / flutter-readiness
- [x] Phase 1: `@pound79/bdd-traceability`（config 駆動・ビルド配布・191 tests green）
- [x] Phase 2: Claude Code plugin（bdd-* skill + 生成ガイドを config 駆動化・dogfood 済み）
- [x] Phase 3: playwright テンプレート（config 駆動・21 files）+ `bdd-kit init` CLI
  （検証: init → npm install → bddgen が guest/authed の spec 生成・authed-admin は条件未満で除外 → tsc クリーン）
- [x] Phase 4: 実プロジェクトでローカル dogfood（既存リポ無変更・kit エンジンが実 manifest で同値の clean を出力）
- [x] Phase 5: Flutter adapter（flutter_gherkin・日本語 Gherkin）— `templates/flutter/` + `bdd-kit init --adapter flutter`。
  **サンプル Flutter アプリで日本語シナリオ（機能/シナリオ/前提/もし/ならば）が green 実証**（Flutter 3.44.1 / Dart 3.12.1）
