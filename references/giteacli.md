# giteacli

通过命令行工具与 Gitea 实例交互。

## 安装

```bash
npm install -g @seepine/giteacli
# 或
pnpm add -g @seepine/giteacli
```

## 认证配置

```bash
# 登录到 Gitea 实例
giteacli login --host <host> --token <token>

# 查看当前登录用户
giteacli whoami
```

## 输出格式

默认输出 JSON，可设置为 toon 格式便于阅读：

```bash
giteacli config set --key format --value toon
```

## 可用命令

### Repository

| 命令 | 描述 |
| ---- | ---- |
| `giteacli repo list` | 列出当前用户的仓库 |
| `giteacli repo add --name <name>` | 创建新仓库 |
| `giteacli repo fork --owner <owner> --repo <repo>` | Fork 仓库 |

### Repository Labels

| 命令 | 描述 |
| ---- | ---- |
| `giteacli repo label list --owner <owner> --repo <repo>` | 列出仓库的所有标签 |
| `giteacli repo label add --owner <owner> --repo <repo> --name <name> --color <color>` | 创建新标签 |
| `giteacli repo label edit --owner <owner> --repo <repo> --id <id>` | 编辑标签 |
| `giteacli repo label del --owner <owner> --repo <repo> --id <id>` | 删除标签 |

### Issue

| 命令 | 描述 |
| ---- | ---- |
| `giteacli issue get --owner <owner> --repo <repo> --index <index>` | 获取单个 Issue |
| `giteacli issue list --owner <owner> --repo <repo>` | 列出仓库的 Issue |
| `giteacli issue search [options]` | 搜索 Issue |
| `giteacli issue add --owner <owner> --repo <repo> --title <title> --body <body>` | 创建新 Issue |
| `giteacli issue edit --owner <owner> --repo <repo> --index <index>` | 编辑 Issue |
| `giteacli issue add-labels --owner <owner> --repo <repo> --index <index> --labels <labels>` | 添加标签 |
| `giteacli issue del-labels --owner <owner> --repo <repo> --index <index> --labels <labels>` | 移除标签 |

#### Issue Search 选项

```bash
giteacli issue search [options]

Options:
  --state <enum>        Filter by state (open, closed, all)
  --labels <string>     Filter by labels (comma-separated)
  --assigned <boolean>  Issues assigned to me
  --created <boolean>   Issues created by me
  --page <number>       Page number
  --limit <number>      Items per page, default 30
  --q <string>          Search keyword
```

#### Issue Comments

| 命令 | 描述 |
| ---- | ---- |
| `giteacli issue comment list --owner <owner> --repo <repo> --index <index>` | 列出 Issue 的评论 |
| `giteacli issue comment add --owner <owner> --repo <repo> --index <index> --body <body>` | 添加评论 |
| `giteacli issue comment edit --owner <owner> --repo <repo> --commentId <commentId> --body <body>` | 编辑评论 |

### Pull Request

| 命令 | 描述 |
| ---- | ---- |
| `giteacli pr list --owner <owner> --repo <repo>` | 列出仓库的 Pull Request |
| `giteacli pr search [options]` | 搜索 Pull Request |
| `giteacli pr get --owner <owner> --repo <repo> --index <index>` | 获取单个 Pull Request |
| `giteacli pr add --owner <owner> --repo <repo> --title <title> --head <head> --base <base>` | 创建新 Pull Request |
| `giteacli pr edit --owner <owner> --repo <repo> --index <index>` | 编辑 Pull Request |
| `giteacli pr comments --owner <owner> --repo <repo> --index <index>` | 获取 PR 评论 |
| `giteacli pr reviews --owner <owner> --repo <repo> --index <index>` | 列出 PR 审核 |
| `giteacli pr tbd` | 查找第一个需要修订的 PR (REQUEST_CHANGES review) |

#### PR Search 选项

```bash
giteacli pr search [options]

Options:
  --state <enum>        Filter by state
  --assigned <boolean>  PRs assigned to me
  --created <boolean>   PRs created by me
  --page <number>       Page number
  --limit <number>      Items per page, default 30
```

### Pull Request Reviewers

| 命令 | 描述 |
| ---- | ---- |
| `giteacli pr reviewer add --owner <owner> --repo <repo> --index <index> --username <username>` | 添加评审者 |
| `giteacli pr reviewer del --owner <owner> --repo <repo> --index <index> --username <username>` | 移除评审者 |
| `giteacli pr reviewer review --owner <owner> --repo <repo> --index <index> --username <username>` | 请求重新评审 |
