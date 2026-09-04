---
name: sdd-implement-ado
description: >-
  Implement features step-by-step based on Azure DevOps Task items created by sdd-spec-ado.
  Takes into account docs/design/ (design concept, design tokens) if present to establish implementation plans.
  Follows a strict cycle: plan implementation (without modifying project files), obtain user approval, execute implementation,
  perform code review and fix findings, run tests/verification commands, and update the ADO Task status to Done.
  Always trigger this skill whenever the user asks to implement, build, or develop a specific ADO Task.
---

# Azure DevOps Task Implementation (`sdd-implement-ado`)

`sdd-spec-ado` 等によって起票された Azure DevOps の `Task` ワークアイテムの `Description` に記載された情報に基づき、計画・実装・レビュー・テスト・完了更新を段階的に進める実装実行スキル。
リポジトリ内に `docs/design/` フォルダが存在する場合は、記載されているデザインコンセプトやデザイントークンも前提として考慮し、世界観・トンマナ・UI原則に調和した実装計画の立案および実装・レビューを行う。

---

## 核心原則

1. **計画段階でのファイル変更の禁止 (Read-Only Planning)**:
   - 実装計画（Step 1）の立案中は、プロジェクト内のソースコードや設定ファイルに対して一切の作成・変更を行わない。ドキュメントの読み込みと調査のみを行う。
2. **ユーザー承認駆動 (Human-in-the-Loop)**:
   - 立案した実装計画をユーザーに提示し、明示的な承諾（LGTM）を得てから実装（Step 3）に着手する。
3. **デザインコンセプト・トークンの尊重 (Design Consistency)**:
   - UIやスタイリングの変更を伴うステップでは、`docs/design/`（`concept.md`, `tokens.md`）が存在する場合、そこに定義されたデザイン哲学・トーン＆マナー・デザイントークン（CSS変数等）を厳格に尊重する。アドホックなカラーやマージンの直書きを禁止し、トークン体系に沿った実装計画を立てる。
4. **レビューと指摘事項の自己修正 (Review & Fix)**:
   - 実装後はコードレビューを実施し、見つかった指摘事項や改善点を速やかに修正する。（※デザイン整合性や特定観点の縛りは設けず、必要に応じて専門のレビュースキルとも連携可能とする）
5. **テスト・検証コマンドの厳守 (Test & Verify)**:
   - ロードマップに定義された検証コマンド（`npm run test`, `npm run lint`, `npm run build` 等）を実行し、すべて成功（パス）させる。テストコードが存在する場合は必ずテストを実行する。
6. **ドキュメントのJIT同期とタスク状態の更新 (Sync & Done)**:
   - 実装と検証が完了した直後、Taskに紐づく設計草案（`docs/proposals/`）のパッチを `docs/spec/` 本体に適用（JIT同期）し、ADO Task の State を `Done`（完了）に更新する。

---

## ワークフロー手順

### Step 1: ADO Task の特定 & 実装計画の立案

1. **対象 Task の特定**:
   - プロンプトから対象の ADO Task ID（例: `#456` または `456`）を特定する。
   - **未指定時の確認**:
     - Task ID が明示されていない場合は、勝手に推測して進めず、ユーザーに「どの Task を実装しますか？ (Task ID を教えてください)」と確認する。

2. **Task 情報の取得**:
   - ADO MCP の `wit_work_item` ツールを使用して、指定された Task ID の詳細を取得する。
   - `System.Description` フィールドの内容を読み込み、以下の情報を確認する：
     - **目的**: Task で達成すべきゴール
     - **目的**: Task で達成すべきゴール
     - **詳細および仕様反映パッチ**: `docs/proposals/pbi-<ID>.md` への参照パスと対象セクション

3. **参照ドキュメント・コードの調査**:
   - Task Descriptionに指定された `docs/proposals/pbi-<ID>.md` の対象Taskセクションを読み込み、適用予定の仕様パッチ（Blueprint差分）と実装内容を正確に把握する。
   - パッチ適用対象の既存 `docs/spec/` を読み込み、他PBIの割り込み作業等で現状の `docs/spec/` とパッチにコンフリクト（乖離）がないか確認する。乖離がある場合は、パッチの「リベース（最新仕様への追従調整）」を行う前提で計画を立てる。
   - **デザインコンセプトの調査 (`docs/design/`)**:
     - `docs/design/` ディレクトリが存在するか確認する。
     - 存在する場合、`docs/design/concept.md`（デザイン哲学・ペルソナ・トーン＆マナー・レイアウト/インタラクション原則）および `docs/design/tokens.md`（カラー・タイポグラフィ・スペーシング・Elevation等のデザイントークン仕様およびCSS変数などの実装コード）を読み込む。
     - UIや画面・スタイルに関わるステップの場合、これらのデザイン方針やデザイントークンの活用方法（既存トークン定義、CSSカスタムプロパティなど）を事前に調査・整理する。
   - 既存のソースコードを読み込み、現在の実装状態を把握する。

4. **実装計画の策定**:
   - 以下の構成で実装計画をまとめる：
     - 対象ステップの概要とゴール
     - 変更・新規作成するファイル一覧とその役割
     - 具体的な実装ロジック・型・関数の変更点
     - **デザインコンセプト・トークン適用方針（UI・スタイリングを伴う場合）**:
       - 適用するデザイントークン（CSS変数・ユーティリティ・テーマ値）
       - トンマナやUIステート（Loading, Empty, Error, Success）の表現方針
       - アドホックなスタイルの排除とデザイン原則への準拠方法
     - テスト方針（追加・更新するテストコードの内容）
     - 実行する検証コマンド
   - **重要制約**: この時点では、ワークスペース内のコードや設定ファイルを一切変更・作成しないこと。

---

### Step 2: ユーザー確認 & 承諾 (LGTM)

1. 立案した実装計画をユーザーに提示し、確認とフィードバックを求める。
2. ユーザーから承諾（「OK」「進めて」「LGTM」など）を得るまで、コード実装には進まない。
3. ユーザーから修正要望や追加指示があった場合は、計画を修正して再度確認を得る。

---

### Step 3: 実装の実施

1. ユーザーから承諾を得たら、計画に基づき実装に着手する。
2. ロードマップの「成果物」に記載されたファイルを作成・変更する。
3. テストコード（ユニットテスト、統合テスト等）が含まれる、または必要な場合は、テストコードも合わせて実装する。
4. ドキュメント（`docs/spec/`, `docs/adr/`, `docs/design/`）で定義された命名規則・型定義・アーキテクチャ方針、およびデザインコンセプト・デザイントークンを遵守する。
   - UI実装時は、ハードコードされたカラー値やマージン値を避け、`docs/design/tokens.md` に定義されたトークンやCSS変数を活用する。

---

### Step 4: コードレビュー & 指摘事項の修正

1. 実装・変更したすべてのファイルに対してコードレビューを実施する。
   - *(必要に応じて、別途用意されたレビュー用スキルや手順を呼び出して適用することも可能)*
2. レビュー観点：
   - **ロジック・型整合性**: 仕様書・ADRに合致しているか、潜在的なバグやエッジケース漏れがないか。
   - **デザイン整合性**: UIやスタイリングにおいて、`docs/design/concept.md` や `docs/design/tokens.md` の原則・トークンに準拠しているか、スタイル直書きがないか。
3. レビューにより発見された不整合や指摘事項を整理し、即座にコード修正を行って品質を高める。

---

### Step 5: 仕様ドキュメントの JIT 同期 (Sync)

1. **仕様パッチの適用**:
   - `docs/proposals/pbi-<ID>.md` で定義された、今回の Task 用の仕様パッチ（Blueprintのスライス）を、リポジトリの `docs/spec/` 本体に適用（マージ）する。
   - 他の PBI 作業によって `docs/spec/` が変更されており、単純なパッチ適用ができない場合は、最新の `docs/spec/` に合わせてパッチの内容を調整（リベース）してから反映する。
2. **草案のクリーンアップ（全Task完了時）**:
   - もし今回の Task がその PBI における最後の Task であった場合、用済みとなった `docs/proposals/pbi-<ID>.md` ファイルを削除（または `docs/proposals/archive/` フォルダへ移動）し、コンテキストの肥大化を防ぐ。

---

### Step 6: テスト・検証 & ADO Task の状態更新

1. **テスト・検証コマンドの実行**:
   - Task の Description に指定されている「検証コマンド」を実行する。
   - テストコードが実装されている場合は、必ずテストを実行してグリーン（成功）になることを確認する。
   - テストやビルド・Lintでエラーが出た場合は、原因を特定して修正し、すべての検証が成功するまで繰り返す。

2. **ADO Task の State 更新**:
   - すべての検証が成功したら、ADO MCP の `wit_work_item_write` ツールを使用して、対象 Task の `System.State` フィールドを `Done` (または完了状態) に更新する。

3. **完了報告**:
   - 以下の内容をユーザーに報告する：
     - 実装・変更したファイル一覧
     - デザインコンセプト・トークンの適用内容（UIステップの場合）
     - コードレビューで発見・修正した内容
     - 実行したテスト・検証コマンドとその結果
     - ADO Task の `Done` への更新完了通知
     - （次ステップが存在する場合）次のステップの概要案内
