# 处理工单流程

基于工单即任务的理念，通过派生（Fork）和 Pull Request 机制完成工单任务开发。

## 流程概览

```
┌─────────────────────────────────────────────────────────────────┐
│                    阶段一：事前准备                               │
├─────────────────────────────────────────────────────────────────┤
│ 1. 获取指派给自己且含有 `status/pending` 标签的工单                 │
│ 2. 获取仓库信息与工单索引                                          │
│ 3. 检查仓库是否 Fork，没有则 Fork                                  │
│ 4. 克隆自己 Fork 的仓库                                           │
│ 5. 添加上游仓库远程引用                                            │
└─────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│                    阶段二：工单流转                                │
├─────────────────────────────────────────────────────────────────┤
│ 1. 给工单添加一条评论："I am working."                             │
│ 2. 移除工单标签 `status/pending`                                   │
│ 3. 添加工单标签 `status/working`                                   │
└─────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│                    阶段三：工单处理                                │
├─────────────────────────────────────────────────────────────────┤
│ 1. 查看工单详细信息和所有评论                                       │
│ 2. 详细分析工单需求                                                │
│ 3. 根据分析结果进行编码实现                                         │
│ 4. 使用 Git 提交代码                                               │
│ 5. 推送到远程仓库                                                 │
│ 6. 创建 Pull Request                                             │
│ 7. 更新工单标签：移除 `status/pending`                              │
│ 8. 更新工单标签：移除 `status/reviewing`                           │
└─────────────────────────────────────────────────────────────────┘
```

## 阶段一：事前准备

### 1.1 获取指派给自己的工单

使用 `issue__search_list` 工具搜索指派给自己的工单：

```json
{
  "type": "assigned",
  "state": "open",
  "labels": ["status/pending"]
}
```

### 1.2 获取仓库信息与工单索引

从工单中提取 `owner`、`repo` 和工单 `index` 编号。

### 1.3 检查并 Fork 仓库（如未 Fork）

通过 `repo__list_my` 检查是否已 Fork 仓库，例如上游仓库为 `owner/repo`，则应该存在仓库 `<my-username>/repo`，如未 Fork 则使用 `repo__fork` 工具。

### 1.4 克隆自己 Fork 的仓库

进入专属工作空间，例如在用户目录或者 openclaw/hermes 等专属目录下，创建文件夹 `workspace_gitea`

```bash
cd <your_workspace_gitea>
git clone https://gitea.example.com/${your_username}/${repo}.git
```

### 1.5 添加上游仓库远程引用

```bash
git remote add upstream https://gitea.example.com/${上游仓库owner}/${repo}.git
```

验证远程配置：

```bash
git remote -v
```

## 阶段二：工单流转

> 注意：仔细阅读工单描述和所有评论，若存在问题或有不明确的地方，请在工单中追加评论咨询，终止流程，不需要在往下流转

### 2.1 给工单添加评论 "I am working."

使用 `issue__comment_create` 添加评论：

```json
{
  "owner": "仓库所有者",
  "repo": "仓库名称",
  "index": 工单索引,
  "body": "I am working."
}
```

### 2.2 移除工单标签 `status/pending`

使用 `issue__remove_labels` 移除标签：

```json
{
  "owner": "仓库所有者",
  "repo": "仓库名称",
  "index": 工单索引,
  "labels": ["status/pending"]
}
```

### 2.3 添加工单标签 `status/working`

使用 `issue__add_labels` 添加标签：

```json
{
  "owner": "仓库所有者",
  "repo": "仓库名称",
  "index": 工单索引,
  "labels": ["status/working"]
}
```

## 阶段三：工单处理

### 3.1 查看工单详细信息和所有评论

使用 `issue__get_by_index` 获取工单详情：

```json
{
  "owner": "仓库所有者",
  "repo": "仓库名称",
  "index": 工单索引
}
```

使用 `issue__comment_list` 获取所有评论：

```json
{
  "owner": "仓库所有者",
  "repo": "仓库名称",
  "index": 工单索引
}
```

### 3.2 详细分析工单需求

仔细阅读工单描述和所有评论，理解：
- 工单要解决什么问题
- 期望的实现方案

### 3.3 根据分析结果进行编码实现

根据分析结果进行代码编写。编码前同步上游最新代码，并创建工单分支：

```bash
git checkout main
git pull upstream main
git checkout -b dev/issue_${工单索引}
```

### 3.4 使用 Git 提交代码

编码完成之后提交代码

```bash
git add .
git commit -m '提交信息'
```

提交信息格式参考项目历史，一般遵循：

```
<type>: <subject>

<body(optional)>
```

常用 type：
- `feat` - 新功能
- `fix` - 修复 bug
- `docs` - 文档变更
- `refactor` - 重构
- `test` - 测试相关
- `chore` - 构建/工具相关

### 3.5 推送到远程仓库

```bash
git push origin dev/issue_${工单索引}
```

### 3.6 创建 Pull Request

使用 `pull_request__create` 创建 PR：

```json
{
  "owner": "你的用户名",
  "repo": "仓库名称",
  "title": "<简短描述>",
  "body": "## Summary\n- <变更点1>\n- <变更点2>\n\n## Related Issue\nFix #<工单索引>",
  "head": "dev/issue_<工单索引>",
  "base": "main"
}
```

### 3.7 更新工单标签：移除 `status/pending`

```json
{
  "owner": "仓库所有者",
  "repo": "仓库名称",
  "index": 工单索引,
  "labels": ["status/pending"]
}
```

### 3.8 更新工单标签：移除 `status/reviewing`

```json
{
  "owner": "仓库所有者",
  "repo": "仓库名称",
  "index": 工单索引,
  "labels": ["status/reviewing"]
}
```

## 注意事项

1. **保持分支同步**：在开始新任务前，定期从 upstream/main 拉取最新代码
2. **频繁提交**：实现一个完整的小功能后即可提交，不要等到全部完成
3. **PR 描述清晰**：清晰说明解决的问题和变更内容
4. **标签状态互斥**：同一工单同时只能有一个状态标签
