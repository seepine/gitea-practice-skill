# 评审修订流程

> 注意：遵循一次只处理一个 PullRequest 的原则执行

## 流程概览

```
┌─────────────────────────────────────────────────────────────────┐
│                    阶段一：发现需要修订的 PR                      │
├─────────────────────────────────────────────────────────────────┤
│ 1. 获取当前用户创建的 Pull Request                                 │
│ 2. 筛选出存在评审意见为 "rejected" 状态的 PR                        │
│ 3. 确认对应的 Issue 编号和仓库信息                                  │
└─────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│                    阶段二：更新工单状态                             │
├─────────────────────────────────────────────────────────────────┤
│ 1. 移除工单标签 `status/reviewing`                                 │
│ 2. 添加工单标签 `status/revising`                                  │
└─────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│                    阶段三：本地修订开发                             │
├─────────────────────────────────────────────────────────────────┤
│ 1. 检查本地是否存在对应工作目录 ${repo}_issue_${index}              │
│ 2. 若不存在，参照【处理工单流程，阶段一：事前准备】创建工作目录        │
│ 3. 同步上游最新代码                                                │
│ 4. 根据评审意见进行代码修订                                        │
│ 5. 提交代码并推送                                                  │
└─────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│                    阶段四：提交修订并更新状态                       │
├─────────────────────────────────────────────────────────────────┤
│ 1. 推送修订后的分支                                                │
│ 2. 移除工单标签 `status/revising`                                  │
│ 3. 添加工单标签 `status/reviewing`                                 │
└─────────────────────────────────────────────────────────────────┘
```

## 阶段一：发现需要修订的 PR

### 1.1 获取当前用户创建的 Pull Request

使用 `pull_request__list_my` 工具获取当前用户创建的所有 PR：

```json
{
  "state": "open"
}
```

### 1.2 筛选存在评审失败的 PR

对于每个 PR，使用 `pull_request__list_reviews` 获取评审列表：

```json
{
  "owner": "仓库所有者",
  "repo": "仓库名称",
  "index": PR编号
}
```

筛选条件：
- 存在评审意见状态为 `rejected` 的评审。
- 对应工单标签必须为 `status/reviewing`
- 找到一个 PR 符合条件的就往下执行

### 1.3 确认 Issue 信息

从 PR 信息中提取关联的 Issue 编号，PR body 中通常包含 `Fix #<issue_index>` 或类似引用。

## 阶段二：更新工单状态

### 2.1 移除工单标签 `status/reviewing`

通过 `issue__remove_labels` 移除工单标签

```json
{
  "owner": "仓库所有者",
  "repo": "仓库名称",
  "index": 工单索引,
  "labels": ["status/reviewing"]
}
```

### 2.2 添加工单标签 `status/revising`

通过 `issue__add_labels` 添加工单标签

```json
{
  "owner": "仓库所有者",
  "repo": "仓库名称",
  "index": 工单索引,
  "labels": ["status/revising"]
}
```

## 阶段三：本地修订开发

### 3.1 检查本地工作目录

检查本地是否存在对应工作目录：

```bash
ls -d workspace_gitea/${repo}_issue_${index}
```

### 3.2 若不存在，参照【处理工单流程，阶段一：事前准备】创建工作目录

参照 [issue-workflow.md](issue-workflow.md) 中的阶段一步骤：

1. 检查并 Fork 仓库（如未 Fork）
2. 克隆 Fork 的仓库到 `${repo}_issue_${index}` 目录
3. 添加上游仓库远程引用

```bash
cd <your_workspace_dir>/workspace_gitea
git clone https://gitea.example.com/${your_username}/${repo}.git ${repo}_issue_${index}
cd ${repo}_issue_${index}
git remote add upstream https://gitea.example.com/${上游仓库owner}/${repo}.git
# 创建对应工单开发分支
git checkout -b dev/issue_${index}
```

### 3.3 同步上游最新代码

同步上游最新代码到工单开发分支，参考命令如下，请注意合并上游最新代码时可能存在冲突，需要先解决冲突

```bash
# 确保处于对应工单分支
git checkout dev/issue_${index}
# 从远程获取 main 分支最新提交
git fetch upstream main
# 合并上游 main 分支代码到开发分支
git merge upstream/main
```

### 3.4 根据评审意见进行代码修订

使用 `pull_request__get_review_comments` 获取评审的具体意见：

```json
{
  "owner": "仓库所有者",
  "repo": "仓库名称",
  "index": PR编号,
  "reviewId": 评审ID
}
```

根据评审意见逐条修改代码。

### 3.5 提交并推送代码

```bash
git add .
git commit -m '<type>: <subject>

- 根据评审意见修订'
git push origin dev/issue_${index}
```

## 阶段四：提交修订并更新状态

### 4.1 推送修订后的分支

确保分支已推送到远程仓库。


### 4.2 给工单添加评论说明修订内容

使用 `issue__comment_create` 添加评论，简要说明修订内容并请求重新评审：

```json
{
  "owner": "仓库所有者",
  "repo": "仓库名称",
  "index": 工单索引,
  "body": "### Revisions\n\n已完成评审意见的修订，请重新评审。\n\n修订内容：\n- <修订点1>\n- <修订点2>\n\n..."
}
```

### 4.3 移除工单标签 `status/revising`

通过 `issue__remove_labels` 移除工单标签

```json
{
  "owner": "仓库所有者",
  "repo": "仓库名称",
  "index": 工单索引,
  "labels": ["status/revising"]
}
```

### 4.4 添加工单标签 `status/reviewing`

通过 `issue__add_labels` 添加工单标签

```json
{
  "owner": "仓库所有者",
  "repo": "仓库名称",
  "index": 工单索引,
  "labels": ["status/reviewing"]
}
```

## 注意事项

1. **评审状态判断**：Gitea 评审状态包括 `approved`、`rejected`、`comment`、`request`，只有 `rejected` 需要修订
2. **保持分支同步**：在开始修订前，先从 upstream 拉取最新代码
3. **逐条处理评审意见**：根据评审意见逐条修改(除非说的是同一个问题)，确保每条意见都得到响应
4. **标签状态互斥**：同一工单同时只能有一个状态标签
