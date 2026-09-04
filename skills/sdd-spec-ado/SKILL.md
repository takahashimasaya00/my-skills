---
name: sdd-spec-ado
description: >-
  Design and flesh out Azure DevOps work items (PBI, Bug, Task) at an architectural and technical specification level using grill-with-docs.
  Takes into account docs/design/ (design concept, design tokens) if present, and generates or updates docs/spec (data-models, features, system-architecture), docs/adr, and outputs implementation steps as child Task work items in Azure DevOps.
  Always trigger this skill whenever the user mentions designing, architectural refinement, technical specification,
  or creating development tasks/roadmaps for an Azure DevOps work item, PBI, Bug, or Task.
---

# Azure DevOps Work Item Design (`sdd-spec-ado`)

Azure DevOpsで管理されているワークアイテム（Product Backlog Item, Bug, Task）を取得し、`grill-with-docs` のアプローチで**実装を考慮した設計レベル（アーキテクチャ・データモデル・機能仕様）**を徹底的につき詰め（Grilling）、合意された内容をリポジトリ内の `docs/`（spec, adr、必要に応じてdesign）へ体系的に反映し、具体的な実装ステップをADOの子Taskとして起票するスキル。
リポジトリ内に `docs/design/` フォルダが存在する場合は、記載されているデザインコンセプトやデザイントークンも前提として考慮し、UI仕様やステート設計をつき詰める。

---

## 核心原則: クラス単位ではなく「設計レベル」に集中

- **対象（設計レベル - Architecture / Model / Interface）**:
  - システム構成・コンポーネント間の責務境界（Angular, NestJS, Cosmos DB, Azure Blob Storage 等）
  - データモデル定義（Cosmos DB ドキュメント構造、型定義、パーティションキー、整合性制約）
  - API / 通信インターフェース（エンドポイントパス、HTTPメソッド、DTO、ステータスコード）
  - 全体機能マップの更新、UIステート（Loading / Success / Empty / Error）と画面フロー（`docs/design/` がある場合はそのデザインコンセプト・トークンを前提とする）
  - トレードオフを伴う技術決定（ADR起票の3条件に合致するもの）
  - 異常系、セキュリティ、エラーハンドリング、非機能要件
  - 段階的な実装ステップ（ADO Taskの起票）
- **対象外（実装詳細 - Micro Coding Details）**:
  - クラス内部の個別メソッド実装コード、ローカル変数名、詳細なCSSプロパティ値など。これらはADO Taskに基づく実装フェーズで決定する。
- 詳細は [design-guidelines.md](./references/design-guidelines.md) を参照。

---

## ワークフロー手順

### Step 1: Azure DevOps ワークアイテムの取得 & 前提調査

1. ユーザーから Work Item ID（例: `#123` または `123`）を受け取る。
2. Azure DevOps MCP の `wit_work_item` を実行してアイテム詳細を取得する。
   ```json
   {
     "action": "get",
     "id": <WorkItemId>,
     "expand": "All"
   }
   ```
3. 取得した情報から以下を把握する：
   - `System.WorkItemType`: 種別 (`Product Backlog Item`, `Bug`, `Task` 等)
   - `System.Title`: タイトル
   - `System.Description`: 要件・概要
   - `Microsoft.VSTS.Common.AcceptanceCriteria`: 受入基準 (PBIの場合)
   - `Microsoft.VSTS.TCM.ReproSteps`: 再現手順・不具合内容 (Bugの場合)
   - 親Feature / 子Task などのリンク関係
4. **前提ドキュメントの事前調査**:
   - 既存の `docs/spec/system-architecture.md`, `docs/spec/data-models.md`, `docs/spec/features.md`, `docs/adr/`, `CONTEXT.md` を読み込み、現在のシステム状態と用語を把握する。
   - **デザインコンセプトの確認 (`docs/design/`)**:
     - `docs/design/` ディレクトリが存在するか確認する。
     - 存在する場合、`docs/design/concept.md`（デザイン哲学・ペルソナ・トーン＆マナー・レイアウト/インタラクション原則）および `docs/design/tokens.md`（カラー・タイポグラフィ・スペーシング等のトークン定義）を読み込む。
     - UI/UXや画面仕様の検討において、これらデザインコンセプト・トークンと矛盾しないよう前提知識としてインプットする。

---

### Step 2: 設計レベルの Grilling (`grill-with-docs` 統合)

`grill-with-docs`（`grilling` + `domain-modeling`）を適用し、設計ツリー（Design Tree）を展開してフロンティア（未決の設計判断事項）をラウンド制でインタビューする。

#### インタビューの基本ルール
- **推論ツリー展開**: 前提条件が整っているフロンティアの質問をラウンドごとにまとめて提示する。
- **推奨解（💡 Recommended Answer）の提示**: 単に質問を投げるのではなく、既存アーキテクチャ・ベストプラクティスを踏まえた具体的な推奨解とその理由を必ず併記する。
- **デザインコンセプトに根ざした提案**: `docs/design/` が存在する場合、UIステートや画面フローの設計判断において、定義されているトンマナ（世界観、質感）やカラールールに基づいた推奨解を提示する（例: 「`docs/design/concept.md` で定義された穏やかで信頼感あるトンマナに合わせ、エラー時はモーダルではなくインラインアラート表示＋再試行ボタンを推奨」など）。
- **事実調査はエージェントの責務**: リポジトリの既存コードや設定ファイル、Bicep、既存ドキュメントから確認できる事実（Fact）はエージェント自身で調べ、ユーザーには「決定（Decision）」だけを問う。

#### ラウンド質問のフォーマット例
```markdown
🔹 **Q1** - **<設計判断タイトル>**: <設計上の背景・選択肢の比較>
💡 **推奨**: <推奨する設計方針とその理由>

---

🔹 **Q2** - **<設計判断タイトル>**: <設計上の背景・選択肢の比較>
💡 **推奨**: <推奨する設計方針とその理由>
```

#### 主な深掘り観点
1. **コンポーネント構成 & API**: 新規エンドポイントのパス・メソッド・DTOはどうするか？どのサービスに配置するか？
2. **データモデル**: 新規エンティティ/フィールドは必要か？Cosmos DBのパーティションキー・型定義はどうするか？
3. **UI / 機能仕様**: 画面遷移や状態管理（ローディング、空表示、エラー表示、再試行）の振る舞いはどうするか？`docs/design/` のデザイン原則やトークンと調和しているか？
4. **アーキテクチャトレードオフ**: 代替案（Approach A vs Approach B）はあるか？ADRを起票すべき不可逆な決定か？
5. **ドメイン用語**: 新たに導入された概念や境界は明確か？

全フロンティアが解消し、ユーザーから設計内容の合意（LGTM）を得たら Step 3 へ進む。

---

### Step 3: `docs` フォルダへの反映

合意された設計内容を、リポジトリの `docs` ディレクトリへ反映する。

#### 1. 仕様書の更新・作成 (`docs/spec/`)
少なくとも以下の3ファイルを確認し、該当箇所を整合性を保ちながら更新する：
- **`docs/spec/data-models.md`**:
  - 新規モデル、変更されたフィールド、型定義、パーティションキー等を追記・更新。
- **`docs/spec/features.md`**:
  - 全体機能マップ（Mermaid図）への追加、および機能詳細（画面遷移、状態、エラーハンドリング）を追記・更新。
  - `docs/design/` が存在する場合、デザインコンセプト・トークンに基づいたUI状態（スタイル・配置方針）の整合性を保って記述する。
- **`docs/spec/system-architecture.md`**:
  - システム構成、レイヤー連携、APIエンドポイント一覧、セキュリティ・非機能要件の変更を反映。

#### 2. デザイン仕様の更新 (`docs/design/`) ※該当する場合
- ワークアイテムの要件によって、新たなデザイントークン（新色、新たなタイポグラフィ種別、コンポーネント造形など）の追加や更新が必要と合意された場合、`docs/design/concept.md` や `docs/design/tokens.md` に追記・更新する。

#### 3. ADRの起票 (`docs/adr/`)
- 設計Grillingの中で「不可逆性」「意外性」「明確なトレードオフ」を満たす重要な意思決定があった場合のみ新規起票する。
- 既存の最大番号を確認し、連番（例: `0015-*.md`）で作成する。
- 詳細は [adr-template.md](./references/adr-template.md) を参照。

#### 4. ドメインモデルの更新 (`CONTEXT.md`)
- 新しい用語や概念の境界が定義された場合、`CONTEXT.md` に用語と定義を追記する。

---

### Step 4: 実装ステップの ADO Task 起票

設計合意内容に基づき、対象のワークアイテム（PBIなど）の子アイテムとして、具体的な実装ステップを `Task` ワークアイテムとして ADO 上に作成する。

1. **Taskの切り出し**:
   - フロントエンド、バックエンド、データベース設定など、論理的でアサイン可能な粒度でTaskを定義する。
2. **`wit_work_item_write` を用いた起票**:
   - 各Taskに対して、`wit_work_item_write` ツールを使用してアイテムを作成する。
   - `action` を `create` とし、`type` を `Task` と設定する。
   - `parentId` に Step 1 で取得した親ワークアイテムの ID を指定する。
3. **Task Description（詳細）の記述ルール**:
   - `System.Description` フィールドには HTML (または ADO が解釈可能な形式) を用いて、以下の情報を必ず含めること。
     - **目的**: Taskで達成すべきゴール
     - **成果物**: 作成・変更対象の想定ファイル
     - **参照コンテキスト**: 関連する仕様書（`docs/spec/system-architecture.md` 等）、該当ADR、デザイン仕様へのリンク
     - **検証コマンド**: ビルド、テスト、Lint コマンド（`npm run test` 等）
     - 💬 **プロンプト指示例**: 後続の `sdd-implement-ado` が参照するための実装指示（例：「`docs/design/` のデザインコンセプトやトークンに準拠して実装してください」）

4. 作成後、起票したすべての Task の ID とタイトル、ならびに更新・作成した `docs/` 配下のファイル一覧をユーザーに報告する。
