# Spec-Driven Development (SDD) ガイド

本プロジェクト「Mabiki」における **Spec-Driven Development（仕様駆動開発、以下 SDD）** の進め方を定義する。本ガイドは [GitHub Spec Kit](https://github.com/github/spec-kit) の方法論をベースに、Claude Code を実装エージェントとして使う前提でまとめている。

**最終更新**: 2026-05-25
**前提読者**: 本リポジトリで実装を進めるエンジニア／PdM

---

## 1. SDD とは何か

SDD は **「仕様（spec）を開発の一次成果物（source of truth）に据える」** 開発手法である。従来の「コードが主、ドキュメントが後追い」という関係を逆転させ、**自然言語で書かれた仕様から実装計画とコードを生成する**。

> コードはもはや「真実」ではなく、仕様という意図を最終的に表現する「ラストワンマイル」になる。

### なぜ SDD なのか（AI コーディング時代の必然）

- **Vibe coding の対極**: その場の思いつきでエージェントに丸投げすると、文脈が散らばり一貫性が崩れる。SDD は構造化された仕様をエージェントに渡すことで、出力品質と再現性を担保する。
- **意図の明文化**: 「何を・なぜ作るか（What/Why）」を先に固め、「どう作るか（How）」と分離する。これにより手戻りが減る。
- **変更耐性**: 要件を変えると計画・タスク・コードへ波及的に再生成できる。要件変更を「破壊的なやり直し」ではなく「通常のワークフロー」として扱える。

### 本プロジェクトでの位置づけ

Mabiki は「デジタル摩擦」という繊細な体験価値を核とするため、**何を作るか以上に「なぜその不便さを残すのか」という意図の共有が重要**になる。SDD の「仕様＝意図の契約」という性質は、この種のプロダクトと特に相性が良い。

---

## 2. コアワークフロー（4フェーズ）

Spec Kit のワークフローは **Spec → Plan → Tasks → Implement** の4フェーズ。各フェーズが Markdown アーティファクトを生成し、次フェーズの構造化された入力になる。

```
Constitution（憲法・原則） ── 全フェーズを貫く制約
        │
        ▼
  1. Specify   → spec.md    What / Why（要件・ユーザーストーリー・受入基準）
        │
   (Clarify)   → 曖昧点を構造化された質問で解消
        │
  2. Plan      → plan.md    How（技術アーキテクチャ・データモデル・契約）
        │
  3. Tasks     → tasks.md   実行可能なタスク分解（依存関係・並列マーク）
        │
   (Analyze)   → アーティファクト間の整合性検証
        │
  4. Implement → 実装（タスクを順に実行）
```

### 各フェーズの責務

| フェーズ | 成果物 | 焦点 | やってはいけないこと |
|---|---|---|---|
| **Specify** | `spec.md` | What / Why。ユーザーストーリー、受入基準、非機能要件、エッジケース | 技術選定・実装詳細（How）を書く |
| **Clarify** | `spec.md` 更新 | 曖昧点（`[NEEDS CLARIFICATION]`）の解消 | 勝手に推測で埋める |
| **Plan** | `plan.md` 他 | How。アーキテクチャ、データモデル、API契約、テスト戦略 | 憲法違反の設計を通す |
| **Tasks** | `tasks.md` | 依存順に並んだ実行可能タスク。並列可能なものは `[P]` | 粒度の粗すぎるタスク |
| **Analyze** | レポート | アーティファクト間の矛盾・抜け・曖昧さの検出 | 検証なしで実装へ進む |
| **Implement** | コード | タスクを順に実行 | 仕様にない投機的機能の追加 |

---

## 3. アーティファクトの構造

### 3.1 `spec.md`（要件仕様）

「What / Why」に集中する。**技術的な How は書かない。**

主なセクション:
- 機能の概要と背景（rationale）
- ユーザーストーリーと受入基準（acceptance criteria）
- 非機能要件
- エッジケース・エラーハンドリング
- **不確実性マーカー** `[NEEDS CLARIFICATION: ...]` — 曖昧な点は推測で埋めず明示する
- 要件の完全性チェックリスト（自己レビュー用ゲート）

### 3.2 `plan.md`（実装計画）

「How」を扱う。spec.md を技術アーキテクチャに翻訳する。

主なセクション:
- 仕様分析サマリ
- **憲法への準拠検証**（Constitution Check）
- 技術アーキテクチャと設計判断（その根拠も）
- 付随ドキュメント生成: `data-model.md`, `contracts/`, `research.md`, `quickstart.md`
- 実装前ゲート（Simplicity / Anti-Abstraction / Integration-First）
- フェーズごとの内訳（前提条件と成果物）
- 複雑度トラッキング（過剰設計の正当化記録）

### 3.3 `tasks.md`（タスク分解）

plan.md（および data-model.md / contracts/ / research.md）から実行可能なタスクを導出する。

特徴:
- 契約・エンティティ・シナリオからタスクを生成
- 依存関係に基づく順序付け
- 独立して並列実行できるタスクには `[P]` マーク
- タスクごとの実装指示

---

## 4. Constitution（憲法）

`memory/constitution.md` に置く、**全生成コードを律する原則**。Spec Kit のデフォルトは以下のような条項を含む（プロジェクトに合わせて取捨選択・改変する）:

- **Library-First**: 機能はまず独立した再利用可能なライブラリとして始める
- **CLI Interface**: 機能はテキスト/JSON を入出力する CLI として公開する
- **Test-First**: 実装コードを書く前に「テストを書く → ユーザー承認 → テストが失敗することを確認」を必須とする
- **Simplicity / Anti-Abstraction**: 初期プロジェクト数を最小に保ち、フレームワークを直接使う（過剰なラッパーを避ける）
- **Integration-First Testing**: モックより実DB・実サービスを優先し、実装前に契約テストを課す

> **Mabiki での注意**: 上記はあくまで Spec Kit のデフォルト原則であり、Next.js/フロントエンド中心の本プロジェクトには「Library-First」「CLI Interface」がそのまま当てはまらない。憲法は**プロジェクト固有に書き換えるべき**もの。Mabiki では「デジタル摩擦を効率化で薄めない」「自動化を安易に足さない」といった**プロダクト原則**を憲法に据えるのが有効。

---

## 5. Spec Kit のセットアップ（任意）

ガイドの方法論だけでも運用できるが、スラッシュコマンドによる自動化を使いたい場合は Spec Kit CLI を導入する。

### 前提

- [uv](https://docs.astral.sh/uv/)（Python パッケージマネージャ）
- Python 3.11+
- Git
- Claude Code

### 初期化

```bash
# カレントディレクトリに Claude Code 連携で初期化
uvx --from git+https://github.com/github/spec-kit.git specify init . --integration claude
```

- 旧 `--ai claude` フラグは非推奨（警告が出て `--integration` にマップされる）。
- Claude Code 連携は **スキルベース**で、関連スキルが `.claude/skills` にインストールされる。
- 生成されるディレクトリ: `.specify/`（設定・コマンド）、`specs/`（仕様アーティファクト）、`memory/`（憲法などのコンテキスト）。

> **導入は任意**。本リポジトリでは既に `specs/001-mabiki-mvp/` を手動の SDD アーティファクトとして用意している。CLI 導入時は既存構成と整合させること。

### スラッシュコマンド一覧

| コマンド | 目的 |
|---|---|
| `/speckit.constitution` | プロジェクトの統治原則・開発ガイドラインを確立 |
| `/speckit.specify` | 要件・ユーザーストーリーを定義（技術ではなく What に集中） |
| `/speckit.clarify` | 構造化された質問で過小仕様の領域を解消 |
| `/speckit.plan` | 採用技術スタックを含む技術実装戦略を作成 |
| `/speckit.tasks` | 計画から実行可能で順序付けされたタスク分解を生成 |
| `/speckit.analyze` | 実装前にアーティファクト間の整合性を検証 |
| `/speckit.checklist` | カスタム品質検証チェックリストを構築 |
| `/speckit.implement` | 全タスクを実行して機能を構築 |

### 推奨実行順

1. `specify init`（初期化）
2. `/speckit.constitution`
3. `/speckit.specify`
4. `/speckit.clarify`
5. `/speckit.plan`
6. `/speckit.tasks`
7. `/speckit.analyze`（任意だが推奨）
8. `/speckit.implement`

---

## 6. 本リポジトリでの運用ルール

CLI を導入してもしなくても、以下を共通ルールとする。

### ディレクトリ規約

```
docs/
  guides/spec-driven-development.md   # 本ガイド（SDD の進め方）
  research/                           # BuJo メソッド調査（仕様の根拠資料）
  specs/
    <NNN>-<feature-slug>/             # 機能単位の仕様
      spec.md                         # What / Why
      plan.md                         # How
      tasks.md                        # タスク分解
      （必要に応じて）data-model.md, contracts/, research.md
memory/
  constitution.md                     # プロジェクト憲法（導入時）
```

- 機能は `001`, `002`, ... と連番でディレクトリを切る。
- 各機能はブランチ `NNN-<feature-slug>` で進める（Spec Kit 慣習）。

> **Spec Kit CLI を導入する場合の注意**: `specify init` はリポジトリ直下に `specs/` を生成する。本プロジェクトは仕様を `docs/specs/` に置く方針のため、CLI 導入時は生成先を `docs/specs/` に揃えるか、生成された `specs/` の内容を `docs/specs/` へ移すこと。

### フェーズゲート（人間のレビューポイント）

実装中ではなく**フェーズの境目でレビューする**のが SDD の肝。承認のオーバーヘッドとコンテキストスイッチを最小化できる。

1. `spec.md` を承認してから `plan.md` へ
2. `plan.md` を承認してから `tasks.md` へ
3. `tasks.md` を承認してから実装へ

### 仕様の書き方の原則

- **What/Why と How を混ぜない**（spec は What、plan は How）。
- **曖昧さは `[NEEDS CLARIFICATION]` で明示**し、推測で埋めない（→ 本プロジェクトの CLAUDE.md「推測で進めない。確認してから進める」と一致）。
- **投機的機能を書かない**（YAGNI）。
- 仕様変更が起きたら、下流（plan → tasks → code）を再生成して整合を取る。

---

## 7. 参考リンク

- [GitHub Spec Kit (リポジトリ)](https://github.com/github/spec-kit)
- [Spec Kit ドキュメント](https://github.github.com/spec-kit/)
- [SDD 方法論 (spec-driven.md)](https://github.com/github/spec-kit/blob/main/spec-driven.md)
- [Diving Into Spec-Driven Development With GitHub Spec Kit (Microsoft for Developers)](https://developer.microsoft.com/blog/spec-driven-development-spec-kit)
- [Implement Spec-Driven Development using the GitHub Spec Kit (Microsoft Learn)](https://learn.microsoft.com/en-us/training/modules/spec-driven-development-github-spec-kit-enterprise-developers/)
