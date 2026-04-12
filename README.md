# Gitea Skill

一款基于 `gitea-mcp` 的 Gitea skill，可借助 `gitea-mcp` 管理仓库、工单和 Pull Request。

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

### 实践 A

```md
根据 gitea skill，完成第一个指派给我的工单
```

### 实践 B

```md
根据 gitea skill，修复第一个未通过评审的PR
```