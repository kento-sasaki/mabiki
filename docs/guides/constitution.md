# Mabiki Constitution（憲法）

本ドキュメントは、Mabiki プロジェクトの **全 SDD アーティファクト（spec / plan / tasks / コード）を律する原則** を定める。Spec Kit のデフォルト憲法（Library-First / CLI Interface など）は Mabiki のプロダクト特性に合わないため、独自の原則を据える。

**最終更新**: 2026-05-29
**位置づけ**: [SDD ガイド](./spec-driven-development.md) の上位制約。spec/plan のレビュー時に必ず照合する。
**改訂**: 原則の追加・修正・削除はコミットメッセージで根拠を残す（個人開発のため軽量運用）。

---

## 目的

Mabiki は「デジタル摩擦」という繊細な体験価値を核とするプロダクトであり、**何を作るか以上に「なぜその不便さを残すのか」という意図の一貫性** が成否を分ける。この憲法は、その一貫性を全アーティファクトに対して強制するための **判断基準** である。

特定の条項を破る設計を採用する場合は、`plan.md` の Constitution Check セクションで **明示的に正当化** すること（破ること自体は禁止しないが、暗黙の逸脱は禁止）。

---

## 原則

各原則は **条項 / 根拠 / どこで効くか** の3点で記述する。

### 1. Friction is a Feature — 摩擦は機能である

**条項**
自動化・効率化を理由に摩擦を薄めてはならない。摩擦は副作用や妥協ではなく、Mabiki の中核機能である。

**根拠**
[research/02-philosophy.md §2.5](../research/02-philosophy.md) — Carroll が繰り返し述べる「The friction is a feature」。デジタル化で摩擦を消すことは「便利にしすぎて魂を抜く」罠への直行を意味する。

**どこで効くか**
- `plan.md` レビュー時、「実装の都合で摩擦を緩めていないか」を確認
- 「ユーザーが楽になる」を理由にした機能追加要求の足切り

---

### 2. The Why First — Why を先に問う

**条項**
すべての機能仕様・摩擦設計には、対応する **Why（哲学的・体験的根拠）** を明文化する。What と How を書く前に Why を固めること。

**根拠**
[research/02-philosophy.md §2.2](../research/02-philosophy.md) — 「True productivity starts by becoming aware of Why we do what we do」。Why が空の機能は、実装してもプロダクトの一貫性を破壊する。

**どこで効くか**
- `spec.md` の各機能・各摩擦設計に「狙い」「再考を促す対象」を併記
- レビュー時、Why が書かれていない項目はそのフェーズで差し戻し

---

### 3. Grounded in BuJo Research — BuJo 研究に根拠を置く

**条項**
摩擦設計・体験設計は、`docs/research/` の該当節を **根拠として明示リンク** する。研究で裏付けのない設計判断は、推測ではなく仮説として扱い、`[NEEDS CLARIFICATION]` か、`spec.md` 内に「仮説」として明示する。

**根拠**
`docs/research/` は BuJo メソッドを実装判断に耐える深さで体系化した一次資料である。これを参照しない設計は、Mabiki ではなく一般的な Todo アプリへの退行を招く。

**どこで効くか**
- `spec.md` の摩擦設計セクションに `→ research/02-philosophy.md §2.5` のような参照を必須化
- レビュー時、参照のない摩擦設計は「なぜそれが必要か」を再質問

---

### 4. Decision-Point Friction, Frictionless Flow — 摩擦は意思決定点に集約する

**条項**
摩擦は **「やるか／やらないかを問い直すべき意思決定点」**（移行・削除・振り返り）に集約する。完了操作・参照・日中のタスク消化など、思考の流れを切るべきでない場面では軽快さを保つ。

**根拠**
[spec.md §5 デジタル摩擦の設計原則](../specs/001-mabiki-mvp/spec.md) / [research/02-philosophy.md §2.11](../research/02-philosophy.md) — 摩擦の無差別配置は単なるストレスになる。哲学的に意味のある場面でのみ摩擦を機能させる。

**どこで効くか**
- UI/UX 設計判断時の「ここで摩擦を入れるべきか」の判定
- `plan.md` のインタラクション設計レビュー

---

### 5. Mobile-First — スマホファースト

**条項**
スマートフォン縦持ち・片手操作・タップしやすさを最優先する。PC/タブレット体験は二次的であり、PC 向けに最適化する都合でスマホ体験を劣化させない。

**根拠**
[spec.md §6 非機能要件](../specs/001-mabiki-mvp/spec.md) — 「手書きノートを開く」の代替体験を目指す以上、常時携行可能なスマホが一次デバイス。

**どこで効くか**
- レイアウト・コンポーネント設計の判断軸
- 技術選定（フレームワーク・UI ライブラリ）の評価基準

---

### 6. Explicit Non-Goals — 自動化禁止リスト

**条項**
以下は **意図的に実装しない**。これらの追加要求は、Constitution 改訂を伴う議論なしには受け付けない。

- 未完了タスクの自動繰り越し
- 一括移動・一括操作
- ドラッグ&ドロップでの並べ替え
- コピペでの移行（手動再入力を強制）
- AI による自動要約・自動分類・自動タスク生成（MVP 範囲外）
- 通知・リマインダーによる行動催促（MVP 範囲外）

**根拠**
[spec.md §3 Out of Scope](../specs/001-mabiki-mvp/spec.md) / [research/02-philosophy.md §2.5](../research/02-philosophy.md) — これらは BuJo の「signal を noise から分離する装置」を破壊する。便利機能としての一般的価値は認めるが、Mabiki のコア価値とは両立しない。

**どこで効くか**
- 機能追加要求の最初のフィルタ
- `spec.md` の Out of Scope セクションは本リストを継承する

---

### 7. No Speculative Friction — 推測の摩擦を足さない

**条項**
「なんとなく不便にする」「面白そうだから不便にする」は禁止。すべての摩擦は **(a) 哲学的根拠（原則3）** か **(b) 検証された仮説** のいずれかに紐づくこと。

**根拠**
[research/02-philosophy.md §2.5](../research/02-philosophy.md) — 摩擦は「signal を noise から分離する」目的を持つ。目的のない摩擦は、ユーザーにとってただのストレスであり、プロダクトを壊す。

**どこで効くか**
- 新規の摩擦設計を `spec.md` に追加するときの正当化要求
- レビュー時、「この摩擦は何のため？」に即答できない設計は差し戻し

---

### 8. Don't Guess, Ask or Clarify — 推測で進めない

**条項**
仕様・設計・実装の各フェーズで、不明点は **推測で埋めず** `[NEEDS CLARIFICATION: ...]` を残す。`[NEEDS CLARIFICATION]` が残ったまま下流フェーズに進んではならない。哲学的判断が必要な場合は `docs/research/` を参照し、それでも不明なら開発者本人に確認する。

**根拠**
[.claude/CLAUDE.md](../../.claude/CLAUDE.md) の「推測で進めない。確認してから進める」と整合。SDD において、推測で埋めた仕様は下流のすべてを汚染する。

**どこで効くか**
- すべてのフェーズゲート（spec → plan → tasks → implement）
- Claude Code を実装エージェントとして使う際の前提条件

---

## Constitution Check の運用

各フェーズの完了時、`plan.md` の冒頭近くに以下のチェックを記録する。

```markdown
## Constitution Check

- [ ] 原則1 (Friction is a Feature): 実装の都合で摩擦を緩めていないか
- [ ] 原則2 (The Why First): すべての機能・摩擦設計に Why が明記されているか
- [ ] 原則3 (Grounded in BuJo Research): research/ への根拠リンクが揃っているか
- [ ] 原則4 (Decision-Point Friction): 摩擦が意思決定点に集約されているか
- [ ] 原則5 (Mobile-First): スマホ縦持ち優先の設計か
- [ ] 原則6 (Explicit Non-Goals): 禁止リストの機能を含んでいないか
- [ ] 原則7 (No Speculative Friction): すべての摩擦に根拠・仮説が紐づくか
- [ ] 原則8 (Don't Guess): [NEEDS CLARIFICATION] が残っていないか

### 逸脱の正当化（該当する場合のみ）
- 原則X を破る理由:
- 代替案を検討した結果:
- 受容するトレードオフ:
```

逸脱を正当化できない場合は、フェーズを進める前に設計を見直すこと。
