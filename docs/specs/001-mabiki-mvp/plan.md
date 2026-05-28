# Implementation Plan: Mabiki MVP

**Feature ID**: 001
**対応 spec**: [spec.md](./spec.md)
**Status**: 確定（spec §7 Clarifications 解消済み）
**最終更新**: 2026-05-29

> 本ドキュメントは **How** を扱う。What/Why は [spec.md](./spec.md) を参照。

---

## 1. 仕様分析サマリ

[spec.md](./spec.md) の検証対象は2つのコア・インタラクション（捨てる / 移行する）と、それらを束ねるイブニングリチュアル。MVP の主眼は **「摩擦の触感」のドッグフーディング検証** であり、機能網羅より体験の質を優先する。

## Constitution Check

→ 判定基準は [docs/guides/constitution.md](../../guides/constitution.md)。本計画が Mabiki 憲法の各原則に整合しているかをチェックする。

| # | 原則 | 判定 | 根拠 / 備考 |
|---|---|---|---|
| 1 | Friction is a Feature | ✅ OK | spec §5 で摩擦が中核機能として設計（自動繰り越しなし・コピペ禁止・演出付き削除）。本書 §6 でその実装方針を踏襲 |
| 2 | The Why First | ✅ OK | spec §2「背景・狙い」と §5 摩擦設計表の「狙い」列で各機能の Why を明記 |
| 3 | Grounded in BuJo Research | ✅ OK | spec §5「摩擦設計の研究根拠」サブセクションで `docs/research/02, 06, 10` への根拠リンクを整備済み |
| 4 | Decision-Point Friction | ✅ OK | 摩擦は移行・削除・振り返りの意思決定点に集約。完了操作（US-1）は本書 §5 `completeTask` で摩擦なし |
| 5 | Mobile-First | ✅ OK | spec §6「スマホファースト」、本書 §2 で Next.js (App Router) を採用しスマホ縦持ちを最優先 |
| 6 | Explicit Non-Goals | ✅ OK | spec §3 Out of Scope に自動繰り越し・一括移動・D&D・コピペ移行を明示禁止。本書はそれらを実装しない |
| 7 | No Speculative Friction | ✅ OK | spec §5 のすべての摩擦に Why と研究根拠が紐づく。本書で追加の摩擦を投機的に足していない |
| 8 | Don't Guess, Ask or Clarify | ✅ OK | spec §7 の Clarifications 6 件はすべて解消済み。本書 §3 の設計判断もそれに準拠 |

逸脱なし。Implement（Issue 化）に進んでよい状態。

## Constitution Check

→ 判定基準は [docs/guides/constitution.md](../../guides/constitution.md)。本計画が Mabiki 憲法の各原則に整合しているかをチェックする。

| # | 原則 | 判定 | 根拠 / 備考 |
|---|---|---|---|
| 1 | Friction is a Feature | ✅ OK | spec §5 で摩擦が中核機能として設計（自動繰り越しなし・コピペ禁止・演出付き削除）。本書 §6 でその実装方針を踏襲 |
| 2 | The Why First | ✅ OK | spec §2「背景・狙い」と §5 摩擦設計表の「狙い」列で各機能の Why を明記 |
| 3 | Grounded in BuJo Research | ✅ OK | spec §5「摩擦設計の研究根拠」サブセクションで `docs/research/02, 06, 10` への根拠リンクを整備済み |
| 4 | Decision-Point Friction | ✅ OK | 摩擦は移行・削除・振り返りの意思決定点に集約。完了操作（US-1）は本書 §5 `completeTask` で摩擦なし |
| 5 | Mobile-First | ✅ OK | spec §6「スマホファースト」、本書 §2 で Next.js (App Router) を採用しスマホ縦持ちを最優先 |
| 6 | Explicit Non-Goals | ✅ OK | spec §3 Out of Scope に自動繰り越し・一括移動・D&D・コピペ移行を明示禁止。本書はそれらを実装しない |
| 7 | No Speculative Friction | ✅ OK | spec §5 のすべての摩擦に Why と研究根拠が紐づく。本書で追加の摩擦を投機的に足していない |
| 8 | Don't Guess, Ask or Clarify | ⚠️ 逸脱（暫定） | spec §7 に `[NEEDS CLARIFICATION]` が 6 件残存。本書 §3 で暫定既定値として提案中。Implement（Issue 化）着手前に必ず解消する |

### 逸脱の正当化

**原則 8（Don't Guess）の暫定逸脱**

- 逸脱内容: spec.md §7 の Clarifications 6 件が未解消の状態で plan を作成
- 理由: plan の暫定既定値を提示することで Clarifications の議論材料を提供するため（本書 §3 は「決定」ではなく「提案」）
- 代替案を検討した結果: Clarifications を先に解消する正攻法もあるが、提案ベースのほうが選択肢が具体化し議論しやすいと判断
- 受容するトレードオフ: 本計画の確定は Clarifications 解消後にずれる。**Issue 化（Implement 着手）前に必ず Clarifications を解消し、本 Check を更新する**

## 2. 技術スタック

| 領域 | 採用 | 備考 |
|---|---|---|
| Frontend | Next.js (App Router), TypeScript | スマホファースト |
| アニメーション | Framer Motion | 「切り落とし」演出（`AnimatePresence`） |
| Backend / DB | Prisma + PostgreSQL | |
| コンポーネント QA | Storybook, Chromatic | 摩擦の触感を単体で検証 |

## 3. 設計判断（spec §7 Clarifications の解消結果）

| 項目 | 採用 | 根拠 |
|---|---|---|
| 削除方式 | **論理削除（`deletedAt` で隠す）** | UI 上は復元不可（潔さ）、DB には痕跡を残してトラブルシュートと将来分析の余地を残す |
| 移行の一致判定 | **完全一致は強制しない**（納得した内容で確定可） | コピペ禁止＋手動再入力で摩擦は成立。完全一致強制は「自分の言葉で再構築する」効果を阻害 |
| イブニングリチュアル | 起動は **任意** | MVP は体験検証が目的。強制度はドッグフーディング後に判断（Kaizen） |
| ユーザー/認証 | **シングルユーザー** | `userId` 列は将来のために保持。認証機構は組まない |
| バレット種別 | **Task のみ** | コア摩擦の検証に種別は不要 |
| 移行先 | 常に **今日** | 任意日付への先送りは意思決定の先送りを許す（Constitution 原則 1 と矛盾） |

> 各項目の詳細根拠は [spec.md §7](./spec.md) を参照。

## 4. データモデル

PostgreSQL / Prisma。シングルユーザー・Task のみ・論理削除（`deletedAt`）の確定値に沿う。

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
  deletedAt DateTime?   // 「捨てる（Mabiki）」で設定。UI 上は復元不可

  // 移行の追跡（移行先が移行元を参照）
  sourceTaskId String?  @unique
  sourceTask   Task?    @relation("MigrationHistory", fields: [sourceTaskId], references: [id])
  migratedTo   Task?    @relation("MigrationHistory")

  @@index([userId, date])
  @@index([deletedAt])
}
```

> すべての読み取りクエリには `deletedAt: null` 条件を必ず付ける。共通の Prisma extension またはリポジトリ層で強制する（実装時 Issue で詰める）。

## 5. API 設計（Next.js App Router / Server Actions 想定）

| エンドポイント / アクション | 概要 | 主な条件・処理 |
|---|---|---|
| `getDailyLog(date)` | 指定日の表示用タスク一覧 | `userId == current && date == 指定日 && deletedAt == null` |
| `getPendingForRitual()` | 前日以前の未完了タスクを古い順に | `date < 今日 && status == INCOMPLETE && deletedAt == null` |
| `migrateTask(id, content)` | 移行（トランザクション） | ①元タスク `status = MIGRATED` ②新タスクを `date=今日, status=INCOMPLETE, sourceTaskId=元id` で作成 |
| `completeTask(id)` | 完了 | `status = COMPLETE`（摩擦なし） |
| `deleteTask(id)` | 捨てる（Mabiki） | `deletedAt = now()`（論理削除）。UI から復元手段は提供しない |

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

「捨てる」時、`AnimatePresence` を使い、要素が斜めにスライドしながらフェードアウト（ハサミで切られて落ちる）する。演出完了時に `deleteTask` を呼ぶ。

### 6.3 モード分離

「日中の消化モード（デイリーログ）」と「振り返りモード（イブニングリチュアル）」を画面・遷移として分離し、精神的切り替えを促す。

## 7. 実装前ゲート

- **Simplicity**: MVP は単一の Next.js プロジェクトに閉じる。マイクロサービス化・過剰な抽象化をしない。
- **Anti-Abstraction**: Prisma / Framer Motion を直接使い、独自ラッパーを早期に作らない。
- **検証優先**: コア摩擦（再入力・切り落とし）の触感を **Storybook で先に確認** してから DB 結線する。

## 8. フェーズ内訳（→ 個別タスクは GitHub Issue で管理）

- **Sprint 1**: コア・インタラクションの検証（UIのみ、`useState` モック、DB なし）
- **Sprint 2**: 振り返り画面のプロトタイプ（Prisma 結線、最小フロー）

> Mabiki では `tasks.md` を持たず、本書から **GitHub Issue** を直接切り出してタスクを管理する。Issue 作成は `.claude/skills/creating-github-issue` を使い、本書 §4〜§7 の内容を Issue 本文に反映する。運用は [SDD ガイド](../../guides/spec-driven-development.md) を参照。

## 9. 付随ドキュメント（必要に応じて追加）

- `data-model.md` — 確定後のスキーマ詳細（現状は本書 §4 に内包）
- `contracts/` — Server Actions / API の入出力契約
- `research.md` — ペースト抜け道調査、Framer Motion 演出比較 など
