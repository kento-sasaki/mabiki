# Implementation Plan: Mabiki MVP

**Feature ID**: 001
**対応 spec**: [spec.md](./spec.md)
**Status**: Draft（spec.md の Clarifications 解消後に確定）
**最終更新**: 2026-05-25

> 本ドキュメントは **How** を扱う。What/Why は [spec.md](./spec.md) を参照。

---

## 1. 仕様分析サマリ

[spec.md](./spec.md) の検証対象は2つのコア・インタラクション（捨てる / 移行する）と、それらを束ねるイブニングリチュアル。MVP の主眼は **「摩擦の触感」のドッグフーディング検証** であり、機能網羅より体験の質を優先する。

> ⚠️ spec.md の **Clarifications が未解消**。本計画は暫定の既定値（下記「設計判断」に明記）で記述しており、確定後に更新する。

## 2. 技術スタック

| 領域 | 採用 | 備考 |
|---|---|---|
| Frontend | Next.js (App Router), TypeScript | スマホファースト |
| アニメーション | Framer Motion | 「切り落とし」演出（`AnimatePresence`） |
| Backend / DB | Prisma + PostgreSQL | |
| コンポーネント QA | Storybook, Chromatic | 摩擦の触感を単体で検証 |

## 3. 設計判断（暫定の既定値 — Clarifications 解消で確定）

| 項目 | 暫定既定値 | 根拠 | 対応する Clarification |
|---|---|---|---|
| 削除方式 | MVP は **物理削除**（最シンプル） | 旧 02 ドラフト推奨。復元不可の潔さを最短で実現 | 削除の方式 |
| 移行の一致判定 | **完全一致は強制しない**（納得した内容で確定可） | 入力の自由度を残しつつ「打ち直し」摩擦は維持 | 移行の一致判定 |
| イブニングリチュアル | 起動は **任意**（強制動線は後続スパン） | MVP は体験検証が目的。強制度はUX検証後に判断 | リチュアルの強制度 |
| ユーザー/認証 | **シングルユーザー**（ドッグフーディング） | `userId` 列は持つが認証は組まない | ユーザー/認証 |
| バレット種別 | MVP は **Task のみ** | コア摩擦の検証に種別は不要 | バレット種別 |
| 移行先 | 常に **今日** | 日付指定は後続 | 移行先の指定 |

> これらは **plan の提案であって決定ではない**。spec.md レビュー時に確定すること。

## 4. データモデル（暫定）

PostgreSQL / Prisma。シングルユーザー・Task のみ・物理削除の暫定既定値に沿う。

```prisma
enum TaskStatus {
  INCOMPLETE   // 未完了
  COMPLETE     // 完了
  MIGRATED     // 移行済み（移行元がこの状態になり編集不可）
}

model Task {
  id        String     @id @default(cuid())
  userId    String     // シングルユーザーでも将来に備え保持
  content   String      // タスク内容
  status    TaskStatus @default(INCOMPLETE)
  date      DateTime    // どの日のログに属するか (YYYY-MM-DD)
  createdAt DateTime   @default(now())
  updatedAt DateTime   @updatedAt

  // 移行の追跡（移行先が移行元を参照）
  sourceTaskId String?  @unique
  sourceTask   Task?    @relation("MigrationHistory", fields: [sourceTaskId], references: [id])
  migratedTo   Task?    @relation("MigrationHistory")

  @@index([userId, date])
}
```

> **削除を「物理削除」にする場合** `deletedAt` は不要。spec の Clarification で「一方向の論理削除」に決まったら `deletedAt DateTime?` を追加し、クエリに `deletedAt == null` 条件を足す。

## 5. API 設計（Next.js App Router / Server Actions 想定）

| エンドポイント / アクション | 概要 | 主な条件・処理 |
|---|---|---|
| `getDailyLog(date)` | 指定日の表示用タスク一覧 | `userId == current && date == 指定日` |
| `getPendingForRitual()` | 前日以前の未完了タスクを古い順に | `date < 今日 && status == INCOMPLETE` |
| `migrateTask(id, content)` | 移行（トランザクション） | ①元タスク `status = MIGRATED` ②新タスクを `date=今日, status=INCOMPLETE, sourceTaskId=元id` で作成 |
| `completeTask(id)` | 完了 | `status = COMPLETE`（摩擦なし） |
| `deleteTask(id)` | 捨てる（Mabiki） | 物理削除 `db.task.delete()`（暫定既定値） |

## 6. フロントエンド実装のポイント

### 6.1 コピペ禁止（ペーストハック）

移行用入力コンポーネントで `onPaste` をインターセプトして無効化する。

```tsx
<input
  type="text"
  value={value}
  onChange={(e) => setValue(e.target.value)}
  onPaste={(e) => {
    e.preventDefault(); // コピペを無効化
    // 任意: 「手書きの摩擦を大切に」的な微細なヒント
  }}
  placeholder="過去のタスクを見ながらタイピングしてください"
/>
```

> 注意: `onPaste` だけでなく、ドラッグ&ドロップ／IME 経由／モバイルの長押し貼り付け等の抜け道も検証対象。MVP では主要経路（Cmd/Ctrl+V）のブロックを最低ラインとする。

### 6.2 切り落とし演出（Framer Motion）

「捨てる」時、`AnimatePresence` を使い、要素が斜めにスライドしながらフェードアウト（ハサミで切られて落ちる）する。

### 6.3 モード分離

「日中の消化モード（デイリーログ）」と「振り返りモード（イブニングリチュアル）」を画面・遷移として分離し、精神的切り替えを促す。

## 7. 実装前ゲート

- **Simplicity**: MVP は単一の Next.js プロジェクトに閉じる。マイクロサービス化・過剰な抽象化をしない。
- **Anti-Abstraction**: Prisma / Framer Motion を直接使い、独自ラッパーを早期に作らない。
- **検証優先**: コア摩擦（再入力・切り落とし）の触感を **Storybook で先に確認** してから DB 結線する。

## 8. フェーズ内訳（→ 詳細は [tasks.md](./tasks.md)）

- **Sprint 1**: コア・インタラクションの検証（UIのみ、`useState` モック、DB なし）
- **Sprint 2**: 振り返り画面のプロトタイプ（Prisma 結線、最小フロー）

## 9. 付随ドキュメント（必要に応じて追加）

- `data-model.md` — 確定後のスキーマ詳細（現状は本書 §4 に内包）
- `contracts/` — Server Actions / API の入出力契約
- `research.md` — ペースト抜け道調査、Framer Motion 演出比較 など
