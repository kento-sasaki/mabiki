# 07. Threading とインデックス — アナログにおける検索性の仕組み

## このドキュメントで分かること

- Threading（スレッディング）の正確な仕様
- Index と Threading の使い分け
- なぜアナログでこの仕組みが必要だったのか
- デジタル化で何に置き換わり、何が変わるか

**想定読者**: コレクション間のナビゲーション UI を設計する人。検索／タグ／関連リンクの実装方針を決める人。

---

## 7.0 結論先出し

```
INDEX:     「全コレクションを一覧化する」装置
THREADING: 「同じトピックの離れたページを繋ぐ」装置

両者は補完関係。
- Index: 縦の一覧（全体俯瞰）
- Threading: 横のリンク（個別ジャンプ）

デジタル化では:
- Index → 全文検索 + タグ + コレクション一覧
- Threading → 自然なハイパーリンク
```

---

## 7.1 アナログの宿命 — ページは時系列に進む

BuJo はノート 1 冊で運用するため、**ページの順序は時系列**になる。以下のような状況が日常的に起きる:

```
p.12   Custom Collection: "Project X"
p.13-25  Daily Logs (5/1 〜 5/13)
p.26   Custom Collection: "Books to Read"
p.27-40  Daily Logs (5/14 〜 5/27)
p.41   Custom Collection: "Project X" (続き) ← p.12 の続き
p.42-50  Daily Logs (5/28 〜 6/5)
```

ここで問題は:

- 「Project X」のメモが p.12 と p.41 に分散している
- 物理ノートではページの間に挟むことができない
- でも「Project X」を見たいときは、両方のページを開きたい

この問題を解くのが **Index** と **Threading** である。

---

## 7.2 Index による解決

### 仕組み

すでに `05-collections.md` で詳述した通り、Index はノートの最初の数ページに置く目次。Project X が複数ページに散らばっているなら、Index に:

```
Project X . . . . . . . . . . . . . 12, 41
```

と書けば、その 2 ページに直接ジャンプできる。

### Index だけでは足りない理由

- 増えると Index 自体が肥大化する
- Daily Log を毎日 Index に書くのは現実的でない
- 「次のページ」「前のページ」のナビゲーションができない

これを補うのが Threading。

---

## 7.3 Threading の仕組み

### 公式の指示

> "Threading is when you link a collection's previous and next instances via the page numbers at the bottom of the page."[^thread-medium]

[^thread-medium]: Dan Johnson, "Basic Page Threading in Bullet Journals," Medium. <https://medium.com/@rdj/basic-page-threading-in-bullet-journals-bujo-95dfce39bfef>

### 基本パターン

Project X が p.12 と p.41 にあるとき:

```
[p.12]
─────────────────────────────────────
       PROJECT X
─────────────────────────────────────
（内容）
                              p.12 → 41
─────────────────────────────────────
```

```
[p.41]
─────────────────────────────────────
       PROJECT X (続き)
─────────────────────────────────────
（内容）
                         12 ← p.41
─────────────────────────────────────
```

p.12 の下端に「**次は p.41 にある**」と書き、p.41 の下端に「**前は p.12 にある**」と書く。これで両ページを行き来できる。

### 多重スレッドの例

3 ページに分散する場合:

```
[p.12]: ... → 41
[p.41]: 12 → 78
[p.78]: 41 → (続く場合は次のページ番号)
```

数珠つなぎ。これにより、Index には先頭の `p.12` だけ記録すればよく、後は Threading が連れて行く。

### 公式が言及する利点

> "Another benefit of using threading is that you don't need to update your index again."[^debit-thread]

[^debit-thread]: <https://www.debitthiscreatethat.com/navigating-your-bullet-journal-migration-scheduling-and-threading/>

新しいページを追加するたびに Index を更新する必要がなく、前のページに `→ 78` と書き足すだけで済む。

---

## 7.4 Threading の派生パターン

### パターン 1: 単一トピックの連続

最もシンプル。同じ Custom Collection を複数ページに渡って展開:

```
[p.12] Project X (start)         → 41
[p.41] Project X (continued)  12 → 78
[p.78] Project X (continued)  41 → 103
[p.103] Project X (final)    78 →
```

### パターン 2: Calendar Thread と Topic Thread の併存

ある記事[^thread-medium]では、Threading を 2 系統に分けて運用する案を紹介している。

```
[CALENDAR THREAD]
Future Log → Jan Monthly → Feb Monthly → ...
Each Daily Log connects to the previous and next Daily Log

[TOPIC THREAD]
Project X → Project X → Project X
Books to Read → Books to Read → ...
```

2 系統が同じノートで独立に走り、互いに干渉しない。

### パターン 3: クロスリファレンス（任意のリンク）

Threading は「同じトピックの連続」だけでなく、「**関連するページ同士のリンク**」としても使える。

```
[p.34 Daily Log 5/20]
・ Andreas からのフィードバック対応
   → p.14 (Project X) で議論された件
```

これは Note や Task の中に直接ページ番号を書くスタイル。Wiki のハイパーリンクに近い。

---

## 7.5 Index と Threading の使い分け

```
┌──────────────────────┬──────────────────────┐
│        INDEX         │       THREADING      │
├──────────────────────┼──────────────────────┤
│ 用途:                │ 用途:                │
│ コレクションの俯瞰   │ 個別ページ間の移動   │
│                      │                      │
│ 場所:                │ 場所:                │
│ ノート先頭の数ページ │ 各ページの下端や行内 │
│                      │                      │
│ 更新頻度:            │ 更新頻度:            │
│ 新規 Collection 作成時 │ 新規ページ追加時    │
│                      │                      │
│ メリット:            │ メリット:            │
│ 全体が見える         │ Index を肥大化させない│
│                      │                      │
│ デメリット:          │ デメリット:          │
│ 多くなると見づらい   │ 最初のページ位置は    │
│                      │ Index 等で別途必要    │
└──────────────────────┴──────────────────────┘
```

両者を組み合わせるのが標準。

---

## 7.6 アナログだから必要だった理由

### 紙ノートの制約

- ページは追加できない（順序を変えられない）
- 同じトピックを連続で書けない（途中で別の日のログが入る）
- 全文検索できない
- フォルダで整理できない

この制約に対する**回避策**が Threading と Index。

### デジタルではほぼ不要

デジタルなら:

- データベースで同じ `collectionId` の Bullet を一括取得できる（Threading 不要）
- 全文検索ができる（Index 不要）
- タグやカテゴリで分類できる（Index 拡張）
- フィルタとソートが自由（順序の制約なし）

つまり、**Index と Threading は「アナログだから必要だった」装置**であり、デジタル化では多くが自動化される。

---

## 7.7 ただし — デジタルでも残すべきもの

### 1. コレクション一覧のビュー

Index の主機能「ノート全体の俯瞰」は、デジタルでも残すべき。検索だけだと「自分が何を持っているか」が把握できない。

例: アプリの「Collections」タブで、全 Custom Collection を一覧表示する。

### 2. ハイパーリンクによる Threading

紙の「p.12 → p.41」は、デジタルではハイパーリンクで自然に再現できる。

例: Daily Log のエントリ内で「Project X」に言及したら、自動的にそのコレクションへリンクが張られる（Roam Research や Obsidian のように）。

### 3. 「過去のページに戻る」体験

紙のノートをパラパラめくる体験は、UX として価値がある。デジタルでも「カレンダー UI で過去の日に戻る」「タイムラインで遡る」体験は欠かさないべき。

### 4. Threading の系譜（Migration の lineage）

Migration で `>` を付けて新しいページに書き写したとき、紙では「元のページ → 新しいページ」というリンクが暗黙的にある（マイグレーションした人の頭の中でだけ繋がっている）。デジタルでは、これを明示的に lineage として保持できる。

```
[Bullet A in May Task Page] >  -- migrated to -->  [Bullet A' in June Task Page]
                                                      ↑
                                                   sourceBulletId = A
```

これがあると、「このタスク、過去に何回 migrate されたか」を辿れる（→ 3 回ルールの自動検出）。

---

## 7.8 公式の Threading 表記バリエーション

### バリエーション 1: 行内番号

```
[p.12] Project X
       → 続き p.41
                              p.12
```

### バリエーション 2: ページ下端の数字

```
[p.12] Project X
       (内容)
                              12 → 41
```

### バリエーション 3: 矢印を使う

```
[p.41] Project X
       (内容)
                              ← 12 | 41 | 78 →
```

公式の動画では、矢印スタイルがよく使われる。

### バリエーション 4: Index に複数ページを書く

Threading を使わず、Index に全ページを書く方式:

```
Project X . . . . . 12, 41, 78, 103
```

短期間で終わる Custom Collection ではこれで十分。

---

## 7.9 Threading のスケーラビリティ問題

### 限界

> "The basic threading approach becomes cumbersome with extensive page collections."[^thread-medium]

ページが何百もある Daily Log を Threading で繋ぐと、

- 各ページに「前 / 次」を書く手間が膨大
- 古いノートに新しいノートのページ番号を書けない
- 結局 Index に頼ることになる

つまり、Threading は「**1 つのトピックが数ページに渡る**」程度のケースで効くが、「**1 年分の Daily Log を繋ぐ**」ようなケースには向かない。

### コミュニティでの解決策

- **Daily Log は Threading しない**（毎日の連続として暗黙的に繋がっているとみなす）
- **Custom Collection だけ Threading する**
- **大きなトピックは独立した Custom Collection ノートに分離する**

---

## 7.10 デジタル化の含意

### 検索 + タグ + 関連リンクの三位一体

Index と Threading の機能を、デジタルでは以下に分散する:

| アナログの機能 | デジタルでの実装 |
|---|---|
| Index（コレクション一覧） | コレクション一覧画面、サイドバー |
| Index（探したいものへの到達） | 全文検索、タグ検索 |
| Threading（同トピックの連続） | コレクションへの自動グルーピング |
| Threading（任意のリンク） | ハイパーリンク、`@mention`、`#tag` |
| Threading（系譜の保持） | データモデルの `lineage` フィールド |

### Backlink 表示

Roam Research や Obsidian で発達した「Backlink」は、BuJo の Threading の延長として理解できる。あるコレクションを開いたとき、「**ここを参照している他のページ**」が自動で表示される。

これは Threading よりも強力（双方向のリンクが自動的に張られる）。

### ハイパーリンクの過剰問題

ただし、デジタルでハイパーリンクを過剰に張れる環境は、BuJo の哲学とは別の方向に進む可能性もある。

- すべてが繋がる → 焦点が分散する
- 全文検索できる → 見返す動機が薄れる
- 検索一発で見つかる → Index を書かなくなる、=見返さなくなる

BuJo の Index は「**書く瞬間に内容を再確認する**」装置でもあった。書き写しの摩擦と同様、これは「便利すぎる」と価値が消える。

→ デジタル化では、「**書く時に少し手間がかかる**」設計を意図的に残す選択がある。例えば:

- 新規コレクション作成時に「タグを必ず 1 つ付ける」
- 月末レビューで「過去 1 ヶ月の Custom Collection を一覧で見る」画面
- 「Index 画面」を視覚的に残す（=メニューじゃなくページとして）

---

## 7.11 まとめ

1. **Index** はコレクション全体の俯瞰、**Threading** は個別ページ間のリンク
2. 両者はアナログ特有の制約から生まれた装置
3. デジタル化では検索／タグ／ハイパーリンクで自動化されるが、**俯瞰の場と書く摩擦**は残す価値あり
4. Migration の lineage（書き写しの系譜）は、デジタルではむしろ Threading より強力に実装できる
5. ハイパーリンク過剰は別の罠を生む。意図性を保つ設計が重要

次は `08-tools-and-setup.md` で、実際の道具（ノート・ペン）と初期セットアップ手順を見る。

