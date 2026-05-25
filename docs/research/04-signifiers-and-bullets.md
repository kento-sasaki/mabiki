# 04. 記号体系（Bullets & Signifiers）と状態遷移の完全仕様

## このドキュメントで分かること

- 3 種類の Bullet（Task / Event / Note）の正式仕様
- Task の状態遷移（incomplete → complete / migrated / scheduled / irrelevant）
- 3 種類の公式 Signifier（Priority / Inspiration / Explore）
- カスタム記号の作り方と運用上の限界

**想定読者**: データモデルを書く実装者。状態遷移図と enum 定義をこのドキュメントから直接導けるレベルまで詳細を期待する人。

---

## 4.0 結論先出し — 完全な記号一覧

```
[BULLETS（行頭の主記号）]
・  Task        — 行動を起こすべきもの
○  Event       — 日付に紐づく出来事
–  Note        — 情報、考え、観察

[TASK STATE TRANSITIONS（タスクの状態）]
・  Open / Incomplete       未完了
×  Completed                完了（・の上に重ねて書く）
>  Migrated                 翌期間（マンスリーログ等）に移行
<  Scheduled                未来（フューチャーログ）に予約
~~・~~  Irrelevant          打ち消し線 = 不要と判断、消滅

[SIGNIFIERS（行頭、Bullet の左に置く補助記号）]
*  Priority                 優先度
!  Inspiration              ひらめき
?  Explore / 👁 Explore     要調査（公式は「目」アイコンも使用）

[CUSTOM SIGNIFIERS（一般的なコミュニティ拡張）]
$  Money / Finance
♡  Personal
☆  Goal
$  Spending
@  Location / People
```

---

## 4.1 3 種類の Bullet — 主記号

### Task: `・`（Dot / 中黒）

> "Tasks are represented by a simple dot '•' and include any kind of actionable items like 'Pick up dry cleaning'."[^rl-faq]

[^rl-faq]: <https://bulletjournal.com/blogs/faq/what-is-rapid-logging-understand-rapid-logging-bullets-and-signifiers>

**意味**: 自分（または他者）が行動を起こすべき、現実世界に介入する項目。

**例**:
```
・ 牛乳を買う
・ 月次報告書を提出する
・ Andreas に電話する
・ React Server Components を試す
```

**判定基準**: 「これは私（または誰か）が何かをする必要がある」と言えるか。
**判定がブレるケース**: 「読みたい本」は Task か Note か。Carroll の指針では、**「行動の動詞が頭にあるか」**で判定する。「本を買う」「読む」は Task、「面白そうな本だった」は Note。

### Event: `○`（Open Circle / 円）

> "Events are date-related entries that can either be scheduled (e.g. 'Charlie's birthday') or logged after they occur (e.g. 'signed the lease')."[^rl-faq]

**意味**: 日時に紐づく、起きたこと／起きる予定の出来事。**自分のアクションではなく「世界の出来事」**であることがポイント。

**例**:
```
○ 14:00 チームミーティング
○ Charlie の誕生日
○ 父の手術日
○ プロジェクトのキックオフ（過去）
```

**判定基準**: 「これは時間と紐づいているか」「これは自分の行動というよりイベントか」。

**Task との区別**: 「ミーティングに参加する」は Task。「ミーティングが 14:00 にある」は Event。BuJo では同じ場面でも、書き手の視点で記号が変わる。

### Note: `–`（Dash / ハイフン）

> "Notes are entries that you want to remember but aren't immediately or necessarily actionable."[^rl-faq]

**意味**: 行動を要求しない、情報・観察・気づき・参照したい事実。

**例**:
```
– Andreas は明日休み
– 「Deep Work」は集中ブロックを 4 時間が上限と言っている
– 朝の散歩で気づいた: 自分の時間を最優先する
– 木曜は雨の予報
```

**判定基準**: 「これは行動ではなく、覚えておきたい情報か」。

### 3 種類の境界が曖昧なときの実践

これは BuJo 初心者が必ず悩むところ。Carroll の実用的な助言は次の通り。

1. **迷ったら Note**: あとで Note → Task に格上げできる
2. **行動が明確になった瞬間に Task に変換**: 「読みたい本」(`–`) → 「アマゾンで注文する」(`・`)
3. **完璧を求めない**: 同じ事象が状況により違う Bullet になっても OK

---

## 4.2 Task の状態遷移

これがアプリ設計で最も重要なセクション。

### 5 つの状態

```
                  ┌──────────────────────────┐
                  │ ・ Open / Incomplete     │
                  │ （新規作成の初期状態）   │
                  └──────────────────────────┘
                              │
                              ├──── 完了した ────────► × Completed
                              │
                              ├──── 翌期間に移す ───► > Migrated
                              │
                              ├──── 未来に予約 ─────► < Scheduled
                              │
                              └──── やめる ─────────► ~~・~~ Irrelevant
                                                    （打ち消し線）
```

公式の表現:

> "Task complete (X), migrated (>), scheduled (<)"[^how-to][^rl-faq]

[^how-to]: <https://bulletjournal.com/pages/how-to-bullet-journal>

### 4.2.1 `・` Open — 未完了

新規作成時のデフォルト状態。行頭に `・` のみ。

```
・ プロジェクト A のドキュメントを書く
```

### 4.2.2 `×` Completed — 完了

タスクが完了した瞬間、`・` の上から `×` を書いて重ねる（あるいは横に `×` を書く）。元の `・` も読み取れる状態で残す（重要：完全消去はしない）。

```
× プロジェクト A のドキュメントを書く
```

**ポイント**: 完了タスクは消去ではなく、上書き。これにより「今日 / 今月で何を達成したか」が振り返り可能。

### 4.2.3 `>` Migrated — 翌期間に移行

未完了タスクを、翌日 / 翌月の Daily Log や Monthly Log に書き写したことを示す。元のタスクの `・` の上に `>` を書く。

```
> プロジェクト A のドキュメントを書く（5/24 の Daily に移行した）
```

公式の表現:

> "Migration (>): Moving a bullet to some time in the near future, by placing it on the next available daily or monthly log."[^debit]

[^debit]: <https://www.debitthiscreatethat.com/navigating-your-bullet-journal-migration-scheduling-and-threading/>

**重要**: `>` を付けた時点で、必ず新しいページに同じ内容を `・` で書き写す。書き写しなしの `>` は意味を成さない。

### 4.2.4 `<` Scheduled — 未来に予約

「数か月先にやる」「日時は決まっているが当面着手しない」タスクは、Future Log に書き写し、元のタスクには `<` を付ける。

```
< 確定申告の準備（来年 2 月の Future Log に書いた）
```

公式の表現:

> "Scheduling (<): Moving a bullet to some time in the more distant future, by placing it on the future log."[^debit]

### Migration と Scheduling の違い（重要）

| | Migration (`>`) | Scheduling (`<`) |
|---|---|---|
| 移動先 | 直近のログ（翌日 / 翌月） | Future Log（数か月〜1年先） |
| タイミング | 月末のレビュー or 都度 | タスクの性質が「遠い未来」と判明したとき |
| 期待される頻度 | 高（毎月発生） | 低（タスク全体の数%） |
| 心理的意味 | 「先送り」というより「今期に持ち越し」 | 「然るべき時に取り組む」 |

### 4.2.5 `~~・~~` Irrelevant — 打ち消し線

「もうやらない」「不要になった」「優先順位から外れた」タスクは、横線で打ち消す。

```
~~・ プロジェクト B のリサーチ~~
```

これは**消去ではなく、明示的な「取り下げ」**。残すことで、

- 「自分が何をやめると決めたか」が見える
- マイグレーションを 3 回繰り返した結果、削った歴史が見える
- これも intentionality のエビデンス

### 状態遷移図（実装向け）

```
              create
                │
                ▼
        ┌─────────────┐
        │  OPEN (・)  │
        └─────────────┘
                │
   ┌────────────┼─────────────┬──────────────┐
   │            │             │              │
   ▼            ▼             ▼              ▼
COMPLETED    MIGRATED      SCHEDULED      IRRELEVANT
   (×)         (>)           (<)         (~~・~~)
   │            │             │              │
   │            ▼             ▼              │
   │   create new OPEN   create new OPEN     │
   │      in next        in Future Log       │
   │       month                             │
   │                                         │
   └─── 終端 ────────────────────────── 終端 ─┘
```

注意: Migrated / Scheduled は「**元のタスクの状態**」を変えると同時に、**新しいタスクが別のページに生まれる**。データモデル上、これは「親子関係（lineage）」を持つ。

### データモデルへの示唆

```
Bullet {
  id          UUID
  type        TASK | EVENT | NOTE
  status      OPEN | COMPLETED | MIGRATED | SCHEDULED | IRRELEVANT (TASK のみ)
  content     short text
  signifiers  [PRIORITY, INSPIRATION, EXPLORE, ...]
  logId       FK to DailyLog or MonthlyLog or FutureLog or Collection
  createdAt   timestamp
  completedAt timestamp?  -- COMPLETED の時刻
  migratedToBulletId  FK?  -- Migrate 先の新規 Bullet
  scheduledToBulletId FK?  -- Schedule 先の新規 Bullet
  sourceBulletId      FK?  -- Migrate / Schedule の元
}
```

「移行先と元」の双方向リンクを保つことで、後述する Migration の履歴トラッキングが可能になる（→ `06-migration-and-reflection.md`）。

---

## 4.3 Event の状態は基本的に変わらない

Event は「起きる予定」「起きた」のいずれも `○` のままで OK。ただし運用上、以下の派生がある（公式の明示ではないが、コミュニティ標準）。

| 表記 | 意味 |
|---|---|
| `○` | 通常の Event |
| `●` または `○` 塗りつぶし | 「終わった」「過ぎた」を明示 |
| `~~○~~` | キャンセルされた |

公式は「Event は予定でも事後でも `○`」と言うのみで、完了マークは推奨していない。コミュニティ慣習として塗りつぶしを使うパターンが多い。

---

## 4.4 Note の状態は変わらない（が、格上げできる）

Note は基本的にステータスを持たない。`–` のままで、追記される。

ただし、Note を読み返したときに「これ、行動に変えるべきだ」と気づくことがある。その場合は、

- Note の前に小さく `・` を追加して Task に格上げ
- もしくは、新しい Daily Log に Task として書き直し（こちらが推奨）

例:
```
（先週の Note）
– 確定申告の書類が必要

（今週の Daily Log）
・ 確定申告の書類を集める
```

---

## 4.5 公式 Signifier — 3 種類

Signifier は、Bullet の **左側**に置く小さな補助記号。Bullet が「何のエントリか」を示すのに対し、Signifier は「**そのエントリのコンテキスト**」を示す。

公式が推奨する Signifier は 3 つ[^rl-faq]。

### `*` Priority — 優先度

> "Used to give a Task priority. Placed left of bullets for quick scanning."[^rl-faq]

**意味**: 「これは特に重要」「他より先にやる」というマーキング。

**例**:
```
*・ クライアントへのレポート提出
・ 牛乳を買う
*・ 体調不良の Andreas に連絡
```

ページ全体を見て、`*` がついている行が「今日の本丸」だと分かる。

**運用ルール（公式）**: `*` の数は **少なめにする**。10 個中 8 個に `*` をつけたら、それは Signifier として機能しない。Carroll は「1 日 1〜3 個」を目安として書籍で示している（=帕累托 80/20 的）。

### `!` Inspiration — ひらめき

> "Most commonly paired with a Note for capturing valuable ideas and insights."[^rl-faq]

**意味**: 「これはアイデアだ」「ひらめいた」を示すマーキング。

**例**:
```
!– React Server Components で書き直す案
!– 子供と一緒に料理を学ぶプロジェクトはどうか
!– このメソッドを使えば、社内に展開できるかも
```

Note との組み合わせが多い。後で見返したとき、`!` がついている行は「過去の自分の閃き」で、新しい Custom Collection の種になる。

### `?` または 👁 Explore — 要調査

> "Used when there is something that requires further research, information, or discovery."[^rl-faq]

**意味**: 「これは追加で調べる必要がある」「未確定」というマーキング。

**例**:
```
?– React Server Components のメモリ消費
👁– Andreas の言っていた論文を探す
?・ 確定申告の電子化要件
```

公式サイトは「目のアイコン（eye）」を示す版と「`?`」を示す版の両方が見られる。コミュニティでは `?` が主流。

### Signifier は **Bullet と組み合わせる**

これがポイント。Signifier 単独では使わない。常に Bullet の左隣に置く。

```
正しい:    *・ クライアントへのレポート
正しい:    !– アイデア: ABC
正しい:    ?○ 来週の会議は本当に必要か

間違い:    * クライアントへのレポート      ← Bullet がない
```

---

## 4.6 カスタム Signifier — コミュニティ拡張

Carroll は「3 つを基本とし、必要なら自分で追加せよ」と書籍で許可している。コミュニティで一般化しているカスタム Signifier:

| 記号 | 意味 |
|---|---|
| `$` | 金銭・支出に関わる |
| `♡` | 個人的・感情的 |
| `☆` | 目標・夢 |
| `@` | 場所 / 人物 |
| `▲` | 緊急 |
| `□` | 仕事関連 |
| `✈` | 旅行関連 |

ただし、**カスタム記号を増やすことは諸刃の剣**である。

**メリット**:
- 自分のコンテキストに即した分類が可能
- ページのスキャナビリティ向上

**デメリット**:
- 記号が増えると判別コストが増える（Hick の法則）
- 続かなくなる
- Key（凡例）ページを毎回参照する必要が出る

Carroll の公式アドバイス[^back-to-basics]:

[^back-to-basics]: <https://bulletjournal.com/blogs/bulletjournalist/back-to-the-basics>

> "Your practice doesn't need to be profound, or beautiful. It needs to be real, relevant, and effective."

つまり、**「効果のある分だけ追加し、機能しないものは捨てる」**。

### Key ページ（凡例ページ）

カスタム Signifier を使うなら、ノートの最初のページに「Key（凡例）」ページを作るのが慣例。

```
─── KEY ───────────────────────────
BULLETS:
・  Task
○  Event
–  Note

STATES:
×  Complete
>  Migrated
<  Scheduled
~~ Irrelevant

SIGNIFIERS:
*  Priority
!  Inspiration
?  Explore
$  Money
♡  Personal
☆  Goal
```

これにより、「3 か月後の自分」「他人」が読んでも記号の意味が伝わる。

---

## 4.7 「Sub-Task」「Sub-Note」— インデント

公式は明示的な階層構造を勧めていないが、コミュニティでは**インデント**による親子関係を使う。

```
・ プロジェクト A をローンチする
  ・ ランディングページのコピーを書く
  ・ デザインレビューを依頼する
  – 注意: 法務確認が必要
```

これは Carroll の動画でも示唆されており、特に書籍では明示的には禁止していない。実装上、Tree 構造を持つかどうかは選択になる。

---

## 4.8 状態遷移の運用例 — 1 つのタスクの 4 か月ライフサイクル

```
[5/24 Daily Log - p.42]
・ 確定申告の準備
   ↓ （その日は手を付けず）

[5/25 Daily Log - p.43]
（書き写されず、p.42 に残ったまま）
   ↓ （翌週も着手せず）

[5/31 月末レビュー]
・ → > に変更（migrate）
[6 月の Monthly Log Task Page - p.51]
・ 確定申告の準備   ← 新規にここに書く

   ↓ （6 月も着手せず、月末レビュー）

[6/30 月末レビュー]
6 月 Task Page で `・` のまま残っている
→ もう一度問う:
  - 必要か？ → 必要
  - 今やる時期か？ → いや、2 月でいい
→ < に変更（schedule）

[Future Log の 2 月の枠 - p.6]
・ 確定申告の準備   ← Future Log に書く

   ↓ （2 月になる）

[2/1 Monthly Log セットアップ時]
Future Log から書き写す
[2 月の Monthly Log Task Page]
・ 確定申告の準備

   ↓ 着手して完了

[2/5 Daily Log]
× 確定申告の準備
```

このように、1 つのタスクが何ヶ月にもわたって状態を変えながら、書き写されていく。デジタル化では「lineage（系譜）」を保持できるかが論点。

---

## 4.9 アンチパターン — 記号体系の壊し方

### 1. 完了タスクを消去する

`×` を書くのではなく、行ごと消去してしまうケース。

→ **問題**: 「今日何を達成したか」が失われ、自己肯定感の機会が消える。Carroll は「完了したタスクは横線でなく `×`」と明示している。

### 2. Migration をスキップする

未完了タスクを放置し続けるパターン。

→ **問題**: ノイズが溜まり、ノート全体が読みづらくなる。意図性の機構が機能しない。

### 3. すべてのタスクに `*` を付ける

優先度の Signifier をデフォルトのように使うケース。

→ **問題**: 「特別」マーカーが「普通」になり、機能しない。

### 4. 記号を増やしすぎる

カスタム Signifier を 10 個以上に増やす。

→ **問題**: 判別コストが増え、Rapid Logging が遅くなる。続かない。

### 5. Note と Task を混同する

「読みたい本」を `・` で書く、「会議参加」を `–` で書く、等。

→ **問題**: 後で振り返ったとき、何が行動可能で何がそうでないか判別できない。

---

## 4.10 デジタル化の含意

### 状態遷移はそのまま enum + Finite State Machine になる

データモデル例:

```typescript
enum BulletType {
  TASK
  EVENT
  NOTE
}

enum TaskStatus {
  OPEN
  COMPLETED
  MIGRATED
  SCHEDULED
  IRRELEVANT
}

enum Signifier {
  PRIORITY     // *
  INSPIRATION  // !
  EXPLORE      // ?
  // カスタム拡張は string で持つ案も
}
```

### 視覚的アンカーをどう再現するか

紙の `・` `○` `–` の「視覚的差別化」は、デジタル UI で 3 種類のアイコン or 色で再現する必要がある。

例:
- Task: `□` 中空のチェックボックス（完了で `■` または `✓`）
- Event: 時計アイコン or `○`
- Note: 横棒アイコン or `–`

### Migration を「儀式」にする UI

`>` の状態は、「単純な next month transfer」ではなく、ユーザーに**意思決定を強制する**ステップとして実装する必要がある。

詳細は `10-digital-implementation-considerations.md`。

### カスタム Signifier をどう許すか

- 完全自由（任意の絵文字）
- プリセット選択
- 制限付き拡張

ここはアプリのターゲットユーザーで判断が変わる。

---

## 4.11 まとめ

1. Bullets は **3 種**（`・` `○` `–`）。最小判別単位で意思決定を高速化
2. Task は **5 状態**（OPEN / COMPLETED / MIGRATED / SCHEDULED / IRRELEVANT）
3. Signifier は **3 種類が公式**（`*` `!` `?`）、カスタム拡張可だが慎重に
4. 状態遷移は「**書き写し**」を伴う。デジタルでも lineage を保持できると BuJo らしさが残る
5. データモデルは enum + FSM で素直に書ける

次は `05-collections.md` で、これらの Bullet が**どこに配置されるか**（Index / Future / Monthly / Daily / Custom）の構造を見る。

