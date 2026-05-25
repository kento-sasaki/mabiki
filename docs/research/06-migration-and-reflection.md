# 06. Migration（移行）と Reflection（振り返り）

## このドキュメントで分かること

- Migration（月次・年次）の正式手順と思想
- AM Reflection と PM Reflection の違いと目的
- なぜ「書き写し」が機能の核なのか（意図的な摩擦）
- Migration の実例フロー

**想定読者**: アプリの**最重要画面 = リフレクション体験**を設計する人。ここを理解しないとアプリの魂が抜ける。

---

## 6.0 結論先出し — 二つの軸

BuJo の「内省」装置には、**時間軸の違う 2 種類**がある。

```
┌────────────────────────────────────────────────────────────┐
│                                                            │
│  時間軸  │ 装置                  │ 主な機能                │
│  ────────┼───────────────────────┼──────────────────────── │
│  日次    │ AM / PM Reflection    │ 注意の方向づけ／振り返り│
│  月次    │ Monthly Migration     │ ノイズと信号の分離      │
│  年次    │ Yearly Migration      │ ノート切り替え／総決算  │
│                                                            │
└────────────────────────────────────────────────────────────┘
```

両者は補完関係にあり、片方だけでは機能不全になる。

- **AM/PM Reflection** だけ: タスクは整理されない、ノイズが溜まる
- **Migration** だけ: 月末にしか自分と向き合わない、雑念が散漫

---

## 6.1 Migration — 月末の儀式

### 公式の定義

> "Migration is to surface what's worth the effort, become aware of our actions, and to separate the signal from the noise."[^bj-migration]

[^bj-migration]: <https://bulletjournal.com/pages/migration>

Migration は **BuJo の cornerstone（基石）** と公式に表現される。これは「**未完了タスクを翌期間に書き写す**」という単純な行為だが、その目的は次の三つ。

1. **Surface what's worth the effort**: 努力に値するものを浮かび上がらせる
2. **Become aware of our actions**: 自分の行動に自覚的になる
3. **Separate the signal from the noise**: ノイズから信号を分ける

### Migration の核は「書き写しの摩擦」

```
書き写すコスト  →  自然な選別
                  ↓
   「これ、もう一度書く価値ある？」
                  ↓
   答えが No なら → 打ち消し線で削除
   答えが Yes なら → 新しいページに書き写す
```

これが BuJo を「ただのタスク管理」と分ける本質的な装置。デジタル化で**ここを省略すると BuJo の心臓を取り除く**ことになる。

### 公式の表現

> "If an entry isn't even worth the effort to rewrite it, then it's probably not that important."[^bj-migration]

書き写すコスト自体が、フィルタリング装置として機能している。

---

## 6.2 Monthly Migration の手順

### 標準的なフロー（公式 + 書籍）

月末（または月初）に行う 6 ステップ:

```
1. 次月の Monthly Log を新規セットアップ
   (Calendar Page + Task Page を見開きで用意)

2. 前月の Monthly Log Task Page と
   全 Daily Log を 1 ページずつ読み返す

3. 未完了タスク（・のままのもの）を 1 件ずつ判定:
   ┌─────────────────────────────────────────┐
   │ a. 完了した？  → × に変更                │
   │                                          │
   │ b. もうやらない？                        │
   │    → 打ち消し線で取り消し                │
   │                                          │
   │ c. 来月もやりたい？                      │
   │    → 元の・を > に変更                   │
   │    → 次月の Task Page に新しく・で書き写す│
   │                                          │
   │ d. もっと先（数か月後）にやる？          │
   │    → 元の・を < に変更                   │
   │    → Future Log の該当月に書き写す       │
   └─────────────────────────────────────────┘

4. Future Log の次月の枠を読み、
   今月実施するものを次月 Task Page に書き写す

5. 必要なら Custom Collection を見直す
   (廃止、続行、新規)

6. 振り返りの Note（– で）を Daily Log の最後に書く
   (任意。書籍では推奨)
```

### Migration のシンボル変換

| 元の状態 | レビュー後 | 新規ページの状態 |
|---|---|---|
| `・ タスク` (incomplete) | `× タスク` | (なし) |
| `・ タスク` (incomplete) | `~~・ タスク~~` | (なし) |
| `・ タスク` (incomplete) | `> タスク` | 新しいページに `・ タスク` |
| `・ タスク` (incomplete) | `< タスク` | Future Log に `・ タスク` |

### 書籍が強調する点

書籍 *The Bullet Journal Method* で Carroll は次のように繰り返す。

- Migration は「**Yes と判定するためのプロセスではなく、No と判定する勇気を持つためのプロセス**」である
- 多くの人は「とりあえず Yes」「全部 migrate」しがちだが、これは Migration の意味を殺す
- 月末に「3 回目の Migration」になっているタスクは、**それ自体を診断対象**にする

### 「3 回ルール」

これは書籍と公式ブログで繰り返される具体的な指針:

> 同じタスクが 3 回マイグレーションされ続けたら、診断的に問い直すべき。

問いの例:
1. **必要性**: 本当に必要か？
2. **恐れ**: 怖くて避けているのか？
3. **時間配分**: 時間を確保していないだけか？
4. **分割**: タスクが大きすぎて手をつけられないのか？

これは UI に直接効く指針: 「3 回繰り越されたタスクをハイライトする」ロジックは BuJo の哲学に沿った機能。

---

## 6.3 Yearly Migration — ノート切り替え

### 概要

ノートが満杯になったとき（通常は半年〜1 年程度）、新しいノートに移行する。これが Yearly Migration。

### 手順

```
1. 新しいノートを用意
2. 古いノートの全ページを 1 つずつ見返す
3. 各 Custom Collection について判定:
   a. 廃止する → 新ノートには書き写さない
   b. 続行する → 新ノートに書き写す
4. 未完了のタスクについて判定:
   - 廃止 → 打ち消し線
   - 続行 → 新ノートの該当ページに転記
5. Index を新ノートに新規作成
6. Future Log を新ノートに作り、古いノートの Future Log を書き写す
7. 必要なら、過去の Note や気づきを新ノートに転記
   (年次振り返り、価値観の整理など)
```

### Yearly Migration の心理的意味

Yearly Migration は単なる事務作業ではなく、

- **過去 1 年の振り返り**: 何を達成し、何を捨てたか
- **次の 1 年の意図設定**: 何を続け、何を新しく始めるか
- **「捨てる」練習**: 「全部書き写そう」という誘惑に抗う

書籍では、これを「**ペーパー断捨離**」と表現することもある。

### Carroll の引用

> 何を諦めるかの選択は、何を続けるかの選択と同じくらい重要。

(書籍 *The Bullet Journal Method* より、要旨)

---

## 6.4 AM Reflection — 朝の準備

### 公式の指示

> "The AM reflection is a time to plan and to clear your mind to make room for the day ahead by reviewing all of the pages of the current month to remind you of any open tasks."[^bj-reflection]

[^bj-reflection]: <https://bulletjournal.com/blogs/bulletjournalist/reflection>

### 目的

- **今日の準備**: 今日やるべきタスクを Daily Log にセットアップ
- **頭の中の整理**: 朝の浮遊した思考を Rapid Logging で外部化
- **Intent の設定**: 今日の最優先事項を Signifier `*` で示す

### 標準フロー

```
1. ノートを開く（前日の Daily Log と Monthly Log を見る）
2. 今日のページ（Daily Log）を新規作成
   - Topic（日付）を書く
   - ページ番号を振る
3. Monthly Log Task Page を見て、今日着手するものを書き写す
4. 前日の Daily Log の未完了タスクを判定（簡易 Migration）
   - 今日もやる → 今日の Daily Log に書き写す
   - もうやらない → 打ち消し線
5. 頭の中で気になっていることを Rapid Logging で外部化
6. 最重要 1〜3 件に *・ を付ける
7. （任意）今日の Why を Note (–) で書く
```

### 所要時間

公式の明示はないが、コミュニティでは **5〜15 分**が一般的。書籍では「時間より継続性」を強調。

### Marcus Aurelius との類似

`02-philosophy.md` で触れた通り、マルクス・アウレリウスの朝の習慣にそっくり。

> Marcus did his journaling in the morning, walking himself through what the day would bring and what he would need to bring to the day.[^dailystoic]

[^dailystoic]: <https://dailystoic.com/journaling/>

---

## 6.5 PM Reflection — 夜の振り返り

### 公式の指示

> "The PM reflection leans toward review to help you unwind, whereas the AM reflection favors planning to gear up for your day. During the PM reflection, you briefly scan your daily record and add anything that's missing."[^bj-reflection]

### 目的

- **タスクのクロージング**: 完了した Task を `×` でマーク
- **当日の追加ログ**: 書き忘れた Event や Note を追記
- **省察**: 今日学んだこと、感じたこと、明日に活かしたいことを Note (`–`) で書く
- **デクラタリング**: 頭の中の残り物を出して眠る

### 標準フロー

```
1. 今日の Daily Log を開く
2. 完了したタスクの・に × を上書き
3. 当日に書き忘れた Event や Note を追記
4. 未完了のタスクを見て判定（厳密な Migration は月末に行うが、
   ここで「明日もやる」と決めて翌日のページに準備しておいてもよい）
5. （任意）今日の振り返り Note を書く:
   - 何が良かったか
   - 何が学びだったか
   - 何に感謝したか
6. ノートを閉じる
```

### 所要時間

公式の明示はないが、コミュニティでは **5〜10 分**が一般的。

### PM Reflection の意味

書籍では、PM Reflection を「**1 日を閉じる儀式**」として位置づけている。スマホで SNS をスクロールして眠るのとは正反対の、能動的なクロージング。

> "It functions as a great way to declutter your mind and unplug at the end of the day."[^bj-reflection]

---

## 6.6 AM/PM Reflection の対比

```
┌────────────────────────────────────┬────────────────────────────────────┐
│           AM REFLECTION            │           PM REFLECTION            │
├────────────────────────────────────┼────────────────────────────────────┤
│ 主な動詞: Plan（計画）             │ 主な動詞: Review（振り返り）       │
│ 目的: 注意の方向づけ               │ 目的: 1 日のクロージング           │
│ 何をする:                          │ 何をする:                          │
│  - 今日の Daily Log を作る         │  - × を付ける                      │
│  - 前日の未完了タスクの簡易整理    │  - 書き忘れを追加                  │
│  - 今日の優先事項を *・            │  - 気づきを Note で残す            │
│  - Monthly Log から今日分を降ろす  │  - 明日の準備（任意）              │
│ 心理状態: 前向き、Intent 設定      │ 心理状態: 受容、感謝               │
└────────────────────────────────────┴────────────────────────────────────┘
```

### 両者が揃って機能する

- AM だけ: 振り返らない → 学習が蓄積しない、後悔が積もる
- PM だけ: 計画しない → 翌朝が混乱したスタートに
- 両方: 計画 → 実行 → 振り返り のサイクルが完成

---

## 6.7 Weekly Review（任意・推奨）

書籍では明示的に「Weekly Review」を必須としていないが、コミュニティとカテゴリ別の本では推奨する派もある。

### Weekly Review の構成（一般的）

- 過去 1 週間の Daily Log を読み返す
- 3 つの達成を選んで Note (`–`) で書く
- 翌週の優先 3 タスクを選ぶ
- Habit Tracker の結果を見る
- 何を改善するかを 1 つ決める

15〜30 分。書籍ではこれを「**Mid-Resolution**」と呼ぶことがある。

---

## 6.8 月次振り返り（Monthly Review）

Monthly Migration と一緒に行う、より広い視点での振り返り。

### 構成

```
1. 今月の総括（– で 1-3 行）:
   - 最も誇れることは何か
   - 最も学んだことは何か
   - 何に時間を使いすぎたか
   - 何を捨てるべきか

2. 価値観の再確認:
   - 今月のタスクは Why に沿っていたか
   - 「3 回 migrate されているタスク」を診断

3. 翌月の Intent 設定:
   - 来月にフォーカスする 1〜3 個のテーマ
   - 新しい Custom Collection の必要性
```

### 書籍が提案するテンプレ

書籍では「Monthly Review Page」として 1 ページ取り、上記の項目を箇条書きで書くことを推奨している（厳密なフォーマットは自由）。

---

## 6.9 Migration の実例 — 1 か月のシナリオ

具体例を 1 つ通して見る。Kento さん（架空のユーザー）の 5 月。

### 5 月 1 日（月初）

```
[Monthly Log Task Page - May 2026]
・ 月次報告書の作成
・ プロジェクト A のドキュメント完成
・ 引っ越しの段取り
・ 母の日のプレゼント
・ 確定申告関連書類の整理
・ 健康診断の予約
・ クライアント会議の準備
・ 旅行プラン Italy を詰める
・ 父の日 (6月) のリマインダ
```

### 5 月 31 日（月末レビュー）

各タスクの結果を判定:

```
× 月次報告書の作成               (5/10 完了)
× プロジェクト A のドキュメント完成 (5/22 完了)
> 引っ越しの段取り               (6月に持ち越し)
× 母の日のプレゼント             (5/14 完了)
~~・ 確定申告関連書類の整理~~     (もうやらない、混在ノートを廃止)
> 健康診断の予約                 (6月に持ち越し)
× クライアント会議の準備         (5/20 完了)
< 旅行プラン Italy を詰める      (7月に Schedule)
× 父の日 (6月) のリマインダ      (Future Log に書いた)
```

### 6 月 1 日（次月セットアップ）

```
[Monthly Log Task Page - June 2026]
・ 引っ越しの段取り        ← May から migrate
・ 健康診断の予約          ← May から migrate
・ 父の日のプレゼント      ← Future Log から
・ 6 月の月次報告書
・ プロジェクト B キックオフ
```

### Future Log の更新

```
[Future Log - July]
・ 旅行プラン Italy を詰める   ← May から schedule
```

### この例で起きた選別

- 完了タスク: 4 件 → 達成感の蓄積
- 廃止: 1 件 → 「やらない決断」の練習
- Migrate: 3 件 → 「来月もやる」の確認
- Schedule: 1 件 → 「今やる時期じゃない」の認識

タスク 9 件のうち、純粋に「またノートに書く」のは 4 件（migrate + schedule の合計）。**過半数（5 件）が「決着」した**ことが重要。

---

## 6.10 Migration の哲学的意義

### 「忙しさの幻想」を打ち砕く

毎月、自分が「やる」と決めたタスクの半分が migrate されているとしたら、それは:

- 計画が大きすぎる
- 時間配分が間違っている
- やる気がないのに「やるべき」と書いている
- 価値観がブレている

のいずれか。Migration は、これらに気づかせる装置として機能する。

### 「決めない」ことのコスト

書籍では、「決めない」ことの代償について繰り返し述べている。

- 決めなければ、タスクは無限に積み上がる
- 積み上がれば、ノートを開くこと自体が苦しくなる
- 苦しくなれば、ノートを使うのをやめる

→ **「決める」のは続けるための装置**。Migration は「やめる練習」でもある。

### Carroll の引用

> "Migration is the practice of intentionality."[^bj-migration]

「マイグレーションは意図性の実践そのもの」と公式に述べている。これは MAU / DAU を稼ぐためのアプリ機能ではなく、ユーザーの人生を変えるかもしれない**儀式の場**である。

---

## 6.11 デジタル化の論点（プレビュー）

Migration と Reflection は、デジタル化で**最も注意深く扱うべき**領域。

### やってはいけないこと

1. **「未完了タスクの自動繰り越し」を実装する** → BuJo の心臓を抜く
2. **「全部一括 migrate」ボタンを置く** → 書き写しの摩擦が消える
3. **Reflection を「省略可能」なオプションにする** → 機能の核を弱める

### 効きそうなアイデア

1. **イブニング・リチュアル画面**: 夜開くと PM Reflection 画面に強制誘導
2. **モーニング・リチュアル画面**: 朝開くと AM Reflection 画面
3. **1 件ずつのマイグレーション UI**: タスクをカード形式で 1 枚ずつ表示
4. **3 回マイグレーション警告**: 3 回繰り越された Task に診断質問を出す
5. **書き写し（=タップ）を要求**: 「自動で次月へ」ではなく「明示的にタップで次月に書き写す」

詳細は `10-digital-implementation-considerations.md`。

---

## 6.12 まとめ

1. **Migration** は BuJo の cornerstone。「書き写す摩擦」が意図性を生む
2. **3 回ルール**: 3 回繰り越されたタスクは診断的に問い直す
3. **AM Reflection** は計画（Plan）、**PM Reflection** は振り返り（Review）
4. **Yearly Migration** はノート切り替え兼、人生レベルの振り返り
5. デジタル化では **「Migration を自動化しないこと」**が最重要原則

次は `07-threading-and-index.md` で、Index と Threading による検索性の仕組みを見る。

