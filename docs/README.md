# Mabiki ドキュメント

「Mabiki（間引き）」は、手書きバレットジャーナル（BuJo）が持つ **「手で書くことによる思考の整理と意図性（Intentionality）」** をデジタルで再解釈した、スマホファーストのアプリである。効率化・自動化をあえて排除し、ユーザーが自分の意志でタスクをコントロールする **「デジタル摩擦」** をコア価値とする。

本リポジトリは [Spec-Driven Development（SDD）](./guides/spec-driven-development.md) で実装を進める。

---

## ディレクトリ構成

| パス | 内容 |
|---|---|
| [`docs/guides/`](./guides/) | 開発の進め方ガイド |
| [`docs/research/`](./research/) | BuJo メソッドの調査資料（仕様の根拠） |
| [`docs/specs/`](./specs/) | 機能単位の仕様（SDD アーティファクト） |

### `docs/guides/`

- [spec-driven-development.md](./guides/spec-driven-development.md) — **SDD の進め方**。Spec Kit ベースのワークフロー、アーティファクト構造、運用ルール。

### `docs/research/`

Ryder Carroll の BuJo メソッドを、実装判断に耐える深さで体系化した調査ドキュメント群。仕様（spec）を書く際の **根拠資料** として参照する。入口は [research/README.md](./research/README.md)。

### `docs/specs/`

機能ごとに `<NNN>-<feature-slug>/` で管理する SDD の仕様アーティファクト。

- [`docs/specs/001-mabiki-mvp/`](./specs/001-mabiki-mvp/) — MVP のコア体験（「捨てる」「移行する」摩擦）の仕様。

---

## はじめに読むもの

1. このページ（全体像）
2. [SDD ガイド](./guides/spec-driven-development.md)（開発の進め方）
3. [docs/specs/001-mabiki-mvp/spec.md](./specs/001-mabiki-mvp/spec.md)（最初の機能仕様）
4. 背景を深掘りするなら [research/](./research/)（特に `02-philosophy.md`, `10-digital-implementation-considerations.md`）
