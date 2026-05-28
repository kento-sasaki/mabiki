---
name: creating-github-issue
description: >
  mabiki リポジトリに GitHub Issue を作成する。
  ユーザーの概要説明と docs/specs/<NNN>-<feature>/{spec.md, plan.md} の文脈から
  task.yml テンプレート形式で Issue 本文を自動生成し、`gh issue create` で作成する。
  mabiki は tasks.md を持たず、spec.md / plan.md から直接 Issue を切り出して
  タスクを管理する運用（→ docs/guides/spec-driven-development.md §3.3）。
  「Issue を作りたい」「タスクを登録して」「GitHub Issue を作成して」と言われたときに使う。
---

# mabiki の GitHub Issue 作成

## スコープ

- 対象リポジトリ: `kento-sasaki/mabiki`
- 本文テンプレート: `.github/ISSUE_TEMPLATE/task.yml` に準拠
- ラベルは付与しない（標準ラベルしか存在しないため）
- プロジェクトへの紐付けは Skill では行わない（手動運用）
- mabiki は Spec-Driven Development (SDD) で開発しているため、Issue は必ず対象 feature（`docs/specs/<NNN>-<feature-slug>/`）に紐付けて文脈を収集する

## Workflow（TaskCreate で進捗追跡）

この Skill は以下の 5 Step を **TaskCreate で必ず登録** し、各 Step の開始時に `TaskUpdate(status="in_progress")`、完了時に `TaskUpdate(status="completed")` で更新しながら進める。Step を飛ばしたり順序を変えてはならない。

| Step    | 内容                                | TaskCreate に登録するタイトル例           |
| ------- | ----------------------------------- | ----------------------------------------- |
| Step 1  | 概要をヒアリング                    | "Step 1: 概要をヒアリング"                |
| Step 2  | 対象 feature と docs/ から文脈収集  | "Step 2: 対象 feature と docs 文脈収集"   |
| Step 3  | Issue 内容を自動生成                | "Step 3: Issue 内容を生成"                |
| Step 4  | 充足性チェック & 追加ヒアリング     | "Step 4: 充足性チェック"                  |
| Step 5  | Issue を作成して事後報告            | "Step 5: Issue 作成と事後報告"            |

### TaskCreate の運用ルール

- **開始時**: Skill が起動したら最初に Step 1〜5 を **まとめて** `TaskCreate` で登録する（5 件を一括投入）
- **状態遷移**: `pending → in_progress`（Step 入口） → `completed`（Step 終了）。次の Step に進む前に必ず前 Step を `completed` にする
- **再生成が発生した場合**: Step 4 で不足が見つかり Step 3 へ戻る際は、Step 3 のステータスを `in_progress` に戻し、ヒアリング完了後に再度 `completed` にする
- **Step を飛ばさない**: 必須項目が満たされていない状態で Step 5 を `in_progress` にしてはならない

## Step 1: 概要をヒアリング

`AskUserQuestion` ツールで「何をしたいか」を確認する。ユーザーが既に概要を伝えている場合はスキップしてよい。

聞くべき最低限の項目:

- 何をしたいか（1〜2 文）
- 対象 feature（例: `001-mabiki-mvp`）— 既存 feature への紐付けか、新規 feature 立ち上げかを確認

## Step 2: 対象 feature と docs/ から文脈収集

### 2.1 対象 feature の確定

`ls docs/specs/` で既存 feature の一覧を取得し、Step 1 の概要と最も整合する feature を選定する。曖昧な場合は `AskUserQuestion` で確認する。

### 2.2 docs/ の読み込み

確定した feature について、以下を読み込んで Issue 各セクションへ反映する。

| ファイル                                          | 反映先セクション                                                          |
| ------------------------------------------------- | ------------------------------------------------------------------------- |
| `docs/specs/<NNN>-<feature>/spec.md`              | 概要、背景・目的、完了条件（受入基準と整合させる）、作業内容（ユーザーストーリーの粒度から導出）|
| `docs/specs/<NNN>-<feature>/plan.md`              | 関連ファイル・コンポーネント、検証方法、参考パターン（技術アーキテクチャ・データモデル・契約から導出）|
| `docs/guides/constitution.md`                     | 参考パターン（Mabiki の判断基準。Issue が原則 6「Explicit Non-Goals」に抵触していないか確認）|
| `docs/guides/spec-driven-development.md`          | 参考パターン（SDD のフェーズや品質基準）                                  |

該当する記述がない場合は無理に紐付けず、`_No response_` または該当項目を空にする。SDD 上の根拠資料が必要な場合は `docs/research/` を補助的に参照してよい。

### 2.3 Issue 粒度の判定

mabiki では **「独立して PR にできる作業単位」** を 1 Issue とする（旧 `tasks.md` の T101 相当の粒度）。`plan.md` の §「フェーズ内訳」や Sprint 区切り、`spec.md` のユーザーストーリー（US-N）を参考に、過大／過小な粒度を避ける。

- 過大（1 Issue が大きすぎる）の典型: 「Sprint 1 を全部やる」「Server Actions を全部実装」
- 過小（1 Issue が小さすぎる）の典型: 「import 文を追加」「変数名を変更」

迷ったときは `AskUserQuestion` で粒度を確認する。

## Step 3: Issue 内容を自動生成

Step 1 の概要 + Step 2 の文脈から以下を生成する。

- **タイトル**: 簡潔に（50 文字以内目安）。可能なら対象 feature の prefix（例: `[001-mabiki-mvp]`）を付ける
- **ラベル**: 付与しない
- **本文**: 後述の task.yml 準拠テンプレートで全セクションを生成

各セクションは task.yml の `placeholder` の粒度を目安にする。
推測できない**推奨項目**は `_No response_` とする（必須項目は Step 4 でヒアリングして埋めるため `_No response_` を使わない）。

## Step 4: 情報の充足性チェック & 追加ヒアリング

最終確認ステップは存在しない。よってこの段階で「Issue として成立する情報」を必ず揃える。

### 必須項目

以下が埋まっていない場合は **必ず** `AskUserQuestion` でヒアリングする。`_No response_` は使わない。
（`summary` と `tasks` は task.yml で `required: true`。他は本 Skill で独自に必須化している）

- **概要 (`summary`)**: タスクで何をするかが 1〜2 文で明確
- **作業内容 (`tasks`)**: チェックボックス付きの具体タスクが最低 1 件
- **背景・目的 (`background`)**: なぜ必要か。spec.md / plan.md の該当箇所から要約・参照できる
- **完了条件 (`acceptance_criteria`)**: spec.md の受入基準と矛盾せず、客観的に判定可能
- **検証方法 (`verification`)**: 動作確認の具体的な手順が記載されている

### 推奨項目（可能な限り埋める）

以下は `_No response_` を許容するが、**docs/ や Step 1 の概要から推測できる場合は積極的に埋める**。
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

以下の形式を厳密に守る。セクションの順序・見出しは変更しない。

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
- **背景・目的**: なぜ必要か。`docs/specs/<NNN>-<feature>/spec.md` のユーザーストーリー・受入基準、`docs/research/` の根拠資料を要約・参照する。SDD の「Why」を厳密に書く。
- **関連ファイル・コンポーネント**: 変更・参照が予想されるファイル・SDD アーティファクトを箇条書き。`plan.md` の技術アーキテクチャから拾う。実装初期で未作成のファイルを挙げる場合は「（新規作成）」と明示する。
- **作業内容**: チェックボックス付きの具体タスク。`plan.md` の §「フェーズ内訳」や Sprint 区切り、`spec.md` のユーザーストーリー（US-N）を参照ラベルとして併記してよい（例: `(US-3 / Sprint 1)`）。task.yml で `required`。
- **完了条件**: 「完了」とみなされる客観的条件。**spec.md の受入基準（acceptance criteria）と矛盾しないこと**。
- **検証方法**: 動作確認の手順を必ず記載する（Step 4 で必須項目）。mabiki は実装初期で検証コマンドが未整備の段階のため、整備状況に応じて以下を使い分ける。
  - 検証ゲートが整備済み: 該当コマンド（型チェック・テスト・ビルドなど）を記載
  - 未整備の段階: 手動操作手順、`quickstart.md` がある場合はその手順を記載
- **参考パターン**: 既存の実装パターンや SDD アーティファクトへの参照（例: `docs/guides/spec-driven-development.md`、`docs/research/` の該当章）。
- **参考情報**: 関連 Issue / PR / 外部ドキュメントへのリンク。

推測できない**推奨項目**は `_No response_` とする（必須項目は Step 4 でヒアリングして埋めるため `_No response_` を使わない）。
