# Single Bug Fix Workflow

单 bug 修复的完整工作流。

## 概述

从发现 bug 到修复、测试、PR 的完整流程。

---

## 📋 Prerequisites

- Git 已配置
- 项目已 clone
- Claude Code 已安装
- OpenClaw 正在运行

---

## Step 1: 理解 Bug（5-10 分钟）

### 收集信息

```
Bug 描述: [用户或 QA 报告的 bug]
影响范围: [严重性: Critical/High/Medium/Low]
复现步骤:
1. [步骤 1]
2. [步骤 2]
3. [步骤 3]

期望结果: [应该发生什么]
实际结果: [实际发生了什么]
错误日志/截图: [如果有]
```

### 快速验证

```bash
# 尝试复现 bug
# 查看相关代码
# 检查日志
```

**输出：** 一句话总结 bug

```
登录时，如果数据库连接超时（>5s），用户会收到 500 错误而不是友好的错误消息
```

---

## Step 2: 创建隔离工作区（1 分钟）

```bash
# 进入项目目录
cd ~/Projects/your-project

# 确保在 main 或 develop
git checkout main  # 或 git checkout develop

# 创建 worktree
git worktree add -b fix/login-timeout-error ~/tmp-workspace/login-timeout main
```

**验证：**
```bash
git worktree list
# 应该看到新的 worktree
```

---

## Step 3: 启动 Claude Code（30 秒）

```bash
# 在 OpenClaw 中执行
bash pty:true workdir:~/tmp-workspace/login-timeout background:true command:"claude 'Fix the bug: When database connection times out (>5s), users receive 500 error instead of friendly error message.

Steps:
1. Find the login/auth related files
2. Read the error handling code
3. Identify where timeout errors occur
4. Add proper timeout handling with friendly error message
5. Add logging to track timeout occurrences
6. Write a test that simulates timeout
7. Run tests to ensure nothing breaks

Requirements:
- Keep existing functionality intact
- Add clear error messages
- Log timeouts for monitoring

When completely finished, run:
openclaw gateway wake --text \"✅ Bug 修复完成：登录超时错误已处理，添加了友好错误消息和日志\" --mode now'"
```

**你收到：**
- Session ID（用于监控）
- 简短确认："🚀 Claude Code 已启动..."

---

## Step 4: 监控进度（可选）

```bash
# 检查 Claude 的输出
process action:log sessionId:XXX

# 查看所有后台任务
process action:list
```

**典型的 Claude 输出：**
```
> Searching for auth-related files...
> Found: auth_service.py, login_handler.py, middleware.py
> Reading auth_service.py...
> Found timeout configuration at line 45 (5s)
> Reading error handling code...
> No timeout-specific error handling found
> Adding timeout handler...
> [代码修改中...]
> Adding logging...
> Writing test for timeout scenario...
> Running tests...
> ✅ All tests passed
```

---

## Step 5: 收到完成通知（自动）

Claude 完成后，你会收到通知：

```
✅ Bug 修复完成：登录超时错误已处理，添加了友好错误消息和日志
```

---

## Step 6: 验证修复（5-10 分钟）

```bash
# 进入 worktree
cd ~/tmp-workspace/login-timeout

# 查看修改
git status
git diff

# 运行测试
npm test  # 或 pytest, make test 等

# 查看具体修改的文件
git diff auth_service.py
```

**检查清单：**
- [ ] Bug 真的被修复了吗？
- [ ] 没有破坏现有功能吗？
- [ ] 测试覆盖了新代码吗？
- [ ] 错误消息友好吗？
- [ ] 代码质量可接受吗？

---

## Step 7: 本地测试（重要）

```bash
# 如果可能，本地测试修复
# 复现 bug 确认已修复

# 运行完整测试套件
npm test
# 或
pytest
```

---

## Step 8: 提交并推送（2 分钟）

```bash
# 提交更改
git add .
git commit -m "fix: handle database timeout with friendly error

- Added timeout error handling in auth_service
- Log timeout occurrences for monitoring
- Friendly error message shown to users
- Added test for timeout scenario

Fixes: #123"
```

```bash
# 推送到远程（第一次可能需要认证）
git push -u origin fix/login-timeout
```

---

## Step 9: 创建 PR（2 分钟）

```bash
# 创建 PR
gh pr create \
  --title "fix: handle database timeout with friendly error" \
  --body "## Bug Description
When database connection times out (>5s), users receive 500 error instead of friendly error message.

## Changes
- Added timeout error handling in \`auth_service.py\`
- Log timeout occurrences for monitoring
- Friendly error message shown to users
- Added test for timeout scenario

## Testing
- Local testing confirmed timeout handling
- All existing tests pass
- New test covers timeout scenario

Closes #123"
```

---

## Step 10: 清理（PR 合并后）

```bash
# 等待 PR 审查和合并...

# 合并后，清理 worktree
git worktree remove ~/tmp-workspace/login-timeout

# 删除本地分支
git branch -d fix/login-timeout
```

---

## ⏱️ 时间估算

| Step | 时间 |
|------|------|
| Step 1: 理解 Bug | 5-10 分钟 |
| Step 2: 创建 Worktree | 1 分钟 |
| Step 3: 启动 Claude Code | 30 秒 |
| Step 4: 监控进度 | 可选（等待时） |
| Step 5: 收到通知 | 自动 |
| Step 6: 验证修复 | 5-10 分钟 |
| Step 7: 本地测试 | 2-5 分钟 |
| Step 8: 提交推送 | 2 分钟 |
| Step 9: 创建 PR | 2 分钟 |
| **总计** | **20-35 分钟** |

---

## 🎯 关键原则

### 1. 隔离工作区
- 使用 git worktree 避免影响主分支
- 可以随时删除重来

### 2. 明确的 Prompt
- 具体描述 bug
- 给出清晰的步骤
- 说明要求（日志、测试等）

### 3. 自动通知
- 使用 `wake` 让 Claude 完成后通知你
- 期间可以去做其他事

### 4. 验证很重要
- 不要盲目信任 AI 的修复
- 亲自查看代码变更
- 本地测试确认

### 5. 良好的提交信息
- 遵循 conventional commits
- 说明改了什么，为什么改
- 关联 issue 编号

---

## 🚨 常见陷阱

### ❌ 不验证就提交
```bash
# Claude 完成了，直接推送
git push  # 危险！
```

✅ **应该先验证**
```bash
git diff  # 看看改了什么
npm test  # 确保测试通过
```

### ❌ Prompt 太模糊
```
Fix the login bug.
```

✅ **应该具体**
```
Fix the bug: When database connection times out (>5s),
users receive 500 error instead of friendly error message.
Add logging and friendly error message.
```

### ❌ 忘记测试
```
Just fix the bug.
```

✅ **应该要求测试**
```
Fix the bug and add a test that covers this scenario.
```

---

## 📝 示例：完整 Prompt 模板

```bash
bash pty:true workdir:~/tmp-workspace/bug-name background:true command:"claude 'Fix this bug: [concise bug description]

Context: [relevant context, severity, impact]

Steps:
1. Find related files (search for [keywords])
2. Read and understand the current implementation
3. Identify the root cause
4. Implement the fix
5. Add logging for monitoring (if applicable)
6. Write a test that covers this bug scenario
7. Run all tests to ensure nothing breaks

Requirements:
- Keep existing functionality intact
- Add clear error messages (if error handling)
- Document any breaking changes

When completely finished, run:
openclaw gateway wake --text \"✅ [Bug name] 修复完成\" --mode now'"
```

---

## 🔄 快速命令参考

```bash
# 创建 worktree
git worktree add -b fix/bug-name ~/tmp-workspace/bug-name main

# 启动 Claude
bash pty:true workdir:~/tmp-workspace/bug-name background:true command:"claude '...'"

# 检查进度
process action:log sessionId:XXX

# 查看修改
cd ~/tmp-workspace/bug-name && git diff

# 运行测试
npm test  # 或 pytest

# 提交推送
git add . && git commit -m "fix: ..."
git push -u origin fix/bug-name

# 创建 PR
gh pr create --title "fix: ..." --body "..."

# 清理
git worktree remove ~/tmp-workspace/bug-name
```

---

**准备好开始了吗？** 🚀

告诉我你想修复什么 bug，我帮你执行这个工作流！
