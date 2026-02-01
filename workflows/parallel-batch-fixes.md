# Parallel Batch Fixes Workflow

批量并行修复多个 bug 的工作流。

## 概述

使用多个 Claude Code 实例并行修复多个 bug，大幅提升效率。

---

## 📋 适用场景

- 有多个独立的 bug
- Bug 不相互依赖
- 每个修复相对独立（小到中等复杂度）
- 需要快速处理多个 issues

---

## ⏱️ 效率对比

### 传统方式（串行）
```
Bug #1: 30 分钟
Bug #2: 30 分钟
Bug #3: 30 分钟
Bug #4: 30 分钟
─────────────
总计: 2 小时
```

### 并行方式
```
同时修复 Bug #1-4:
- 启动: 2 分钟
- 等待: 30 分钟
- 验证: 10 分钟
─────────────
总计: 42 分钟
```

**节省 65% 时间！**

---

## Step 1: 准备 Bug 列表（5 分钟）

### 创建 bug 列表文件

```bash
# 创建 bug 文件
cat > ~/tmp-workspace/bugs.txt << 'EOF'
Bug #1: Login button doesn't work on mobile
Bug #2: User profile image doesn't update
Bug #3: Search returns duplicate results
Bug #4: Export CSV encoding issue
EOF
```

### 确认独立性

```
Bug #1: Login - 独立 ✅
Bug #2: Profile image - 独立 ✅
Bug #3: Search - 独立 ✅
Bug #4: Export - 独立 ✅

可以并行修复！
```

---

## Step 2: 创建多个 Worktrees（2 分钟）

```bash
# 进入项目
cd ~/Projects/your-project
git checkout main

# 为每个 bug 创建 worktree
git worktree add -b fix/mobile-login ~/tmp-workspace/mobile-login main
git worktree add -b fix/profile-image ~/tmp-workspace/profile-image main
git worktree add -b fix/search-duplicates ~/tmp-workspace/search-duplicates main
git worktree add -b fix/csv-encoding ~/tmp-workspace/csv-encoding main

# 验证
git worktree list
```

---

## Step 3: 启动多个 Claude Code（5 分钟）

### Bug #1: Mobile Login

```bash
bash pty:true workdir:~/tmp-workspace/mobile-login background:true command:"claude 'Fix the bug: Login button doesn't work on mobile devices.

The button is unresponsive when tapped on mobile browsers.
Works fine on desktop.

Steps:
1. Find the login button code
2. Check CSS and event handlers for mobile issues
3. Fix the responsiveness issue
4. Test on mobile viewport
5. Add mobile-specific test if possible

When done, run: openclaw gateway wake --text \"✅ Bug #1: Mobile login fixed\" --mode now'"
```

### Bug #2: Profile Image

```bash
bash pty:true workdir:~/tmp-workspace/profile-image background:true command:"claude 'Fix the bug: User profile image doesn't update after upload.

After user uploads a new image, the old image still shows.
Refresh doesn't help.

Steps:
1. Find the profile image upload code
2. Check the update logic
3. Fix the image update/caching issue
4. Test upload and update flow
5. Add test for image update

When done, run: openclaw gateway wake --text \"✅ Bug #2: Profile image update fixed\" --mode now'"
```

### Bug #3: Search Duplicates

```bash
bash pty:true workdir:~/tmp-workspace/search-duplicates background:true command:"claude 'Fix the bug: Search returns duplicate results.

When searching for items, some results appear multiple times.
This happens especially with pagination.

Steps:
1. Find the search query code
2. Check for JOIN or GROUP BY issues
3. Fix the duplicate problem
4. Test with various search terms
5. Add test for duplicate prevention

When done, run: openclaw gateway wake --text \"✅ Bug #3: Search duplicates fixed\" --mode now'"
```

### Bug #4: CSV Encoding

```bash
bash pty:true workdir:~/tmp-workspace/csv-encoding background:true command:"claude 'Fix the bug: Exported CSV has encoding issues with non-ASCII characters.

Chinese characters appear as garbage (e.g., ä¹±ç) in exported CSV.
Excel doesn't display correctly.

Steps:
1. Find the CSV export code
2. Fix the encoding (should be UTF-8 with BOM for Excel)
3. Test with Chinese and other Unicode characters
4. Add test for CSV encoding

When done, run: openclaw gateway wake --text \"✅ Bug #4: CSV encoding fixed\" --mode now'"
```

---

## Step 4: 监控所有实例（可选）

```bash
# 查看所有后台任务
process action:list

# 查看特定实例的进度
process action:log sessionId:XXX

# 检查哪个实例还在运行
process action:poll sessionId:XXX
```

---

## Step 5: 逐个验证修复（10-15 分钟）

当所有实例完成后（会收到多个通知），逐个验证：

### Bug #1: Mobile Login
```bash
cd ~/tmp-workspace/mobile-login
git diff
npm test
# Review the fix
```

### Bug #2: Profile Image
```bash
cd ~/tmp-workspace/profile-image
git diff
npm test
# Review the fix
```

### Bug #3: Search Duplicates
```bash
cd ~/tmp-workspace/search-duplicates
git diff
npm test
# Review the fix
```

### Bug #4: CSV Encoding
```bash
cd ~/tmp-workspace/csv-encoding
git diff
npm test
# Review the fix
```

---

## Step 6: 提交所有修复（5 分钟）

```bash
# Bug #1
cd ~/tmp-workspace/mobile-login
git add .
git commit -m "fix: login button now works on mobile

- Fixed event handler for mobile touch events
- Added mobile-specific CSS z-index fix
- Added test for mobile viewport

Fixes #101"
git push -u origin fix/mobile-login

# Bug #2
cd ~/tmp-workspace/profile-image
git add .
git commit -m "fix: profile image updates correctly after upload

- Fixed cache invalidation after image update
- Force-refresh image display
- Added test for image update flow

Fixes #102"
git push -u origin fix/profile-image

# Bug #3
cd ~/tmp-workspace/search-duplicates
git add .
git commit -m "fix: search no longer returns duplicate results

- Added DISTINCT to query
- Fixed JOIN causing duplicates
- Added pagination test

Fixes #103"
git push -u origin fix/search-duplicates

# Bug #4
cd ~/tmp-workspace/csv-encoding
git add .
git commit -m "fix: CSV export handles Unicode characters correctly

- Use UTF-8 with BOM for Excel compatibility
- Properly encode Chinese and other Unicode
- Added encoding test

Fixes #104"
git push -u origin fix/csv-encoding
```

---

## Step 7: 批量创建 PR（5 分钟）

```bash
# Bug #1
cd ~/tmp-workspace/mobile-login
gh pr create --title "fix: login button works on mobile" --body "Fixes #101"

# Bug #2
cd ~/tmp-workspace/profile-image
gh pr create --title "fix: profile image updates correctly" --body "Fixes #102"

# Bug #3
cd ~/tmp-workspace/search-duplicates
gh pr create --title "fix: search no longer returns duplicates" --body "Fixes #103"

# Bug #4
cd ~/tmp-workspace/csv-encoding
gh pr create --title "fix: CSV export handles Unicode" --body "Fixes #104"
```

---

## Step 8: 清理（PR 合并后）

```bash
# 清理所有 worktrees
git worktree remove ~/tmp-workspace/mobile-login
git worktree remove ~/tmp-workspace/profile-image
git worktree remove ~/tmp-workspace/search-duplicates
git worktree remove ~/tmp-workspace/csv-encoding

# 删除本地分支
git branch -d fix/mobile-login
git branch -d fix/profile-image
git branch -d fix/search-duplicates
git branch -d fix/csv-encoding
```

---

## 🎯 关键原则

### 1. 确保独立性
```
✅ 可以并行：登录 bug, 搜索 bug, 导出 bug
❌ 不能并行：登录 bug（依赖用户表）, 用户表修改 bug
```

### 2. 统一的 Prompt 格式
- 每个 bug 使用相同的结构
- 便于理解和比较

### 3. 批量验证
- 不要等到全部完成再验证
- 逐个验证更不容易遗漏

### 4. 资源管理
- 不要同时启动太多（建议 3-5 个）
- 系统可能会变慢

---

## 🚨 常见问题

### Q: 如何处理依赖关系？

**A:** 先修复基础 bug，再修复依赖它的 bug
```
阶段 1: 修复数据库 bug（基础）
阶段 2: 并行修复 UI bug, API bug（依赖数据库）
```

### Q: Claude 太慢怎么办？

**A:** 调整数量或增加等待时间
```
启动 2-3 个而不是 5 个
或者等待更久（45-60 分钟）
```

### Q: 如何确保质量？

**A:**
- 每个 bug 都要审查代码
- 运行完整测试套件
- 本地测试（如果可能）

### Q: 工作空间太多，混乱怎么办？

**A:**
```
# 查看所有 worktrees
git worktree list

# 使用统一的目录命名
~/tmp-workspace/bug-name-1
~/tmp-workspace/bug-name-2

# 清理完成后统一删除
rm -rf ~/tmp-workspace/*
```

---

## 📝 自动化脚本（可选）

### 批量启动脚本

```bash
#!/bin/bash
# parallel-fixes.sh

BUGS=(
  "mobile-login:Login button doesn't work on mobile"
  "profile-image:User profile image doesn't update"
  "search-duplicates:Search returns duplicate results"
  "csv-encoding:Export CSV encoding issue"
)

PROJECT_DIR=~/Projects/your-project
cd $PROJECT_DIR
git checkout main

for bug in "${BUGS[@]}"; do
  IFS=: read -r name description <<< "$bug"
  echo "Creating worktree for: $name"
  git worktree add -b fix/$name ~/tmp-workspace/$name main
done

echo "All worktrees created. Starting Claude Code instances..."

# 手动启动每个实例
# （因为每个 prompt 可能不同）
```

---

## ⏱️ 时间估算（4 个 bug）

| Step | 时间 |
|------|------|
| Step 1: 准备列表 | 5 分钟 |
| Step 2: 创建 Worktrees | 2 分钟 |
| Step 3: 启动 Claude | 5 分钟 |
| Step 4: 监控进度 | 可选（等待） |
| Step 5: 验证修复 | 10-15 分钟 |
| Step 6: 提交推送 | 5 分钟 |
| Step 7: 创建 PR | 5 分钟 |
| **总计** | **32-42 分钟** |

**串行方式：** 2 小时
**并行方式：** 42 分钟
**节省：** 65% 时间

---

## 🚀 准备好批量修复了吗？

告诉我你想修复哪些 bug，我帮你：
1. 创建 worktrees
2. 启动多个 Claude 实例
3. 监控进度
4. 完成后帮你验证和提交
