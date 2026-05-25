# Bullet Journal (BuJo) 徹底調査ドキュメント

このディレクトリは、Ryder Carroll が考案した手書きノート術「Bullet Journal Method（以下、BuJo メソッド）」を、デジタル化アプリ開発の実装判断に耐えうる深さで体系化した調査ドキュメント群です。

**調査日**: 2026-05-24
**調査者向け前提**: 読者は「BuJo を題材にしたスマホアプリを開発するエンジニア／PdM」を想定しています。表面的な紹介ではなく、原典の方法論、思想、運用ルール、そして「アナログ前提の仕組みをデジタルに移すとき何が起きるか」までを扱います。

---

## このドキュメント群の読み方

### 推奨される読了順

| 順序 | ファイル | 内容 | 読了の意味 |
|---|---|---|---|
| 1 | [01-overview.md](./01-overview.md) | BuJo とは何か。起源、Ryder Carroll、書籍紹介、社会的普及 | 「これは何のシステムか」を掴む |
| 2 | [02-philosophy.md](./02-philosophy.md) | 思想・哲学（Intentionality、The Why、ストア哲学、マインドフルネス） | なぜこのシステムが「単なる ToDo 管理」と違うのか |
| 3 | [03-rapid-logging.md](./03-rapid-logging.md) | Rapid Logging（高速ログ）の方法論 | 日々の書き方の基本文法 |
| 4 | [04-signifiers-and-bullets.md](./04-signifiers-and-bullets.md) | 記号体系と状態遷移の完全な仕様 | データモデル設計の元ネタ |
| 5 | [05-collections.md](./05-collections.md) | Index / Future Log / Monthly Log / Daily Log / Custom Collections | 画面構造の元ネタ |
| 6 | [06-migration-and-reflection.md](./06-migration-and-reflection.md) | Migration、AM/PM Reflection、Monthly/Yearly Review | コア体験・コア時間の設計指針 |
| 7 | [07-threading-and-index.md](./07-threading-and-index.md) | Threading とインデックスによる検索性 | 関連付け・検索 UI の設計指針 |
| 8 | [08-tools-and-setup.md](./08-tools-and-setup.md) | 必要な道具、ノートの選び方、初期セットアップ | 物理側のリアリティを掴む |
| 9 | [09-community-and-variants.md](./09-community-and-variants.md) | Minimalist vs Decorative、コミュニティ文化 | ユーザー層の二極化を理解する |
| 10 | [10-digital-implementation-considerations.md](./10-digital-implementation-considerations.md) | **デジタル化の論点（最重要・最厚）** | 実装判断の主戦場 |
| 付 | [references.md](./references.md) | 参照した URL・書籍・記事の一覧 | 出典の追跡 |

### 急いでいる人のためのショートカット

- **BuJo を全く知らない**: `01` → `03` → `05` の順で最小理解
- **アプリ実装の判断に使いたい**: `02` → `06` → `10` の順で論点を掴む
- **データモデルを設計したい**: `04` → `05` → `07` の順で構造を抑える

---

## このドキュメントが守っている方針

1. **原典優先**: bulletjournal.com（公式）、Ryder Carroll の書籍 *The Bullet Journal Method*（Penguin Random House, 2018）、彼の TEDxYale トーク（2017）、彼の Medium 寄稿を一次情報とし、その他は二次情報として明示する。
2. **「BuJo メソッド（Ryder Carroll の方法論）」と「装飾系 BuJo 文化（コミュニティの派生）」を区別する**: 両者は別物であり、混同するとアプリの設計判断を誤る。
3. **推測と原典を区別する**: 原典に当たれない箇所は「公式の明示は確認できないが、コミュニティでは一般的に〜」と書く。
4. **記号は原語で扱う**: `・`（タスク）、`○`（イベント）、`–`（ノート）、`×`（完了）、`>`（マイグレート）、`<`（スケジュール）、`*`（プライオリティ）、`!`（インスピレーション）。
5. **デジタル化の含意を明記する**: 各セクションで「これをデジタルに置き換えるとどうなるか」の論点を末尾に置く。

---

## 用語の対応表

| 英語（原語） | 本ドキュメントでの日本語表記 | 補足 |
|---|---|---|
| Bullet Journal / BuJo | バレットジャーナル／BuJo | 略称も併用 |
| Rapid Logging | 高速ログ／Rapid Logging | 文中では原語併記 |
| Bullets | バレット／記号 | `・` `○` `–` の総称 |
| Signifiers | シグニファイア／補助記号 | `*` `!` 等 |
| Index | インデックス | 目次に相当 |
| Future Log | フューチャーログ | 半年〜1年先の予定 |
| Monthly Log | マンスリーログ | 月次ページ |
| Daily Log | デイリーログ | 日次ページ |
| Collection | コレクション／ページ単位の集まり | 一般化された概念 |
| Migration | マイグレーション／移行 | 月次・年次の書き写し |
| Threading | スレッディング | ページ番号でのリンク |
| Reflection | リフレクション／振り返り | AM/PM の習慣 |
| Intentionality | 意図性 | Ryder Carroll の中核概念 |

---

## 重要な免責

- BuJo は Ryder Carroll の登録商標です。商用アプリで「Bullet Journal」「BuJo」を製品名や核となる UI コピーに使う場合は、商標の確認と回避が必要になります（本ドキュメントは法的助言ではありません）。
- 引用は調査時点で公開されていた原典に依拠していますが、Carroll は記事や書籍で言い回しを更新することがあります。実装時には最新の bulletjournal.com を再確認してください。

