# Code Review Prompt Template

代码审查的 prompt 模板。

## 场景

- PR 审查
- 代码质量检查
- 安全审计
- 最佳实践检查

## Prompt 模板

```
Review this code: [diff or file]

Focus on:
- Bugs and potential issues
- Security vulnerabilities
- Performance problems
- Code style and best practices
- Test coverage

对于每个问题：
1. Severity: Critical/High/Medium/Low
2. Description: Clear explanation
3. Suggested fix: Code example if possible
4. Location: File and line number

Format: Provide as a markdown report with sections for each issue.

Context:
- [Relevant context: this is payment code, user-facing, etc.]
```

## 实际例子

### 例子：审查支付模块 PR

```
Review this PR for the payment module.

Focus on:
- Security: No API key leaks, proper validation, no injection risks
- Bugs: Edge cases, error handling
- Performance: Database queries, unnecessary loops
- Best practices: Code organization, naming, comments

This code processes real payments, so security is critical.

对于每个问题：
1. Severity: Critical/High/Medium/Low
2. Description: Clear explanation
3. Suggested fix: Code example
4. Location: File and line number

Here's the diff: [paste diff]
```

### 例子：全面代码审查

```
Review the auth module (auth_service.py, auth_middleware.py).

Check for:
- Common security issues (XSS, CSRF, SQL injection, auth bypass)
- Error handling and edge cases
- Performance bottlenecks
- Code duplication
- Missing tests
- Deprecated APIs or patterns

Context: This module handles user authentication and session management.

Provide:
1. Summary of findings
2. Critical issues (must fix before merge)
3. Suggestions for improvement
4. Positive aspects (what's done well)
```

## 审查维度

### 安全 🔒
- SQL 注入
- XSS
- CSRF
- 认证绕过
- 权限提升
- 敏感信息泄露

### 正确性 🐛
- 边界条件
- 错误处理
- 并发问题
- 数据一致性
- 空值/undefined 处理

### 性能 ⚡
- 不必要的循环
- N+1 查询
- 内存泄漏
- 缺少缓存
- 低效算法

### 可维护性 📝
- 命名清晰
- 注释充分
- 模块化
- DRY（不重复）
- 测试覆盖

## 输出格式

```markdown
# Code Review Report

## 关键问题（必须修复）
### Issue #1: SQL Injection Risk
- **Severity**: Critical
- **Location**: `auth.py:45`
- **Description**: User input is directly concatenated into SQL query...
- **Fix**: Use parameterized queries:
  ```python
  cursor.execute("SELECT * FROM users WHERE id = %s", (user_id,))
  ```

## 高优先级问题
### Issue #1: Missing Error Handling
...

## 中等优先级问题
...

## 低优先级问题
...

## 做得好的地方 🌟
- Good use of type hints
- Comprehensive test coverage
...
```
