# Azure DevOps Work Item フィールド仕様 & MCP 呼び出しリファレンス

Azure DevOps MCP (`azure-devops`) を利用して Work Item の取得・更新・子アイテム作成を行う際のリファレンスです。

---

## 1. アイテム情報の取得

`wit_work_item` ツールを使用してアイテムの詳細を取得します。

```json
{
  "action": "get",
  "id": <WorkItemId>,
  "expand": "All"
}
```

### 取得データの主要フィールド
- `fields["System.WorkItemType"]`: アイテム種別 (`Feature`, `Product Backlog Item`, `User Story`, `Bug` など)
- `fields["System.Title"]`: タイトル
- `fields["System.Description"]`: 説明（HTML または Markdown）
- `fields["Microsoft.VSTS.Common.AcceptanceCriteria"]`: 受入基準（Feature, PBI）
- `fields["Microsoft.VSTS.TCM.ReproSteps"]`: 再現手順（Bug）
- `fields["System.AreaPath"]`: エリアパス
- `fields["System.IterationPath"]`: イテレーションパス
- `fields["System.State"]`: ステータス (`New`, `Approved`, `Committed`, etc.)
- `relations`: 関連リンク（親アイテム、子アイテム、関連PRなど）

---

## 2. アイテム情報の更新

`wit_work_item_write` ツールを使用して精緻化した内容を反映します。

### 基本パラメータ
```json
{
  "action": "update",
  "id": <WorkItemId>,
  "updates": [
    {
      "op": "replace",
      "path": "/fields/System.Description",
      "value": "<精緻化したDescription (Markdown/HTML)>"
    },
    {
      "op": "replace",
      "path": "/fields/Microsoft.VSTS.Common.AcceptanceCriteria",
      "value": "<精緻化した受入基準 (Markdown/HTML)>"
    },
    {
      "op": "add",
      "path": "/fields/System.History",
      "value": "🤖 **AI Refinement Complete**<br/>要件および受入基準を精緻化・合意内容を反映しました。"
    }
  ]
}
```

> [!NOTE]
> - 該当フィールドが元々存在しない（空の）場合は `op: "add"` または `op: "replace"` を使用します。
> - `System.History` は常に `op: "add"` でディスカッションにコメントを追記できます。

### アイテム種別ごとの反映対象フィールド

| 種別 | 主な反映フィールド | パス |
| :--- | :--- | :--- |
| **Feature** | 概要・価値・スコープ<br/>受入基準・KPI<br/>履歴コメント | `/fields/System.Description`<br/>`/fields/Microsoft.VSTS.Common.AcceptanceCriteria`<br/>`/fields/System.History` |
| **PBI / Story** | ユーザーストーリー・背景<br/>受入基準 (Given-When-Then等)<br/>履歴コメント | `/fields/System.Description`<br/>`/fields/Microsoft.VSTS.Common.AcceptanceCriteria`<br/>`/fields/System.History` |
| **Bug** | 再現手順・期待される振る舞い<br/>修正検証の受入基準<br/>履歴コメント | `/fields/Microsoft.VSTS.TCM.ReproSteps` (または `System.Description`)<br/>`/fields/Microsoft.VSTS.Common.AcceptanceCriteria`<br/>`/fields/System.History` |

---

## 3. 子PBIの一括作成 (Feature分割時)

FeatureのGrillingによって複数のPBIに分解された場合、`action: "add_child"` でFeature配下に子PBIを一括作成します。

```json
{
  "action": "add_child",
  "parentId": <FeatureId>,
  "workItemType": "Product Backlog Item",
  "items": [
    {
      "title": "PBI 1 タイトル",
      "description": "### ユーザーストーリー\n...\n### 受入基準\n...",
      "format": "Markdown"
    },
    {
      "title": "PBI 2 タイトル",
      "description": "### ユーザーストーリー\n...\n### 受入基準\n...",
      "format": "Markdown"
    }
  ]
}
```

> [!TIP]
> プロジェクトのプロセスが `Agile` の場合は `workItemType: "User Story"`、`Scrum` の場合は `workItemType: "Product Backlog Item"` を指定します（事前にFeatureのコンテキストや既存アイテムの種別から判定）。