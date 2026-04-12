---
name: gitea-practice-skill
description: 当需要通过 gitea-mcp 处理 Gitea 工单任务时使用，包括认领工单、创建分支开发、提交 Pull Request、或根据评审意见进行修订等场景
metadata: 
  version: 0.1.0
---

# Gitea Practice Skill

## gitea-mcp

`gitea-mcp` 提供了常见的的 gitea 接口，支持通过 Model Context Protocol 与 Gitea 实例交互。

若需要了解更多关于 `gitea-mcp` 介绍，可查看 [references/gitea-mcp.md](references/gitea-mcp.md)。

## 工单状态管理

基于工单即任务的理念，我们通过标签系统对工单状态进行流转管理。每个工单在同一时刻应只具有一个状态标签。

### 状态标签定义

> 若标签不存在则创建

- `status/draft` - 草稿：工单创建中，内容尚未完善，不进入处理队列
- `status/pending` - 等待中：工单已就绪，等待分配或认领
- `status/working` - 进行中：已有人员正在处理该工单
- `status/reviewing` - 评审中：相关代码/成果已提交，等待评审
- `status/revising` - 修订中：根据评审意见进行修订
- `status/finished` - 已完成：工单任务已处理完成
- `status/aborted` - 已终止：工单不再需要处理（取消、重复、无效等）

### 标签使用约定

1. **状态标签互斥**：一个工单同一时刻只能有一个状态标签
2. **标签前缀**：所有状态标签使用 `status:` 前缀，便于分类和管理
3. **标签先行**：所有操作

## 场景A：处理工单流程

获取指派给自己的工单，并通过派生、克隆、多分支等系列流程进行开发，最后推送代码并创建 Pull Request 请求合并到主仓库，完成工单任务。

具体流程和细节请查看 [references/issue-workflow.md](references/issue-workflow.md)。

## 场景B：评审修订流程

创建 Pull Request 后会由专人评审，当评审失败时，需要根据评审意见进行修订。

具体流程和细节请查看 [references/fix-review-workflow.md](references/fix-review-workflow.md)。
