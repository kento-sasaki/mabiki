---
name: creating-github-issue
description: >
  mabiki リポジトリに GitHub Issue を作成する。
  spec/plan に紐づく SDD タスクと、spec/plan を持たない一般タスク
  （開発支援ツールの整備・ドキュメント・リポジトリ整備など）の両方に対応する。
  ユーザーの概要説明と、対応する文脈（docs/specs/<NNN>-<feature>/{spec.md, plan.md}
  または関連するリポジトリ資料）から task.yml テンプレート形式で Issue 本文を生成し、
  `gh issue create` で作成する。
  「Issue を作りたい」「タスクを登録して」「GitHub Issue を作成して」と言われたときに使う。
---

# mabiki の GitHub Issue 作成

## スコープ

- 対象リポジトリ: `kento-sasaki/mabiki`
- 本文テンプレート: `.github/ISSUE_TEMPLATE/task.yml` に準拠（SDD タスク・一般タスク共通の 8 セクション構成）
- ラベルは付与しない（標準ラベルしか存在しないため）
- プロジェクトへの紐付けは Skill では行わない（手動運用）

### Issue の 2 モード

mabiki は Spec-Driven Development (SDD) で開発しているが、SDD の feature に紐づかない作業（開発支援ツールの整備、ドキュメント、リポジトリ整備など）も発生する。本 Skill は以下の 2 モードを扱う。

| モード           | 説明                                                              | 文脈の収集元                                       | タイトル prefix                  |
| ---------------- | ----------------------------------------------------------------- | -------------------------------------------------- | -------------------------------- |
| **SDD タスク**   | `docs/specs/<NNN>-<feature-slug>/` の spec/plan に紐づく実装作業   | `spec.md` / `plan.md` / `docs/research/`           | feature prefix（例 `[001-mabiki-mvp]`） |
| **一般タスク**   | spec/plan を持たない作業（ツール・ドキュメント・整備など）         | 関連するリポジトリ資料（`.claude/`、`docs/guides/` など） | カテゴリタグ（Step 3 参照）       |

モードは Step 1 で概要から **自動判定** し、判定が曖昧なときだけ `AskUserQuestion` で確認する。

## Workflow（TaskCreate で進捗追跡）

この Skill は以下の 5 Step を **TaskCreate で必ず登録** し、各 Step の開始時に `TaskUpdate(status="in_progress")`、完了時に `TaskUpdate(status="completed")` で更新しながら進める。Step を飛ばしたり順序を変えてはならない。

| Step    | 内容                                | TaskCreate に登録するタイトル例           |
| ------- | ----------------------------------- | ----------------------------------------- |
| Step 1  | 概要をヒアリング & モード判定        | "Step 1: 概要ヒアリングとモード判定"      |
| Step 2  | 文脈収集（モード別）                | "Step 2: 文脈収集（モード別）"            |
| Step 3  | Issue 内容を自動生成                | "Step 3: Issue 内容を生成"                |
| Step 4  | 充足性チェック & 追加ヒアリング     | "Step 4: 充足性チェック"                  |
| Step 5  | Issue を作成して事後報告            | "Step 5: Issue 作成と事後報告"            |

### TaskCreate の運用ルール

- **開始時**: Skill が起動したら最初に Step 1〜5 を **まとめて** `TaskCreate` で登録する（5 件を一括投入）
- **状態遷移**: `pending → in_progress`（Step 入口） → `completed`（Step 終了）。次の Step に進む前に必ず前 Step を `completed` にする
- **再生成が発生した場合**: Step 4 で不足が見つかり Step 3 へ戻る際は、Step 3 のステータスを `in_progress` に戻し、ヒアリング完了後に再度 `completed` にする
- **Step を飛ばさない**: 必須項目が満たされていない状態で Step 5 を `in_progress` にしてはならない

## Step 1: 概要をヒアリング & モード判定

`AskUserQuestion` ツールで「何をしたいか」を確認する。ユーザーが既に概要を伝えている場合はスキップしてよい。

聞くべき最低限の項目:

- 何をしたいか（1〜2 文）

### モード判定

概要から **SDD タスク** か **一般タスク** かを自動判定する。

- **SDD タスク** … プロダクト機能の実装・修正で、`docs/specs/<NNN>-<feature>/` の spec/plan に対応づく作業。新規 feature の立ち上げを伴う場合も含む。
- **一般タスク** … プロダクト機能の spec/plan に紐づかない作業。例:
  - Claude Code の skill / subagent / hook / workflow の作成・修正（例: 「ドキュメントをレビューするための subagent を作成する」）
  - ドキュメントの作成・整備・レビュー
  - リポジトリ設定・CI・依存関係などの整備

判定の目安:

- 概要が既存 feature（`docs/specs/` 配下）の機能要件・受入基準に対応づけられる → **SDD タスク**
- 概要がツール整備・ドキュメント・リポジトリ運用など、どの feature にも対応づかない → **一般タスク**

判定が曖昧で、どちらのモードかでその後の文脈収集・タイトルが変わる場合のみ `AskUserQuestion` で確認する。明らかにどちらかに振り分けられる場合は確認しない。

## Step 2: 文脈収集（モード別）

判定したモードに応じて文脈を収集する。

### 2A. SDD タスクの場合

#### 2A.1 対象 feature の確定

`ls docs/specs/` で既存 feature の一覧を取得し、Step 1 の概要と最も整合する feature を選定する。既存に該当がなく新規 feature が必要な場合、または曖昧な場合は `AskUserQuestion` で確認する。

#### 2A.2 docs/ の読み込み

確定した feature について、以下を読み込んで Issue 各セクションへ反映する。

| ファイル                                          | 反映先セクション                                                          |
| ------------------------------------------------- | ------------------------------------------------------------------------- |
| `docs/specs/<NNN>-<feature>/spec.md`              | 概要、背景・目的、完了条件（受入基準と整合させる）、作業内容（ユーザーストーリーの粒度から導出）|
| `docs/specs/<NNN>-<feature>/plan.md`              | 関連ファイル・コンポーネント、検証方法、参考パターン（技術アーキテクチャ・データモデル・契約から導出）|
| `docs/guides/constitution.md`                     | 参考パターン（Mabiki の判断基準。Issue が原則 6「Explicit Non-Goals」に抵触していないか確認）|
| `docs/guides/spec-driven-development.md`          | 参考パターン（SDD のフェーズや品質基準）                                  |

該当する記述がない場合は無理に紐付けず、`_No response_` または該当項目を空にする。SDD 上の根拠資料が必要な場合は `docs/research/` を補助的に参照してよい。

> Issue を切り出す前に **必ず `spec.md` の `[NEEDS CLARIFICATION]` を解消する**（Constitution 原則 8）。

#### 2A.3 Issue 粒度の判定

mabiki では **「独立して PR にできる作業単位」** を 1 Issue とする（旧 `tasks.md` の T101 相当の粒度）。`plan.md` の §「フェーズ内訳」や Sprint 区切り、`spec.md` のユーザーストーリー（US-N）を参考に、過大／過小な粒度を避ける。

- 過大（1 Issue が大きすぎる）の典型: 「Sprint 1 を全部やる」「Server Actions を全部実装」
- 過小（1 Issue が小さすぎる）の典型: 「import 文を追加」「変数名を変更」

迷ったときは `AskUserQuestion` で粒度を確認する。

### 2B. 一般タスクの場合

#### 2B.1 関連資料の特定

spec/plan は存在しないため、概要に関連するリポジトリ内の資料を読み込んで文脈を補う。対象の例:

| タスク種別                                  | 主に参照する資料                                              |
| ------------------------------------------- | ------------------------------------------------------------- |
| Claude Code 関連（skill / subagent など）   | `.claude/skills/`、`.claude/agents/`、既存の類似定義          |
| ドキュメント関連                            | 対象ドキュメント本体、`docs/guides/`                          |
| リポジトリ整備                              | 対象の設定ファイル（`.github/`、`package.json` など）         |

`docs/guides/constitution.md` は判断基準として参照してよい（Issue が原則に抵触しないか確認）。**spec/plan への無理な紐付けは行わない。** 該当する関連資料がなければ、Step 1 の概要のみを根拠に進める。

#### 2B.2 Issue 粒度の判定

一般タスクでも **「独立して PR にできる作業単位」** を 1 Issue とする。

- 過大の典型: 「複数の独立した成果物（複数 subagent・複数ドキュメント）を 1 Issue にまとめる」
- 過小の典型: 「1 行だけ修正」「タイポ修正のみ」

迷ったときは `AskUserQuestion` で粒度を確認する。

## Step 3: Issue 内容を自動生成

Step 1 の概要 + Step 2 の文脈から以下を生成する。

- **タイトル**: 簡潔に（50 文字以内目安）。モードに応じて prefix を付ける。
  - SDD タスク: 対象 feature の prefix（例 `[001-mabiki-mvp]`）
  - 一般タスク: カテゴリタグ（下表から最も合うものを 1 つ選ぶ）
- **ラベル**: 付与しない
- **本文**: 後述の task.yml 準拠テンプレートで全セクションを生成（両モード共通の 8 セクション）

### 一般タスクのカテゴリタグ

| タグ       | 用途                                                              |
| ---------- | ----------------------------------------------------------------- |
| `[meta]`   | Claude Code の skill / subagent / hook / workflow など開発支援ツール |
| `[docs]`   | ドキュメントの作成・整備・レビュー                                |
| `[chore]`  | リポジトリ設定・依存関係・雑務的整備                              |
| `[infra]`  | CI/CD・ビルド・実行環境                                            |
| `[test]`   | プロダクトコードに紐づかないテスト基盤の整備                      |

いずれにも当てはまらない場合は最も近いものを選ぶ（判断に迷えば `[chore]`）。

各セクションは task.yml の `placeholder` の粒度を目安にする。
推測できない**推奨項目**は `_No response_` とする（必須項目は Step 4 でヒアリングして埋めるため `_No response_` を使わない）。

## Step 4: 情報の充足性チェック & 追加ヒアリング

最終確認ステップは存在しない。よってこの段階で「Issue として成立する情報」を必ず揃える。

### 必須項目

以下が埋まっていない場合は **必ず** `AskUserQuestion` でヒアリングする。`_No response_` は使わない。
（`summary` と `tasks` は task.yml で `required: true`。他は本 Skill で独自に必須化している）

- **概要 (`summary`)**: タスクで何をするかが 1〜2 文で明確
- **作業内容 (`tasks`)**: チェックボックス付きの具体タスクが最低 1 件
- **背景・目的 (`background`)**: なぜ必要か。SDD タスクは spec.md / plan.md の該当箇所から、一般タスクは Step 1 の概要や関連資料から要約・参照できる
- **完了条件 (`acceptance_criteria`)**: 客観的に判定可能。SDD タスクは spec.md の受入基準と矛盾しないこと
- **検証方法 (`verification`)**: 動作・成果物を確認する具体的な手順。コードを伴わないタスク（ドキュメント・ツール定義など）では、成果物のレビュー観点や確認手順を記載する

### 推奨項目（可能な限り埋める）

以下は `_No response_` を許容するが、**docs/ や関連資料、Step 1 の概要から推測できる場合は積極的に埋める**。
推測の根拠が薄い・複数解釈が可能で重要度が高い場合は `AskUserQuestion` で確認する。
明らかに不要・無関係な場合のみ `_No response_` を使う。

- 関連ファイル・コンポーネント
- 参考パターン
- 参考情報

### 進行ルール

- 必須項目が満たされていない、または推奨項目で重要な不明点が残っている場合 → ヒアリングして Step 3 に戻り再生成（Step 3 を `in_progress` に戻す）
- すべて満たされている場合 → そのまま Step 5 へ進む（ユーザーへの最終確認・承認は行わない）

**充足性チェックを通らない情報で `gh issue create` を実行しない。**

## Step 5: Issue を作成して事後報告

```bash
gh issue create \
  --repo kento-sasaki/mabiki \
  --title "{title}" \
  --body "$(cat <<'EOF'
{body}
EOF
)"
```

実行後、以下の最小構成でユーザーに事後報告する。

- 作成したタイトル
- Issue URL
- 修正方法の案内: 「内容を修正したい場合は `gh issue edit <number> --repo kento-sasaki/mabiki` または GitHub UI から編集してください」

報告後、Step 5 のタスクを `completed` にして Skill を終了する。

## Issue 本文テンプレート（task.yml 準拠）

以下の形式を厳密に守る。セクションの順序・見出しは変更しない（SDD タスク・一般タスク共通）。

```markdown
### 📝 概要

{summary}

### 🎯 背景・目的

{background}

### 📁 関連ファイル・コンポーネント

{related_files}

### ✅ 作業内容

- [ ] {task1}
- [ ] {task2}

### 🏁 完了条件

{acceptance_criteria}

### 🔍 検証方法

{verification}

### 🧩 参考パターン

{reference_patterns}

### 🔗 参考情報

{references}
```

各セクションのガイドライン：

- **概要**: このタスクで何をするかを 1〜2 文で簡潔に。task.yml で `required`。
- **背景・目的**: なぜ必要か。SDD タスクは `docs/specs/<NNN>-<feature>/spec.md` のユーザーストーリー・受入基準、`docs/research/` の根拠資料を要約・参照し、SDD の「Why」を厳密に書く。一般タスクは Step 1 の概要や関連資料（既存ツール・ドキュメントの不足など）から「Why」を書く。
- **関連ファイル・コンポーネント**: 変更・参照が予想されるファイル・コンポーネントを箇条書き。SDD タスクは `plan.md` の技術アーキテクチャから、一般タスクは関連するリポジトリ資料（`.claude/`、設定ファイルなど）から拾う。未作成のファイルを挙げる場合は「（新規作成）」と明示する。
- **作業内容**: チェックボックス付きの具体タスク。SDD タスクは `plan.md` の §「フェーズ内訳」や Sprint 区切り、`spec.md` のユーザーストーリー（US-N）を参照ラベルとして併記してよい（例: `(US-3 / Sprint 1)`）。task.yml で `required`。
- **完了条件**: 「完了」とみなされる客観的条件。SDD タスクは **spec.md の受入基準（acceptance criteria）と矛盾しないこと**。
- **検証方法**: 動作・成果物確認の手順を必ず記載する（Step 4 で必須項目）。整備状況・タスク種別に応じて使い分ける。
  - 検証ゲートが整備済み: 該当コマンド（型チェック・テスト・ビルドなど）を記載
  - 未整備の段階: 手動操作手順、`quickstart.md` がある場合はその手順を記載
  - コードを伴わないタスク（ドキュメント・ツール定義など）: 成果物のレビュー観点や確認手順を記載
- **参考パターン**: 既存の実装パターンや SDD アーティファクトへの参照（例: `docs/guides/spec-driven-development.md`、`docs/research/` の該当章）。一般タスクでは類似の既存定義（例: `.claude/skills/` の他 skill、`.claude/agents/` の既存 subagent）を参照する。
- **参考情報**: 関連 Issue / PR / 外部ドキュメントへのリンク。

推測できない**推奨項目**は `_No response_` とする（必須項目は Step 4 でヒアリングして埋めるため `_No response_` を使わない）。
