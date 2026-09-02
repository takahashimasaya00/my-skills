# ワークアイテム開発ロードマップ テンプレート

Azure DevOps ワークアイテム（PBI, Bug, Task）ごとの実装ロードマップの標準フォーマットである。
本ファイルを `docs/roadmap/#{WorkItemId}.roadmap.md` として出力する。

---

## テンプレート

```markdown
# #{WorkItemId}: {WorkItemTitle} - 実装ロードマップ & プロンプト進行ガイド

このドキュメントは、Azure DevOps ワークアイテム **#{WorkItemId} ({WorkItemTitle})** の実装を段階的に進めるための開発ロードマップです。
各ステップ完了時にチェックボックス `[x]` を更新して進捗を管理します。

---

## 📋 ワークアイテム概要

- **アイテムID**: #{WorkItemId}
- **種別**: {WorkItemType} (Product Backlog Item / Bug / Task)
- **タイトル**: {WorkItemTitle}
- **関連ドキュメント**:
  - `docs/spec/system-architecture.md`
  - `docs/spec/data-models.md`
  - `docs/spec/features.md`
  - {docs/design/ がある場合: `docs/design/concept.md`, `docs/design/tokens.md`}
  - {該当するADRがあれば link: `docs/adr/00XX-*.md`}

---

## 🗺️ 実装ステップ一覧

### [ ] Step 1: {ステップ1 タイトル (例: データモデル & 型定義 / APIエンドポイント実装)}

- **目的**: {このステップで達成する目的}
- **成果物**:
  - `{変更または新規作成するファイルパス1}`
  - `{変更または新規作成するファイルパス2}`
- **参照コンテキスト**:
  - `docs/spec/data-models.md`
  - `docs/spec/system-architecture.md`
- **検証コマンド**:
  - `npm run build`
  - `npm run test`
- 💬 **プロンプト指示例**:
  > docs/spec/data-models.md および docs/roadmap/#{WorkItemId}.roadmap.md に基づいて、「Step 1: {ステップ1 タイトル}」を実装してください。{具体的な指示内容}

---

### [ ] Step 2: {ステップ2 タイトル (例: サービスクラス・UIコンポーネント実装)}

- **目的**: {このステップで達成する目的}
- **成果物**:
  - `{変更または新規作成するファイルパス1}`
  - `{変更または新規作成するファイルパス2}`
- **参照コンテキスト**:
  - `docs/spec/features.md`
  - {UI実装の場合: `docs/design/concept.md`, `docs/design/tokens.md`}
- **検証コマンド**:
  - `npm run test`
  - `npm run lint`
- 💬 **プロンプト指示例**:
  > docs/spec/features.md{docs/designがある場合: 、docs/design/concept.md および docs/design/tokens.md}、docs/roadmap/#{WorkItemId}.roadmap.md に基づいて、「Step 2: {ステップ2 タイトル}」を実装してください。デザインコンセプトおよびデザイントークンに準拠してUIを構築してください。

---

### [ ] Step 3: {ステップ3 タイトル (例: 統合・E2Eテスト & 動作検証)}

- **目的**: {このステップで達成する目的}
- **成果物**:
  - `{テストファイルパス}`
- **参照コンテキスト**:
  - `docs/spec/features.md`
- **検証コマンド**:
  - `npm run test`
  - `npm run test:e2e`
- 💬 **プロンプト指示例**:
  > docs/roadmap/#{WorkItemId}.roadmap.md の「Step 3: {ステップ3 タイトル}」に基づき、テストを作成して全検証コマンドが成功することを確認してください。

---

## ✅ 完了の定義 (Definition of Done)

- [ ] すべての受入基準を満たしていること
- [ ] 全てのステップのテスト・検証コマンドがパスしていること (`npm run test`, `npm run lint`, `npm run build`)
- [ ] ドキュメント（`docs/spec/`, `docs/adr/`, `docs/design/`）との整合性が保たれていること
```
