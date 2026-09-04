# 初期技術スタック選定 ADR テンプレート

既存プロジェクトからSDD（スキル駆動開発）を導入する際、最初にリバースエンジニアリングして記録する初期ADR（`0001-technology-stack.md`）の標準テンプレート。

---

## 採番 & ファイル名
- **ファイル名**: `docs/adr/0001-technology-stack.md`
- **ステータス**: `Accepted`

---

## テンプレート

```markdown
# 0001. 初期技術スタックおよびアーキテクチャベースラインの策定

## Context

本プロジェクトは既存コードベースとして開発が進められており、スキル駆動開発（SDD）の導入に伴い、現状採用されている主要な技術スタックおよびアーキテクチャ構成をベースラインとして明文化・記録する。

## Decision

既存コードベースの解析結果に基づき、以下の技術スタックおよびアーキテクチャ構成を現状の標準として承認・記録する。

### 1. フロントエンド / クライアント
- **フレームワーク / ライブラリ**: <例: Next.js (App Router), React 18, Angular, Vue 3 など>
- **言語**: <例: TypeScript (Strict モード)>
- **スタイリング / UI**: <例: Tailwind CSS, CSS Modules, MUI など>
- **状態管理**: <例: React Context, Zustand, Redux Toolkit, TanStack Query など>

### 2. バックエンド / API
- **ランタイム / フレームワーク**: <例: Node.js (Hono / Express / NestJS), Python (FastAPI), Go など>
- **API形式**: <例: REST, GraphQL, gRPC, tRPC>
- **認証 / 認可**: <例: NextAuth, Azure AD / Entra ID, Supabase Auth, JWT など>

### 3. データ永続化 / ストレージ
- **データベース**: <例: Azure Cosmos DB, PostgreSQL, MySQL, MongoDB など>
- **ORM / クエリビルダ**: <例: Prisma, TypeORM, Drizzle, 自作データアクセス層 など>
- **ストレージ / キャッシュ**: <例: Azure Blob Storage, Redis など>

### 4. インフラ / デプロイ / CI/CD
- **ホスティング環境**: <例: Azure Static Web Apps, Azure Container Apps, Vercel, AWS ECS など>
- **IaC (Infrastructure as Code)**: <例: Bicep, Terraform, なし>
- **CI/CD**: <例: GitHub Actions, Azure Pipelines>

## Consequences

- **メリット**:
  - 今後の機能拡張（`sdd-spec-ado`, `sdd-implement-ado` 等）において、既存の技術選定との整合性を維持できる。
  - 新規メンバーやAIエージェントがプロジェクトの全体像を即座に把握できる。
- **今後の変更方針**:
  - 新たなライブラリ導入や永続化層の変更など、重大なアーキテクチャ決定を行う場合は、次番のADR（`0002-*.md` 以降）を起票して合意形成を行う。

## Status

Accepted
```
