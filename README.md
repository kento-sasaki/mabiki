# Mabiki

手書きバレットジャーナル（BuJo）が持つ **「手で書くことによる思考の整理と意図性（Intentionality）」** をデジタルで再解釈した、スマホファーストのアプリ。

効率化・自動化をあえて排除し、ユーザーが自分の意志でタスクをコントロールする **「デジタル摩擦」** をコア価値とする。一般的な Todo アプリが自動繰り越しやワンタップ移動で消し去る「本当に今日やるべきか？」という問い直しを、**手で打ち直す移行** と **演出付きの削除** によって体験に残すことを狙う。

- **開発手法**: [Spec-Driven Development（SDD）](./docs/guides/spec-driven-development.md) — 仕様を一次成果物に据え、What/Why → How → タスク → 実装の順で進める。
- **現状**: 仕様策定フェーズ（MVP 仕様は Draft、実装前）。

## ドキュメント

すべてのドキュメントは [`docs/`](./docs/) 配下にある。入口は [docs/README.md](./docs/README.md)。

| パス | 内容 |
|---|---|
| [docs/README.md](./docs/README.md) | ドキュメント全体の見取り図・はじめに読むもの |
| [docs/guides/spec-driven-development.md](./docs/guides/spec-driven-development.md) | SDD の進め方（ワークフロー・アーティファクト構造・運用ルール） |
| [docs/research/](./docs/research/) | BuJo メソッドの調査資料（仕様の根拠）。入口は [research/README.md](./docs/research/README.md) |
| [docs/specs/001-mabiki-mvp/](./docs/specs/001-mabiki-mvp/) | MVP（コア体験「捨てる」「移行する」）の仕様 |

### MVP 仕様アーティファクト

| ファイル | 焦点 |
|---|---|
| [spec.md](./docs/specs/001-mabiki-mvp/spec.md) | What / Why（要件・ユーザーストーリー・受入基準） |
| [plan.md](./docs/specs/001-mabiki-mvp/plan.md) | How（技術スタック・データモデル・設計判断） |
| [tasks.md](./docs/specs/001-mabiki-mvp/tasks.md) | 実行可能なタスク分解 |

## はじめに読むもの

1. このページ（全体像）
2. [docs/README.md](./docs/README.md)（ドキュメントの歩き方）
3. [SDD ガイド](./docs/guides/spec-driven-development.md)（開発の進め方）
4. [MVP の spec.md](./docs/specs/001-mabiki-mvp/spec.md)（最初の機能仕様）
