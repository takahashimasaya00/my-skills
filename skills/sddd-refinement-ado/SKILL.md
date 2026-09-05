---
name: sddd-refinement-ado
description: >-
  Refine and clarify Azure DevOps work items (Feature, Product Backlog Item, User Story, Bug) using a Scrum-focused grilling interview.
  Always trigger this skill whenever the user mentions refining, fleshing out, clarifying, grilling, or detailing an Azure DevOps item,
  PBI, Feature, or Bug, or asks to prepare requirements and acceptance criteria before sprint planning.
---

# Azure DevOps Item Refinement (`sddd-refinement-ado`)

Azure DevOpsで管理されているアイテム（Feature, PBI / User Story, Bug）を取得し、スクラム開発における **Definition of Ready (DoR)** を満たすレベルまで要件と受入基準を徹底的につき詰め（Grilling）、合意形成後にAzure DevOpsへ自動反映するスキル。

---

## 核心原則: 実装詳細への踏み込み禁止

- **フォーカス（Why & What）**:
  - ビジネス価値、ユーザー課題、ペルソナ、利用シナリオ
  - 受入基準（Given-When-Then、検証チェックリスト）
  - スコープ境界（In Scope / Out of Scope）、エラー時の振る舞い
  - Featureの場合はPBIへの分割案
- **対象外（How - 実装詳細）**:
  - クラス設計、関数・変数名、物理DBスキーマ、特定APIの内部ロジックなど。これらはスプリント中に開発者がタスク化する領域のため、本スキルでは扱わない。
- **ユーザー承認駆動のADO反映 (Human-in-the-Loop)**:
  - 要件や受入基準を精緻化した後、**Azure DevOps（アイテム更新や子PBI起票）へ反映する前に、必ず更新内容のプレビューを提示してユーザーから明示的な承諾（LGTM）を得る**。
  - ユーザーからの明示的な承諾を得るまで、勝手にADOの更新・作成ツール（`wit_work_item_write`）を実行してはならない。
- 詳細は [scrum-guidelines.md](./references/scrum-guidelines.md) を参照。

---

## ワークフロー手順

### Step 1: Azure DevOpsアイテムの取得

1. ユーザーから Work Item ID（またはURL / 検索キーワード）を受け取る。
2. Azure DevOps MCP の `wit_work_item` を実行してアイテム詳細を取得する。
   ```json
   {
     "action": "get",
     "id": <WorkItemId>,
     "expand": "All"
   }
   ```
3. 取得した情報から以下を把握する：
   - `System.WorkItemType`: 種別 (`Feature`, `Product Backlog Item`, `User Story`, `Bug`)
   - `System.Title`: タイトル
   - `System.Description`: 既存の説明
   - `Microsoft.VSTS.Common.AcceptanceCriteria`: 既存の受入基準
   - `Microsoft.VSTS.TCM.ReproSteps`: 再現手順（Bugの場合）
   - 親子関係や関連リンク

---

### Step 2: スクラム特化のGrilling（要件深掘りインタビュー）

`grilling` 手法に基づき、推論ツリー（Design Tree）を展開してフロンティアの質問をラウンド制で投げかける。

#### 質問のフォーマット
各ラウンドでは、未解決の前提条件のない質問（フロンティア）をまとめて提示し、**必ず推奨解（Recommended Answer）を併記**する。

```markdown
🔹 **Q1** - **<質問タイトル>**: <背景および選択肢の説明>
💡 **推奨**: <推奨する方針とその理由>

---

🔹 **Q2** - **<質問タイトル>**: <背景および選択肢の説明>
💡 **推奨**: <推奨する方針とその理由>
```

#### アイテム種別ごとの深掘りフロンティア
- **Feature**:
  - ビジネス価値 & KPI（何を達成すればこのFeatureは成功か？）
  - ターゲットペルソナ & 主要利用フロー
  - MVP（フェーズ1）と将来スコープ（フェーズ2以降）の切り分け
  - 1スプリントで完結可能なPBI群への分割案（タイトルと概要）
- **PBI / User Story**:
  - ユーザーストーリーの明確化（Who / What / Why）
  - 受入基準（Given-When-Then 形式、またはチェックリスト形式）
  - エッジケース・異常系（データ未登録、入力エラー、タイムアウト時などの画面挙動）
  - 今回のスコープ外（Out of Scope）
- **Bug**:
  - 発生環境、アカウント条件、トリガー操作
  - 期待される振る舞い vs 実際の振る舞い
  - 影響度（全ユーザーか特定データ依存か、業務影響の大きさ）
  - 修正検証の受入基準（何をどう確認すればクローズできるか）

ユーザーの回答を受け取り、新たなフロンティアを計算して次のラウンドへ進む。全ての決定事項が合意され、DoRが満たされたらStep 3へ進む。

---

### Step 3: 精緻化仕様の提示 & ユーザー確認・承認 (LGTM)

Grillingで合意した内容を整理し、**Azure DevOpsへ反映する前に必ず更新内容のプレビューを提示してユーザーに承認（LGTM）を求める**。

#### 提示フォーマット例 (PBIの場合)
```markdown
# 📋 [Refined PBI プレビュー] #<ID> <タイトル>

### ユーザーストーリー
- **As a**: <ユーザー種別>
- **I want**: <機能・やりたいこと>
- **So that**: <得られる価値・目的>

### 受入基準 (Acceptance Criteria)
- [ ] **シナリオ1 (正常系)**: Given ... When ... Then ...
- [ ] **シナリオ2 (異常系)**: Given ... When ... Then ...
- [ ] **非機能基準**: レスポンスが3秒以内であること

### スコープ外 (Out of Scope)
- 〇〇の機能（次スプリントの別PBIとして対応）
```

※ Featureの場合は、上記に加えて **「分割PBI一覧（タイトル・概要・受入基準ドラフト）」** を提示する。

#### ユーザー確認の必須ルール
1. プレビューを提示し、ユーザーに確認とフィードバックを求める。
2. **重要制約**: ユーザーから明示的な承諾（「OK」「反映して」「LGTM」など）を得るまで、**絶対にStep 4（ADOへの反映）には進まないこと**。
3. ユーザーから修正要望や追加指示があった場合は、Grillingに戻るか仕様を調整して再度確認を得る。

---

### Step 4: ユーザー承認後の Azure DevOps への反映

**Step 3でユーザーから明示的な承認（LGTM）を得た後のみ**、Azure DevOps MCP を呼び出して反映する。
詳細なパラメータ仕様は [ado-field-mappings.md](./references/ado-field-mappings.md) を参照。

#### 1. 対象アイテムの更新
`wit_work_item_write` を呼び出し、最新の要件・受入基準で上書き更新し、ディスカッション（`System.History`）にAI Refinementの実施記録を残す。

```json
{
  "action": "update",
  "id": <WorkItemId>,
  "updates": [
    {
      "op": "replace",
      "path": "/fields/System.Description",
      "value": "<精緻化されたMarkdown説明>"
    },
    {
      "op": "replace",
      "path": "/fields/Microsoft.VSTS.Common.AcceptanceCriteria",
      "value": "<精緻化された受入基準>"
    },
    {
      "op": "add",
      "path": "/fields/System.History",
      "value": "🤖 **AI Refinement Complete**<br/>スクラム要件定義（User Story / 受入基準）の合意内容を反映しました。"
    }
  ]
}
```

#### 2. 子PBIの一括作成 (Feature分割時)
Featureで子PBIの分割案が合意された場合、`add_child` アクションで一括登録する。

```json
{
  "action": "add_child",
  "parentId": <FeatureId>,
  "workItemType": "Product Backlog Item",
  "items": [
    {
      "title": "<PBI 1 タイトル>",
      "description": "<PBI 1 ユーザーストーリー & 受入基準>",
      "format": "Markdown"
    },
    {
      "title": "<PBI 2 タイトル>",
      "description": "<PBI 2 ユーザーストーリー & 受入基準>",
      "format": "Markdown"
    }
  ]
}
```

#### 3. 完了報告
反映したアイテムのURLや、新設された子PBIの一覧をユーザーに分かりやすく報告する。