# Gitea Practice Skill

基于 `gitea-mcp` 的 Gitea skill，通过标签系统管理工单状态，配合 Fork 和 Pull Request 机制完成工单开发。

## 核心概念

**工单即任务**：每个工单通过 `status/*` 标签追踪状态，状态标签互斥。

| 标签 | 含义 |
|------|------|
| `status/pending` | 等待中，等待分配 |
| `status/working` | 进行中 |
| `status/reviewing` | 评审中 |
| `status/revising` | 修订中 |
| `status/finished` | 已完成 |
| `status/aborted` | 已终止 |

## 安装 skill

执行以下命令，或者直接将仓库地址发给 `ClaudeCode`、`openclaw` 等 Agent。

```
npx -y skill add https://github.com/seepine/gitea-skill
```

## 安装 gitea-mcp

其中环境变量替换成你自己的， `GITEA_ACCESS_TOKEN` 可在 gitea 的【设置-应用】中生成，注意勾选所有读写权限。

建议为 AI 单独创建一个 gitea 账号，通过 `fork` 和 `pull request` 的最佳实践进行工作。

```json
{
  "mcpServers": {
    "gitea": {
      "command": "npx",
      "args": ["gitea-mcp"],
      "env": {
        "GITEA_HOST": "https://gitea.example.com",
        "GITEA_ACCESS_TOKEN": "your-token"
      }
    }
  }
}
```

## 使用示例

### 实践 A：认领并处理工单

```md
根据 gitea skill，完成第一个指派给我的工单
```

### 实践 B：修复评审失败的 PR

```md
根据 gitea skill，修复第一个未通过评审的PR
```