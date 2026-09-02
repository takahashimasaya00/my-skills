# sdd-skills

スキル駆動開発のAgent Skills

## 概要

### sdd-init-docs

既存プロジェクトからSDD用のドキュメント（`docs/spec/`、`docs/adr/`）を一括初期生成するSkills。

### sdd-design-concept

デザインコンセプトを明確にするためのSkills。

### sdd-refinement-ado

Azure DevOpsのワークアイテムを明確にするためのSkills。

### sdd-spec-ado

Azure DevOpsのワークアイテムを基に設計を行うSkills。

### sdd-implement

生成した設計を基に実装を行うSkills。

### sdd-sync

ワンショットで実装したコードを設計に反映するSkills。

## フォルダ構成

Skillsによって生成されるフォルダ構成例。

```
docs/
  spec/
    features.md
    data-models.md
    system-architecture.md
  adr/
    001-ui-ux-原則.md
    002-state-management-原則.md
    ...
  design/
    concept.md
    tokens.md
  roadmap/
    #1.roadmap.md
    #2.roadmap.md
    ...
```

## 依存Skills・MCP
- [grill-with-docs](https://github.com/mattpocock/skills/blob/main/docs/engineering/grill-with-docs.md)
- [Azure DevOps MCP](https://github.com/microsoft/azure-devops-mcp)
