# Bad vs Good Prompts

对比展示 prompt 质量对结果的影响。

---

## 案例 1: Bug 修复

### ❌ Bad Prompt

```
Fix the login bug.
```

**问题：**
- 没有说明是什么 bug
- 没有 context
- 没有 requirements
- Claude 可能修复错误的地方

**可能的输出：**
- 修复了无关的 bug
- 没有添加测试
- 破坏了其他功能
- 需要多轮对话

---

### ✅ Good Prompt

```
Fix the bug: When users try to login with expired tokens,
they receive a 500 error instead of a friendly "session expired" message.

Context:
- This affects the login flow in auth_service.py
- Token expiration check at line 45
- Currently throws unhandled exception

Requirements:
- Add proper token expiration check
- Return 401 with message "Session expired, please login again"
- Log the expiration event for monitoring
- Add a test that simulates expired token

Ensure existing tests still pass.
```

**优点：**
- 具体的问题描述
- 明确的 context
- 清晰的 requirements
- 预期的输出格式

**更好的输出：**
- 精确修复问题
- 添加了测试
- 友好的错误消息
- 一次完成

---

## 案例 2: 新功能

### ❌ Bad Prompt

```
Add user notifications.
```

**问题：**
- 太模糊
- 不知道要什么样的通知
- 没有技术栈信息
- Claude 需要大量澄清

**可能的输出：**
- 问很多问题
- 实现了不需要的功能
- 没有考虑现有架构

---

### ✅ Good Prompt

```
Implement a user notification system.

Requirements:
1. Users can subscribe to different notification types (email, push, SMS)
2. Admin can send broadcast notifications
3. Users can manage their notification preferences via settings
4. Notifications are queued and sent asynchronously
5. Failed notifications are retried up to 3 times

Technical specifications:
- Use Redis for queue (already configured)
- Use existing email service in utils/email.py
- Use Firebase Cloud Messaging for push (credentials in .env)
- Use Twilio for SMS (credentials in .env)
- REST API: POST /api/notifications/send, GET /api/notifications/preferences
- WebSocket for real-time updates

Implementation steps:
1. Design notification schema (models/notification.py)
2. Create notification service (services/notifications.py)
3. Add API endpoints (api/notifications.py)
4. Implement queue worker (workers/notification_worker.py)
5. Add WebSocket support (websockets/notifications.py)
6. Build admin UI components (components/AdminNotifications.tsx)
7. Write unit and integration tests
8. Document API endpoints in docs/api.md

Deliverables:
- Complete implementation with all features
- Comprehensive tests (>90% coverage)
- API documentation
- Update CHANGELOG.md

Constraints:
- Must be production-ready with error handling and logging
- Maintain backward compatibility
- Follow existing code style
- Don't break existing notification preferences
```

**优点：**
- 详细的需求列表
- 明确的技术栈
- 分步实施计划
- 清晰的交付物
- 约束条件

---

## 案例 3: 代码审查

### ❌ Bad Prompt

```
Review this code.
```

**问题：**
- 不知道审查重点
- 没有指定审查标准
- 可能给出无关的建议

---

### ✅ Good Prompt

```
Review the authentication module (auth_service.py and auth_middleware.py).

Focus on:
- Security: SQL injection, XSS, CSRF, auth bypass, privilege escalation
- Bugs: Edge cases, error handling, race conditions
- Performance: Database queries, unnecessary operations
- Code quality: Style, duplication, complexity, naming

Context:
- This module handles user authentication and JWT token management
- It's critical for security, any vulnerability is unacceptable
- It processes ~10,000 requests per hour

For each issue found, provide:
1. Severity: Critical/High/Medium/Low
2. File and line number
3. Clear description of the issue
4. Suggested fix with code example
5. Why this matters (security risk, bug, performance, etc.)

Format output as a markdown report with sections:
- Critical Issues (Must Fix)
- High Priority Issues
- Medium Priority Issues
- Low Priority Issues
- What's Done Well 🌟
```

**优点：**
- 明确审查重点
- 指定输出格式
- 提供 context
- 清晰的严重程度标准

---

## 案例 4: 性能优化

### ❌ Bad Prompt

```
Make it faster.
```

**问题：**
- 不知道优化什么
- 没有基准数据
- 可能过度优化

---

### ✅ Good Prompt

```
Optimize the /api/reports endpoint.

Current performance:
- Response time: 8-12 seconds
- Memory usage: ~500MB per request
- Database queries: 200+ queries
- Users report timeouts and slow loading

Target:
- Response time: <2 seconds
- Memory usage: <100MB
- Database queries: <10 queries

Context:
- This endpoint generates sales reports with charts
- Currently fetches all data, then filters in memory
- Uses heavy aggregation in Python instead of database
- No pagination or caching

Steps:
1. Profile the endpoint to identify bottlenecks
2. Move aggregations to database (SQL)
3. Add database indexes
4. Implement pagination
5. Add Redis caching for common queries
6. Stream results instead of loading all in memory
7. Add benchmarks to verify improvement

Requirements:
- Maintain the same API contract
- Keep the same report accuracy
- Add logging for cache hits/misses
- Document the optimization strategy

After optimization, provide:
1. Before/after benchmarks
2. Query explain plans
3. Cache hit/miss statistics
```

**优点：**
- 明确当前问题
- 清晰的目标
- 详细的优化步骤
- 可测量的指标

---

## 案例 5: 重构

### ❌ Bad Prompt

```
Refactor the code.
```

**问题：**
- 不知道为什么重构
- 没有重构目标
- 可能破坏现有功能

---

### ✅ Good Prompt

```
Refactor the payment processing module (services/payments.py).

Current state:
- Direct SQL queries mixed with business logic
- No separation of concerns
- Difficult to test
- Error handling is inconsistent
- Duplicate code in multiple payment methods

Target state:
- Clean architecture with separated layers
- Repository pattern for database access
- Service layer for business logic
- Dependency injection for testability
- Consistent error handling

Requirements:
1. Maintain backward compatibility (same API)
2. All existing tests must still pass
3. Add new unit tests for refactored code
4. Don't break existing integrations
5. Document the new architecture

Constraints:
- Cannot change the database schema
- Must maintain the same external API
- No breaking changes for clients
- Maintain performance (benchmark before/after)

Implementation plan:
Phase 1: Design new architecture (just design, no code)
Phase 2: Create new repository layer
Phase 3: Create new service layer
Phase 4: Migrate existing code incrementally
Phase 5: Update tests
Phase 6: Performance benchmark
Phase 7: Documentation

After each phase, run tests to ensure nothing breaks.

When complete, provide:
1. Architecture diagram
2. Migration notes
3. Performance comparison
4. Test coverage report
```

**优点：**
- 明确当前问题和目标
- 分阶段实施
- 每阶段都测试
- 清晰的交付物

---

## Prompt 质量检查清单

### ✅ 好的 Prompt 包含：

**问题/任务描述**
- [ ] 清晰描述要做什么
- [ ] 具体的问题或需求
- [ ] 避免模糊的表述

**Context**
- [ ] 相关的背景信息
- [ ] 技术栈
- [ ] 依赖关系
- [ ] 使用场景

**要求/约束**
- [ ] 明确的要求列表
- [ ] 技术约束
- [ ] 业务约束
- [ ] 性能要求

**步骤**
- [ ] 清晰的实施步骤
- [ ] 按阶段划分（复杂任务）
- [ ] 测试要求

**输出格式**
- [ ] 期望的输出格式
- [ ] 交付物列表
- [ ] 如何报告问题

**示例**
- [ ] 提供实际例子（如果复杂）
- [ ] 对比好/坏情况

---

## 常见 Mistake 修复

### Mistake 1: 太模糊

❌ Bad: "Make it better"
✅ Good: "Improve error handling with specific error codes and messages"

### Mistake 2: 没有 context

❌ Bad: "Add caching"
✅ Good: "Add Redis caching to the user profile endpoint. Current response time is 2s, target is <200ms"

### Mistake 3: 太长太复杂

❌ Bad: 50 行 prompt，什么都想要
✅ Good: 分成多个 prompt，每个专注一个任务

### Mistake 4: 没有测试要求

❌ Bad: "Fix the bug"
✅ Good: "Fix the bug and add a test that covers this scenario"

### Mistake 5: 忘记验证

❌ Bad: 完成 prompt 后直接用
✅ Good: 审查代码，运行测试，验证结果

---

## Prompt 模板

### 简单任务模板

```
[TASK]: [concise description]

Context:
- [relevant context]
- [what you know]

Requirements:
- [what needs to be done]
- [constraints]

Steps:
1. [step 1]
2. [step 2]
3. [step 3]
```

### 复杂任务模板

```
[TASK]: [comprehensive description]

Current state:
- [describe current situation]
- [problems identified]

Target state:
- [describe desired outcome]
- [success criteria]

Requirements:
1. [requirement 1]
2. [requirement 2]
...

Constraints:
- [constraint 1]
- [constraint 2]
...

Implementation phases:
Phase 1: [goal]
  - [sub-steps]
Phase 2: [goal]
  - [sub-steps]
...

Deliverables:
- [what to deliver]
- [documentation needed]

When complete, provide:
1. [report item 1]
2. [report item 2]
...
```

---

## 实战练习

### 练习 1: 把 bad prompt 改成 good

**Bad:** "Make the code faster"

**你的答案：**
```
（思考...）

Good Prompt:
```

### 练习 2: 添加缺少的 context

**Good-ish:**
```
Fix the bug in the user registration.

Users cannot register with email addresses containing + sign.
```

**缺少什么？如何改进？**
```
（思考...）

改进后:
```

---

## 总结

**好 Prompt 的三个核心：**

1. **具体** - 清晰、准确、可执行
2. **完整** - 包含 context、要求、约束
3. **结构化** - 有步骤、有格式、有目标

**记住：**
```
 garbage in, garbage out

模糊的 prompt → 模糊的结果
清晰的 prompt → 清晰的结果
```

---

**准备好写出更好的 prompt 了吗？** ✨

下次给 Claude Code 任务时，先问自己：
- 够具体吗？
- 有 context 吗？
- 清楚要什么结果吗？
