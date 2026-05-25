# 05. Collections（コレクション）の構造 — Index / Future Log / Monthly Log / Daily Log / Custom

## このドキュメントで分かること

- BuJo を構成する 4 つの基本コレクションの正式仕様
- 各コレクションのレイアウト（ASCII 図）
- セットアップの公式手順
- Custom Collection の作り方と運用ルール

**想定読者**: アプリの画面構成・情報アーキテクチャを設計する実装者。各コレクションが「何のためのページか」と「データ的に何を持つか」を把握したい人。

---

## 5.0 全体構造 — ノートのアーキテクチャ

公式 [How to Bullet Journal](https://bulletjournal.com/pages/how-to-bullet-journal) と書籍に基づく標準構造:

```
┌─────────────────────────────────────────────────────────────────┐
│                  ┌─────────────────┐                            │
│  p.1-2           │   INDEX         │  目次。全コレクションの    │
│  (4 ページ程度)  │                 │  ページ番号を記録          │
│                  └─────────────────┘                            │
│  ─────────────                                                  │
│                  ┌─────────────────┐                            │
│  p.3-4           │   FUTURE LOG    │  6〜12ヶ月先の予定         │
│  (見開き2-4枚)   │                 │                            │
│                  └─────────────────┘                            │
│  ─────────────                                                  │
│                  ┌─────────────────┐                            │
│  p.7-8           │  MONTHLY LOG    │  当月のカレンダー +        │
│                  │  (Calendar+Task)│  当月のタスクページ        │
│                  └─────────────────┘                            │
│                                                                 │
│                  ┌─────────────────┐                            │
│  p.9-...         │   DAILY LOG     │  当日のログ                │
│                  │   (5/24)        │                            │
│                  │   ...           │                            │
│                  └─────────────────┘                            │
│                  ┌─────────────────┐                            │
│                  │   DAILY LOG     │                            │
│                  │   (5/25)        │                            │
│                  └─────────────────┘                            │
│                                                                 │
│                  ┌─────────────────┐                            │
│                  │ CUSTOM COLL.    │  読書記録、習慣トラッカー │
│                  │ (Books to Read) │  プロジェクトノート 等     │
│                  └─────────────────┘                            │
│                                                                 │
│                  ┌─────────────────┐                            │
│                  │  MONTHLY LOG    │  翌月のセットアップ        │
│                  │  (June)         │                            │
│                  └─────────────────┘                            │
│                                                                 │
│                  ...                                            │
└─────────────────────────────────────────────────────────────────┘
```

**重要な原則**: ページは「**書いた順に進む**」ものであり、コレクションは時系列に**混在**する。Custom Collection は Daily Log の合間に挟まる。これを後から探せるようにするのが **Index** と **Threading**。

---

## 5.1 Index（インデックス）

### 公式の指示

> "Simply leave the first couple pages of your notebook blank and give them the topic of 'Index.' As you progress, add the topics of your entries and their page numbers to the Index, so you can quickly find them later."[^how-to]

[^how-to]: <https://bulletjournal.com/pages/how-to-bullet-journal>

### レイアウト

```
─────────────────────────────────────
           INDEX
─────────────────────────────────────
Future Log . . . . . . . . . . . . . 3-4
May 2026 . . . . . . . . . . . . . . 5-6
Daily Log (May) . . . . . . . . . . 7-22
Books to Read . . . . . . . . . . . 12, 28
Project X . . . . . . . . . . . . . 14-15, 35
Travel Italy . . . . . . . . . . . . 24-25
June 2026 . . . . . . . . . . . . . 30-31
Habit Tracker (June) . . . . . . . . 33
Daily Log (June) . . . . . . . . . . 34-50
─────────────────────────────────────
                                p.1
─────────────────────────────────────
```

### 役割

- **目次**: ノートの全コレクションを一覧化
- **検索の起点**: 「あれ、Project X のメモどこだっけ」→ Index を見れば `p.14-15, p.35` と分かる
- **書きっぱなしを許す装置**: ページの順序を気にせず書け、後から Index で見つけられる

### 運用ルール

1. 新しいコレクションを始めたら、**そのページで止まって Index を更新する**
2. Daily Log は通常 Index に書かない（毎日記入する必要があり、肥大化するため）
3. ただし「5 月のデイリーログ群」をまとめて 1 行で書くことはあり
4. 同じトピックが複数ページに散る場合、ページ番号をカンマ区切りで列挙

### Threading との関係

Index に書く代わりに、Threading で「p.12 → p.28」と直接リンクする方法もある（→ `07-threading-and-index.md`）。両者を併用するのが一般的。

### Index の Modification

公式ブログ[^index-mods]で紹介されている派生バリエーション:

[^index-mods]: <https://bulletjournal.com/blogs/bulletjournalist/index-mods-and-variations>

- **Subject-Based Index**: トピックごとにグループ化（Work / Personal / Health 等）
- **Color-Coded Index**: 色分けでカテゴリを示す
- **Alphabetical Index**: 五十音／アルファベット順
- **Daily Log Index (Calendex)**: 日付軸でフラットに表示

---

## 5.2 Future Log（フューチャーログ）

### 公式の指示

> "Set up your Future Log by graphing the pages by the amount of months you'll need. Two equally-spaced horizontal lines across facing pages creates a six-month calendar."[^how-to]

### 役割

- 当月以外の予定（来月以降〜1 年先まで）を一覧化
- 月初のセットアップで「今月やるべきもの」を Future Log から Monthly Log に降ろす
- 月末のレビューで「< Scheduled」されたタスクを Future Log に登録

### 標準レイアウト（Carroll オリジナル）

見開き 2 ページで 6 ヶ月分を作る基本形:

```
─────────────────────┬─────────────────────
        June         │       Sept
                     │
・ Henri 誕生日      │ ○ ヨーロッパ旅行
○ 6/15 顧客イベント  │ ・ 確定申告開始
                     │
─────────────────────┼─────────────────────
        July         │        Oct
                     │
○ 7/10 引っ越し      │ ○ プロジェクト B 〆切
・ 夏休みの計画      │
                     │
─────────────────────┼─────────────────────
        Aug          │        Nov
                     │
○ 8/22 結婚式 (友人) │ ・ 年末挨拶準備
                     │
─────────────────────┴─────────────────────
                                 p.3-4
```

これを 12 ヶ月分にするなら、2 つの見開き（合計 4 ページ）が必要。

### 派生レイアウト

#### Alastair Method[^alastair]

[^alastair]: <https://kalynbrooke.com/planning/bullet-journaling/bullet-journal-future-log-ideas/>

Alastair Johnston 考案。1 ページに 6 ヶ月の月見出しを縦に並べ、右側の広いカラムにタスクの中身、左側の細いカラムにそれぞれの月の○印を打つ方式。

```
J F M A M J | Tasks / Events
──────────────────────────────────
   ○        | 3/15 確定申告
      ○     | 4/5  歯医者の予約
         ○  | 5/10 母の誕生日
            | 6/15 顧客イベント
   ○        | 3/30 給与改定提出
──────────────────────────────────
```

省スペースで、1 ページに 6 ヶ月分が入るのが利点。

#### Calendex Method

Eddy Hope 考案[^calendex]。2 ページに 1 年分のカレンダーを作り、日付に Bullet を打って、対応するページ番号を書く。

[^calendex]: 同上

```
     Jan Feb Mar Apr May Jun Jul Aug Sep Oct Nov Dec
 1
 2                          ・p.34
 3
 4                                  ・p.40
 5
 6
 ...
30           ・p.28
31
```

ページ番号で関連 Daily Log にジャンプできるのが特徴。Threading の発展形。

### 運用ルール

1. 月末の Migration 時に「< Scheduled」になったタスクを Future Log に書き写す
2. 月初の Monthly Log セットアップ時に、Future Log の該当月を見て、今月分を Monthly Log に降ろす
3. Future Log に書いたタスクは、降ろし元として残す（lineage を保つ）

---

## 5.3 Monthly Log（マンスリーログ）— Calendar + Task の見開き

### 公式の指示

> "Go to the next available spread of facing pages. The left page will be your Calendar Page; the right will be your Task Page."[^how-to]

### 構造

```
─────────────────────────┬─────────────────────────
        May 2026         │      May Tasks
─────────────────────────┼─────────────────────────
 1  M                    │
 2  T                    │ ・ 5月の月次報告
 3  W                    │ ・ プロジェクト A 完了
 4  T  ○ Andreas 出張    │ ・ 引っ越しの段取り
 5  F                    │ ・ 母の日のプレゼント
 6  S                    │ ・ 確定申告関連書類整理
 7  S  ・ 庭の手入れ     │ ・ 健康診断の予約
 8  M                    │ ・ クライアント会議準備
 9  T                    │ ・ 旅行プラン Italy 詰める
10  W  ○ 14:00 ミーティング│ ・ 父の日 (6月) のリマインダ
...
─────────────────────────┴─────────────────────────
                                          p.5-6
```

### Calendar Page（左ページ）

- 縦に日付と曜日を全て書く（5 月なら 1〜31 行）
- 各日にイベント・期限を書く
- スペースが少ないので、極めて短く書く（`○ 14:00 mtg`）

### Task Page（右ページ）

- その月にやりたい / やるべきタスクを Rapid Logging で列挙
- Future Log から降ろしたものを含む
- 月末のレビュー後、次月の Monthly Log にマイグレーション

### 運用ルール

1. 月初に新しい見開きを設定（Calendar Page + Task Page）
2. Calendar Page に「分かっている予定」を Future Log から書き写す
3. Task Page にその月の「やりたいこと」を書く
4. 月中、Daily Log と並行して、Calendar Page には新しい予定を、Task Page には新しい月単位タスクを追記
5. 月末に Migration

### 派生レイアウト

#### Weekly Spread

公式ではないが、コミュニティで頻出。Calendar Page を月単位ではなく週単位に分割する派生。

```
─── Week 22 (May 25-31) ───────────────
M 25 |
T 26 | ○ チームミーティング
W 27 | ・ ドキュメント完成
T 28 | ○ プレゼン
F 29 |
S 30 |
S 31 | レビュー
────────────────────────────────────────
```

Carroll は書籍で「Weekly Spread は使ってもいいが、Daily Log の柔軟性が失われる」と述べている。

#### Habit Tracker（埋め込み型）

Task Page の余白に、習慣トラッカーを埋め込む例。

```
─── May Habits ──────────────────────
       1 2 3 4 5 6 7 8 9 10 ...
Yoga   ✓ ✓ . ✓ ✓ ✓ . . ✓  ✓
Read   ✓ ✓ ✓ ✓ . . ✓ ✓ ✓  ✓
Walk   ✓ . . ✓ ✓ . . ✓ .  ✓
─────────────────────────────────────
```

---

## 5.4 Daily Log（デイリーログ）

### 公式の指示

> "At the top of the page, record the date as your topic. Throughout the course of the day, simply Rapid Log your Tasks, Events, and Notes as they occur."[^how-to]

### 役割

- 当日の流れに沿った Rapid Logging の中心地
- Task、Event、Note が時系列に混ざる
- 最も使用頻度が高いコレクション

### レイアウト

```
─────────────────────────────────────
        5.24.26 (月)
─────────────────────────────────────
・ プロジェクト A 議事録を Slack へ
× 牛乳を買う
*・ Andreas からのフィードバックに返信
・ チームミーティングの議題を準備
○ 14:00 チームミーティング
○ 19:30 ヨガクラス
– Andreas のコメント: 「ドキュメントが薄い」
– 朝の散歩で気づいた: 自分の時間を最優先する
!– React Server Components で書き直す案
>・ 確定申告の書類を準備
                                p.42
─────────────────────────────────────
```

### 重要な特性

1. **書く順序は固定**: 入力した順に並ぶ。後から並び替えない（Note が下、Task が上、のような事前ソートは不要）
2. **長さは可変**: 1 日 3 行のこともあれば、見開きいっぱいになることもある
3. **休んでいい**: 旅行で書かない日があっても OK。BuJo は連続性を要求しない
4. **時系列の混在**: 朝のタスク、昼の Note、夕方の Event がそのまま順に並ぶ

### Carroll のオリジナル動画での書き方

公式 YouTube の "How to Bullet Journal" 動画では、

- 日付を見出しで書く（フォーマットは自由）
- Bullet をその場で書く
- 終わった Task はその日のうちに `×`
- 翌日には新しい Topic（日付）で新しいページに移る

### Daily Log と Monthly Log の関係

- Monthly Log Task Page: 「今月やりたいこと」
- Daily Log: 「今日やったこと / やること」
- 月初に Task Page から Daily Log に「今日やる」項目を書き写す（任意）
- 月末に Daily Log で完了しなかったものを次月の Task Page にマイグレーション

---

## 5.5 Custom Collections（カスタムコレクション）

### 公式の指示

> "A 'collection' is a spread used to collect information on a specific topic."[^tinyray]

[^tinyray]: <https://www.tinyrayofsunshine.com/blog/bullet-journal-guide>

### 役割

Daily / Monthly / Future の 3 つの基本コレクションでは扱いきれない、テーマ別の情報を集めるページ。

### 代表的な Custom Collections

| カテゴリ | 例 |
|---|---|
| 学習 | Books to Read、語学学習ログ、論文メモ |
| 健康 | Habit Tracker、睡眠ログ、体重ログ |
| プロジェクト | Project X plan、ブレスト、議事録 |
| 旅行 | Italy Trip plan、持ち物リスト |
| 創作 | 短編アイデアリスト、絵のラフ、レシピ |
| 家計 | 月次支出、年間予算、欲しいもの |
| メタ | 1 年振り返り、5 年計画、Why のメモ |

### レイアウト例: Books to Read

```
─────────────────────────────────────
       BOOKS TO READ (2026)
─────────────────────────────────────
・ The Bullet Journal Method - Ryder Carroll
× Deep Work - Cal Newport
・ Atomic Habits - James Clear
・ Building a Second Brain - Tiago Forte
・ Stoic Wisdom - Nancy Sherman
– (Andreas 推薦) The Anti-Library
!– 次は「日本の小説」を読む年にしたい
                                p.12
─────────────────────────────────────
```

### レイアウト例: Habit Tracker

```
─── May Habit Tracker ───────────────
         1  2  3  4  5  6  7  ...
Yoga     ✓  ✓  .  ✓  ✓  ✓  .  ...
Read     ✓  ✓  ✓  ✓  .  .  ✓  ...
Walk     ✓  .  .  ✓  ✓  .  .  ...
Meditate .  ✓  ✓  ✓  ✓  .  ✓  ...
─────────────────────────────────────
                                p.33
```

### レイアウト例: Project Page

```
─── PROJECT X ───────────────────────
Goal: Q2 中にローンチ
Why: 自分のプロダクトを世に出す経験

・ ランディングページ
  ・ コピー
  ・ デザイン
  ・ 法務確認
・ MVP 実装
  ・ 認証
  ・ コア機能 A
  ・ コア機能 B
・ ローンチ準備
  ・ 価格設定
  ・ プレスリリース

– 競合: A, B, C
!– 「友達 10 人テスト」を入れる
                                p.14-15
─────────────────────────────────────
```

### 運用ルール

1. **思いついたタイミングで作る**: 「あ、このトピックは独立させたい」と思ったら、次の空きページに新規作成
2. **Index に登録**: トピック名とページ番号を Index に書く
3. **Threading で続ける**: 1 ページで終わらないなら、次回続きを書くページにジャンプリンク（`p.12 → p.28`）
4. **不要になったら放置**: 完成したコレクションは捨てない。次のノートに引き継ぐかどうかは年末に判断

### Custom Collection の作りすぎ問題

Carroll の警告:

> "If the chair is too fancy, you won't sit in it. If the kitchen is too complicated, you won't cook in it."[^back-to-basics]

[^back-to-basics]: <https://bulletjournal.com/blogs/bulletjournalist/back-to-the-basics>

Custom Collection を 30 個も 40 個も作ると、

- Index がパンクする
- 「どこに書いたっけ」が増える
- そもそも見返さなくなる

**目安**: 月に 1〜3 個の新規 Custom Collection が現実的。

---

## 5.6 セットアップ全体フロー（公式）

新しいノートを開いたときの手順:

```
Step 1: ノートの最初の 2-4 ページを INDEX として確保
Step 2: 続けて FUTURE LOG を 2-4 ページ確保（6-12 ヶ月分）
Step 3: 今月の MONTHLY LOG を Calendar Page + Task Page として 2 ページ確保
Step 4: 続いて今日の DAILY LOG を始める
Step 5: 必要に応じて Custom Collection を間に挟む
Step 6: 月末になったら、次月の MONTHLY LOG を新しい見開きで作る
        （Daily Log の途中でも構わない）
Step 7: 同時に Migration を行う
```

これがエッセンス。**ページの順序は時系列に進み、コレクションは混在する**ことを前提に、Index と Threading で「後から探せる」ようにする。

---

## 5.7 コレクション間の情報フロー

```
        ┌──────────────────────┐
        │   FUTURE LOG         │
        │   (6-12 ヶ月先)      │
        └──────────┬───────────┘
                   │ 月初に降ろす
                   ▼
        ┌──────────────────────┐
        │   MONTHLY LOG        │
        │   Task Page (今月)   │
        └──────────┬───────────┘
                   │ 日々参照、書き写し
                   ▼
        ┌──────────────────────┐
        │   DAILY LOG          │
        │   (今日)             │
        └──────────┬───────────┘
                   │ 完了 / 未完了
                   ▼
        ┌──────────────────────┐
        │   MIGRATION          │
        │   (月末レビュー)     │
        └────┬─────────────────┘
             │
       ┌─────┴─────┐
       │           │
       ▼           ▼
  次月 Monthly  Future Log
  Task Page    (< Schedule)
```

このフローを見ると、BuJo は単なる ToDo リストではなく、「**時間軸を行き来する一種のワークフロー**」だと分かる。

---

## 5.8 コレクションのデータモデル化

実装視点でコレクションを抽象化すると:

```typescript
interface Collection {
  id: UUID
  type: 'INDEX' | 'FUTURE_LOG' | 'MONTHLY_LOG' | 'DAILY_LOG' | 'CUSTOM'
  topic: string             // e.g. "May 2026", "Books to Read"
  pageNumber: number        // 紙ノートの場合
  createdAt: Date
  bullets: Bullet[]         // このコレクションに属する Bullets
  linkedCollections: UUID[] // Threading で繋がる他のコレクション
}

interface DailyLog extends Collection {
  type: 'DAILY_LOG'
  date: Date                // この日のログ
}

interface MonthlyLog extends Collection {
  type: 'MONTHLY_LOG'
  year: number
  month: number             // 1-12
  calendarBullets: Bullet[] // Calendar Page
  taskBullets: Bullet[]     // Task Page
}

interface FutureLog extends Collection {
  type: 'FUTURE_LOG'
  startMonth: { year, month }
  endMonth: { year, month }
  // bullets は month の hint を持つ
}

interface CustomCollection extends Collection {
  type: 'CUSTOM'
  // 自由
}
```

### 留意点

- `Bullet` は `collectionId` で所属を持つ
- `Bullet.lineage` (sourceBulletId, migratedToBulletId) で書き写しの系譜を保つ
- `Collection.linkedCollections` で Threading を表現

---

## 5.9 デジタル化での再解釈

### Index は検索 + タグになる

紙の Index = 全コレクションを手書きで一覧化。
デジタルでは:

- **検索**（全文）
- **タグ**（コレクションのメタデータ）
- **タイムライン**（時系列）

これらが Index の役割を分散して引き受ける。ただし、**「全 Custom Collection を俯瞰する画面」**は意外と重要で、これを欠かすとアプリがフラットになる。

### Future Log は「予約された未来」のビュー

カレンダーアプリと類似するが、BuJo の Future Log は「**まだ日付が決まっていないが、月単位ではある**」エントリも含む。例: 「6 月のどこかで歯医者」。これは通常のカレンダーアプリでは扱いにくい。

### Monthly Log は「月のダッシュボード」

Calendar Page と Task Page は同じ画面に並べる UI 設計が自然。

### Daily Log はメイン画面

これがアプリの「ホーム」になる。

### Custom Collection は「フォルダ」「ノート」になる

汎用的なノート機能として実装。プロジェクト管理アプリの「プロジェクト」に近い。

詳細は `10-digital-implementation-considerations.md`。

---

## 5.10 まとめ

1. BuJo は 4 つの基本コレクション（**Index / Future Log / Monthly Log / Daily Log**）+ Custom Collection で成り立つ
2. **Monthly Log は Calendar Page + Task Page の見開き**が公式
3. ページは**時系列に進む**ことが原則で、コレクションは混在する
4. 探索は **Index + Threading** で実現する（→ `07`）
5. デジタル化では、Index は検索／タグに、Daily Log はホーム画面に、それぞれ移し替えるのが自然

次は `06-migration-and-reflection.md` で、月末レビューと AM/PM Reflection の哲学と手順を深掘りする。

