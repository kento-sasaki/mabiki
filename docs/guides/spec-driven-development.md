# Mabiki の Spec-Driven Development（SDD）ガイド

本プロジェクト「Mabiki」における **Spec-Driven Development（仕様駆動開発、以下 SDD）** の進め方を定義する。GitHub Spec Kit の方法論をベースに、**Mabiki の個人開発・Claude Code 連携・GitHub Issue ベースのタスク管理** に合わせて運用する。

**最終更新**: 2026-05-29
**前提読者**: 本リポジトリで実装を進める開発者本人（個人開発）
**位置づけ**: 開発の進め方の標準。**判断の根拠**は [constitution.md](./constitution.md)。

---

## 1. SDD とは何か（Mabiki 文脈で）

SDD は **「仕様（spec）を開発の一次成果物（source of truth）に据える」** 開発手法。Mabiki でこれを採る理由は、プロダクトの核が「デジタル摩擦」という繊細な体験価値だからである。

> Mabiki では「何を作るか」以上に **「なぜその不便さを残すのか」** という意図の一貫性が成否を分ける。仕様＝意図の契約として明文化することで、Claude Code を含むエージェントに渡しても一貫性が崩れない。

Mabiki で SDD を回す上での前提:

- **個人開発** — レビュアーは自分。フェーズゲートはセルフレビューで運用する。
- **Claude Code が一次実装エージェント** — 仕様の曖昧さはそのまま実装の曖昧さになる。
- **GitHub Issue でタスク管理** — 仕様から Issue を切り出し、Issue 単位で実装する。`tasks.md` は持たない。
- **`docs/research/` が哲学的根拠の一次資料** — 摩擦設計は研究資料を根拠に置く。

---

## 2. ワークフロー（3 フェーズ）

Mabiki の SDD は **Spec → Plan → Implement** の3フェーズ。Spec Kit のデフォルト 4 フェーズから `Tasks` フェーズを抜き、その役割を **GitHub Issue** に移している。

```
┌─────────────────────────────────────────────────────────────┐
│ Constitution（docs/guides/constitution.md）                 │
│ 全フェーズを貫く判断基準。逸脱は plan.md で明示・正当化。   │
└─────────────────────────────────────────────────────────────┘
        │
        ▼
  1. Specify     → spec.md     What / Why（要件・ユーザーストーリー・受入基準）
        │
   (Clarify)     → spec.md 更新 [NEEDS CLARIFICATION] の解消
        │
  2. Plan        → plan.md     How（技術アーキテクチャ・データモデル・契約）
        │
  3. Implement   → GitHub Issue + コード
                   spec/plan から `.claude/skills/creating-github-issue` で Issue を切り出し、
                   Issue 単位で実装・PR・マージ
```

### 各フェーズの責務

| フェーズ | 成果物 | 焦点 | やってはいけないこと |
|---|---|---|---|
| **Specify** | `spec.md` | What / Why。ユーザーストーリー、受入基準、非機能要件、エッジケース、摩擦設計の Why | 技術選定・実装詳細（How）を書く |
| **Clarify** | `spec.md` 更新 | `[NEEDS CLARIFICATION]` の解消 | 推測で埋める／哲学的判断を `docs/research/` を見ずに決める |
| **Plan** | `plan.md` | How。アーキテクチャ、データモデル、契約、Constitution Check | 憲法違反の設計を暗黙に通す |
| **Implement** | GitHub Issue + コード | spec/plan から Issue を切り出し、Issue 単位で実装 | 仕様にない投機的機能の追加、Issue にない作業の混入 |

---

## 3. アーティファクトの構造

### 3.1 `spec.md`（要件仕様）

「What / Why」に集中する。**技術的な How は書かない。**

主なセクション:
- **概要（What）** と **背景・狙い（Why）**
- ユーザーストーリーと受入基準（acceptance criteria）
- スコープ（In Scope / **Out of Scope**）— Constitution の「Explicit Non-Goals」を継承
- **摩擦設計** — 操作ごとの「狙い」と `docs/research/` への根拠リンク（Constitution 原則 2, 3）
- 非機能要件
- エッジケース・エラーハンドリング
- **不確実性マーカー** `[NEEDS CLARIFICATION: ...]`（Constitution 原則 8）
- 自己レビュー用チェックリスト

Mabiki 固有の必須項目:
- 各摩擦設計には **Why（哲学的・体験的根拠）** が明記されているか
- 摩擦設計には `docs/research/` の該当節への参照があるか
- Out of Scope に Constitution の禁止リスト（自動繰り越し・一括移動など）が含まれているか

### 3.2 `plan.md`（実装計画）

「How」を扱う。`spec.md` を技術アーキテクチャに翻訳する。

主なセクション:
- 仕様分析サマリ
- **Constitution Check** — `constitution.md` のチェックリストを必ず実施し、逸脱があれば正当化
- 技術スタックと設計判断（その根拠も）
- データモデル
- API / Server Actions の契約
- フロントエンド実装のポイント
- 実装前ゲート（Simplicity / 検証優先など）
- フェーズ内訳（Sprint 単位の俯瞰）

Mabiki 固有の必須項目:
- Constitution Check セクションがあり、各原則について OK / 逸脱が記録されているか
- スマホファーストの設計判断が明示されているか（原則 5）
- 摩擦設計の実装方針が `spec.md` の Why と矛盾していないか

### 3.3 タスク管理（GitHub Issue）

`tasks.md` は **持たない**。代わりに以下のフローで管理する。

1. `spec.md` と `plan.md` が確定したら、必要な作業を **GitHub Issue** として切り出す
2. Issue 切り出しは `.claude/skills/creating-github-issue` を使う
   - `spec.md` の受入基準 → Issue の「完了条件」
   - `plan.md` の技術判断 → Issue の「関連ファイル・コンポーネント」「検証方法」
   - `docs/research/` → Issue の「参考パターン」
3. Issue 1 件 = 独立して PR にできる作業単位（例: 旧 `tasks.md` の T101 相当の粒度）
4. Issue タイトル先頭に対象 feature を `[001-mabiki-mvp]` の形で付ける
5. 並列実行可能・依存関係は Issue 本文に記述（マイルストーンや個別 Issue の参照で表現）

> Issue を切り出す前に **必ず `spec.md` の `[NEEDS CLARIFICATION]` を解消する**（Constitution 原則 8）。

---

## 4. Constitution（憲法）

Mabiki の全 SDD アーティファクトを律する原則は [docs/guides/constitution.md](./constitution.md) に集約されている。

各フェーズで参照する場面:

| フェーズ | Constitution の使い方 |
|---|---|
| Specify | 摩擦設計に Why と研究根拠が書けているか（原則 1〜4, 7） |
| Clarify | `[NEEDS CLARIFICATION]` を残したまま進めない（原則 8） |
| Plan | `plan.md` の Constitution Check セクションで各原則を照合（全原則） |
| Implement | Issue 化前・PR 前のセルフレビューで再度照合（特に原則 6, 7） |

**逸脱は禁止しないが、`plan.md` で明示的に正当化すること。** 暗黙の逸脱は禁止する。

---

## 5. 運用ルール

### 5.1 ディレクトリ規約

```
docs/
  README.md                          # ドキュメント全体の見取り図
  guides/
    spec-driven-development.md       # 本ガイド
    constitution.md                  # Mabiki 憲法（全 SDD の判断基準）
  research/                          # BuJo メソッド調査（仕様の哲学的根拠）
  specs/
    <NNN>-<feature-slug>/            # 機能単位の仕様
      spec.md                        # What / Why
      plan.md                        # How
      （必要に応じて）data-model.md, contracts/, research.md
.claude/
  skills/
    creating-github-issue/           # spec/plan から Issue を切り出す skill
```

- 機能は `001`, `002`, ... と連番でディレクトリを切る。
- ブランチ命名は `NNN-<feature-slug>` を推奨（個人開発のため強制ではない）。

### 5.2 フェーズゲート（セルフレビュー）

個人開発のため形式的な承認プロセスは置かないが、**フェーズの境目で必ず立ち止まる**ことを運用ルールとする。

| 移行 | ゲート |
|---|---|
| Specify → Plan | spec.md の自己レビューチェックリストを通過。`[NEEDS CLARIFICATION]` ゼロ。 |
| Plan → Implement | plan.md の Constitution Check を完了。逸脱は正当化済み。 |
| Issue 切り出し → 実装 | Issue 本文の必須項目（概要・作業内容・完了条件・検証方法）が埋まっている。 |

ゲート通過の記録は **コミットメッセージ** に残す（例: `docs(spec): clarify migration policy and pass spec→plan gate`）。

### 5.3 仕様の書き方の原則

- **What/Why と How を混ぜない**（spec は What、plan は How）。
- **曖昧さは `[NEEDS CLARIFICATION]` で明示**し、推測で埋めない（Constitution 原則 8）。
- **投機的機能を書かない**（YAGNI）。Constitution の Explicit Non-Goals（原則 6）に該当する機能は最初から排除する。
- **摩擦には必ず Why と研究根拠を添える**（原則 2, 3）。
- 仕様変更が起きたら、下流（plan → Issue → code）を **必ず追従させる**。spec.md の変更がコミットに含まれるなら、影響範囲を同一 PR / 直後の PR で更新する。

### 5.4 Claude Code との接続

- **Issue 切り出し**: `.claude/skills/creating-github-issue` を使う。
- **実装**: Issue を Claude Code に渡し、Issue 本文の「作業内容」を順に実行させる。
- **不明点が出たとき**: Claude Code は推測せず `AskUserQuestion` で確認する（`.claude/CLAUDE.md` の方針と Constitution 原則 8 が整合）。
- **PR レビュー**: 個人開発のため自己レビュー。Constitution Check を再度走らせ、逸脱があれば差し戻す。

---

## 6. 既存仕様（リファレンス）

`docs/specs/001-mabiki-mvp/` は、Mabiki 流 SDD の最初の実例。**新しい feature を起こすときの構造リファレンス**として参照する。

- [001-mabiki-mvp/spec.md](../specs/001-mabiki-mvp/spec.md) — What/Why と摩擦設計の書き方の見本（§5 摩擦設計の研究根拠、§7 Clarifications 解消ログを含む）
- [001-mabiki-mvp/plan.md](../specs/001-mabiki-mvp/plan.md) — How と Constitution Check の見本

---

## 7. 参考リンク

- [GitHub Spec Kit](https://github.com/github/spec-kit) — Mabiki の SDD 運用のベースになっている方法論
- [Spec-Driven Development (spec-driven.md)](https://github.com/github/spec-kit/blob/main/spec-driven.md) — SDD の理念
- [Mabiki Constitution](./constitution.md) — 本プロジェクトの判断基準
- [docs/research/](../research/) — BuJo メソッド調査（摩擦設計の哲学的根拠）
