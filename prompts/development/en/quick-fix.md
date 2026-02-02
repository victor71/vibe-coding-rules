# Quick Fix Prompt Template

快速修复简单 bug 的 prompt 模板。

## 场景

- 简单的语法错误
- 明确的 bug（已知问题）
- 单文件修改
- 不影响整体架构

## Prompt 模板

```
Fix the bug in [file/area]: [concise bug description]

Steps:
1. Read the file and understand the issue
2. Make the fix
3. Add a test to prevent regression
4. Run tests to ensure nothing breaks

Context:
- [Any relevant context or constraints]
```

## 实际例子

### 例子 1：修复登录超时问题

```
Fix the login timeout issue in auth_service.py.

The timeout is currently set to 5 seconds but should be 30 seconds
for slow network connections.

Steps:
1. Read auth_service.py
2. Find the timeout configuration
3. Change it from 5 to 30 seconds
4. Add a comment explaining why 30s is appropriate
5. No tests needed, this is a configuration change
```

### 例子 2：修复空指针崩溃

```
Fix the null pointer crash in user_profile.js.

When user.profile is undefined, the app crashes at line 45.

Steps:
1. Read user_profile.js and find the issue
2. Add null check before accessing profile properties
3. Add a test case with undefined profile
4. Run all tests to ensure nothing breaks

Context: This is critical for user onboarding.
```

## 关键原则

1. **具体** - 说明文件、行号（如果知道）
2. **明确步骤** - 告诉 Claude 要做什么
3. **添加测试** - 防止回归
4. **提供上下文** - 相关的约束或重要性
