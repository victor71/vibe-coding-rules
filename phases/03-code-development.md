# Phase 3: 代码开发

## 🎯 目标

基于方案设计，高质量、高效率地实现代码，保持持续集成和快速反馈。

**核心价值：**
- 保持代码质量
- 快速发现和修复问题
- 支持增量迭代
- 便于代码审查

---

## 🏗️ 开发模式选择

### 常见开发模式

| 模式 | 适用场景 | 特点 | Prompt重点 |
|------|---------|------|-----------|
| **TDD（测试驱动）** | 复杂逻辑、关键功能 | 先写测试，再写代码 | 测试用例设计 |
| **增量开发** | 大型功能、不确定需求 | 小步迭代，持续验证 | 阶段性任务拆解 |
| **特性分支** | 团队协作、并行开发 | Git工作流 | 分支管理策略 |
| **原型先行** | 新技术、不确定方案 | 先POC再生产 | 技术验证 |

---

## 📋 开发前准备

### 检查清单

**环境准备：**
- [ ] 开发环境搭建完成
- [ ] 依赖库已安装
- [ ] 配置文件已准备
- [ ] Git配置正确

**代码质量工具：**
- [ ] Linter配置（ESLint/Pylint等）
- [ ] Formatter配置（Prettier/Black等）
- [ ] 类型检查（TypeScript/MyPy等）
- [ ] Pre-commit hooks配置

**测试环境：**
- [ ] 单元测试框架就绪
- [ ] 测试数据准备
- [ ] Mock/Stub方案确定

**文档准备：**
- [ ] API文档已更新
- [ ] 数据库schema已确认
- [ ] 设计文档已评审

---

## 🔄 增量开发工作流

### 流程图

```
1. 需求拆解 → 2. 创建分支 → 3. 编码 → 4. 提交 → 5. 测试 → 6. 合并
```

### Step-by-Step

#### Step 1: 需求拆解

将大需求拆分为可独立验收的小任务。

**Prompt模板：**
```markdown
请将以下需求拆解为可独立开发的小任务：

## 需求
[需求描述]

## 拆解原则
1. 每个任务可在2-4小时内完成
2. 每个任务有明确的验收标准
3. 任务之间的依赖关系清晰
4. 识别可以并行的任务

## 输出格式
### 任务1: [任务名称]
- **描述：** [具体描述]
- **验收标准：** [如何验证完成]
- **估计时间：** X小时
- **依赖：** [前置任务]
- **可并行：** Yes/No
```

**示例输出：**
```markdown
### 任务1: 用户模型定义
- **描述：** 定义User schema，包含字段：id, name, email, created_at
- **验收标准：** 运行数据库迁移成功，模型可用
- **估计时间：** 2小时
- **依赖：** 无
- **可并行：** Yes

### 任务2: 注册API实现
- **描述：** 实现POST /api/users/register接口
- **验收标准：** 可用Postman成功注册，返回用户信息
- **估计时间：** 3小时
- **依赖：** 任务1
- **可并行：** No

### 任务3: 登录API实现
- **描述：** 实现POST /api/users/login接口
- **验收标准：** 可用Postman成功登录，返回token
- **估计时间：** 3小时
- **依赖：** 任务1
- **可并行：** Yes（与任务2并行）
```

---

#### Step 2: 创建分支

**分支命名规范：**
```
feature/[功能名称]
fix/[bug名称]
refactor/[重构描述]
```

**Prompt：**
```markdown
为以下任务创建分支名：

任务：[任务描述]

请提供：
1. 建议的分支名
2. 命名理由
```

---

#### Step 3: 编码

**AI辅助编码Prompt模板：**

##### 通用编码任务

```markdown
请实现以下功能：

## 任务描述
[详细描述]

## 上下文
- 项目结构：[简要说明]
- 相关文件：[列出相关文件]
- 现有代码风格：[简述代码风格]

## 要求
1. 遵循现有代码风格
2. 添加必要的错误处理
3. 添加日志记录（关键操作）
4. 考虑边界情况

## 约束
- [约束条件1]
- [约束条件2]

## 输出
请直接提供代码，包括：
- 新建/修改的文件列表
- 每个文件的完整代码
- 必要的注释（不要过度注释）
```

---

##### 特定场景：API接口实现

```markdown
请实现以下API接口：

## API定义
- **方法：** POST
- **路径：** /api/users/create
- **认证：** 需要token
- **请求体：**
  ```json
  {
    "name": "string (required, max 50 chars)",
    "email": "string (required, valid email)",
    "role": "enum [admin, user] (optional, default: user)"
  }
  ```
- **响应（成功）：** 201 Created
  ```json
  {
    "id": "uuid",
    "name": "...",
    "email": "...",
    "role": "...",
    "created_at": "iso8601"
  }
  ```
- **响应（错误）：** 400/401/409/500
  ```json
  {
    "error": "error_code",
    "message": "human readable message",
    "details": {...}
  }
  ```

## 要求
1. 参数验证（name长度、email格式）
2. 权限检查（只有admin可创建admin用户）
3. 邮箱唯一性检查
4. 错误处理和日志
5. 单元测试（至少3个测试用例）

## 现有代码
[粘贴相关现有代码]
```

---

##### 特定场景：数据库迁移

```markdown
请创建数据库迁移脚本：

## 迁移目标
[描述需要做的数据库变更]

## 要求
1. 支持向上迁移和向下迁移（up/down）
2. 数据安全（备份、验证）
3. 兼容性（支持从旧版本迁移）
4. 回滚方案

## 现有schema
[粘贴现有数据库schema]

## 期望schema
[粘贴期望的schema]

## 输出
提供完整的迁移脚本SQL，包括：
- 表创建/修改语句
- 索引创建/删除
- 数据迁移（如有）
- 验证查询
```

---

##### 特定场景：复杂算法

```markdown
请实现以下算法：

## 算法描述
[详细描述算法逻辑]

## 输入
```
[输入格式]
```

## 输出
```
[输出格式]
```

## 要求
1. 时间复杂度：O(n^2) 或更优
2. 空间复杂度：O(n) 或更优
3. 边界情况处理
4. 代码可读性
5. 单元测试（覆盖边界情况）

## 示例
输入：[示例输入]
输出：[示例输出]

## 输出
提供：
1. 算法实现代码
2. 复杂度分析
3. 测试用例（至少5个）
```

---

#### Step 4: 提交代码

**提交信息规范（Conventional Commits）：**

```
<type>(<scope>): <subject>

<body>

<footer>
```

**Type类型：**
- `feat`: 新功能
- `fix`: Bug修复
- `refactor`: 重构
- `docs`: 文档更新
- `test`: 测试相关
- `chore`: 构建/工具相关

**示例：**
```
feat(auth): add JWT token refresh mechanism

- Implement token refresh endpoint at /api/auth/refresh
- Add refresh token rotation for security
- Update auth middleware to handle expired tokens
- Add unit tests for refresh flow

Closes #123
```

**Prompt：**
```markdown
请为以下代码变更生成提交信息：

## 变更描述
[描述做了什么改动]

## 要求
- 使用Conventional Commits格式
- 简洁的subject（不超过50字符）
- 清晰的body（说明改动原因和影响）
- 关联issue编号（如有）
```

---

#### Step 5: 代码审查

**AI辅助代码审查Prompt：**

```markdown
请审查以下代码：

## 代码
[粘贴代码]

## 审查重点
1. **功能正确性** - 是否实现了预期功能？
2. **代码质量** - 是否清晰、可读、可维护？
3. **错误处理** - 是否有足够的错误处理？
4. **安全性** - 是否有安全漏洞（SQL注入、XSS等）？
5. **性能** - 是否有性能问题（N+1查询、不必要的循环）？
6. **边界情况** - 是否处理了边界情况？
7. **测试覆盖** - 是否有足够的测试？

## 输出格式
### 严重问题（必须修复）
1. [问题描述]
   - 文件:行号
   - 严重程度: Critical/High
   - 建议: [具体建议]

### 改进建议（建议修复）
...

### 做得好的地方
...
```

---

#### Step 6: 测试

**测试类型：**

| 类型 | 目的 | Prompt重点 |
|------|------|-----------|
| **单元测试** | 测试单个函数/类 | 边界情况、异常情况 |
| **集成测试** | 测试模块交互 | API端到端测试 |
| **E2E测试** | 测试完整流程 | 用户场景测试 |

**Prompt模板：**

##### 单元测试

```markdown
请为以下代码编写单元测试：

## 代码
[粘贴代码]

## 测试框架
[使用的测试框架，如Jest/Pytest]

## 要求
1. 测试正常情况
2. 测试边界情况（空输入、最大值等）
3. 测试异常情况（错误输入、异常抛出）
4. 至少5个测试用例
5. 使用清晰的测试名称

## 输出
提供完整的测试代码，包括：
- 测试文件
- 每个测试用例的描述
- 必要的mock/stub
```

---

##### 集成测试

```markdown
请为以下API编写集成测试：

## API定义
[API定义，同前]

## 要求
1. 测试正常情况
2. 测试参数验证
3. 测试权限检查
4. 测试错误处理
5. 使用测试数据库

## 输出
提供完整的集成测试代码。
```

---

## 🚀 持续集成

### CI/CD Pipeline

```
代码提交 → 自动构建 → 运行测试 → 代码检查 → 部署（可选）
```

### CI配置示例

**GitHub Actions示例：**

```yaml
name: CI

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Setup Node.js
        uses: actions/setup-node@v2
        with:
          node-version: '18'
      - name: Install dependencies
        run: npm ci
      - name: Run linter
        run: npm run lint
      - name: Run tests
        run: npm test
      - name: Run type check
        run: npm run type-check
```

---

## 📊 代码质量门禁

### 质量指标

| 指标 | 目标值 | 检查工具 |
|------|--------|---------|
| **测试覆盖率** | >80% | Jest/Pytest coverage |
| **代码复杂度** | <10 | ESLint complexity rule |
| **代码重复率** | <5% | SonarQube |
| **类型安全** | 0 errors | TypeScript/MyPy |
| **Linter警告** | 0 errors | ESLint/Pylint |

---

### Prompt: 代码质量检查

```markdown
请检查以下代码的质量：

## 代码
[粘贴代码]

## 检查维度
1. **复杂度** - 是否过于复杂？如何简化？
2. **重复** - 是否有重复代码？如何提取？
3. **命名** - 命名是否清晰？
4. **长度** - 函数/类是否过长？
5. **注释** - 是否缺少必要注释？是否有无用注释？

## 输出格式
### 质量评分
总体评分：X/10

### 改进建议
1. [建议]
   - 原因：[为什么需要改]
   - 改动：[具体如何改]
```

---

## 🤖 AI辅助开发最佳实践

### 1. 提供充分上下文

**❌ Bad:**
```markdown
修复这个bug
```

**✅ Good:**
```markdown
修复登录超时的bug：

## Bug描述
用户登录时，如果数据库连接超时，会收到500错误而不是友好提示。

## 相关文件
- auth_service.py (行 45-60)
- login_handler.py (行 20-35)

## 当前代码
[粘贴相关代码]

## 要求
- 添加超时处理
- 返回友好错误消息
- 添加日志记录
- 添加测试
```

---

### 2. 分阶段使用AI

**阶段1: 设计**
```markdown
帮我设计这个功能的架构
```

**阶段2: 编码**
```markdown
基于上面的设计，实现代码
```

**阶段3: 测试**
```markdown
为上面的代码编写测试
```

**阶段4: 审查**
```markdown
审查代码和测试的质量
```

---

### 3. 迭代优化

**第一轮：** 基础实现
**第二轮：** 添加错误处理
**第三轮：** 性能优化
**第四轮：** 文档完善

---

## ✅ 代码质量最佳实践

### 1. 编写可读代码

**原则：**
- 代码是写给人看的
- 好的代码不需要注释
- 注释解释"为什么"，不是"什么"

**示例：**

```python
# ❌ Bad
def f(x):
    return x*2 + x/3

# ✅ Good
def calculate_total_weight(base_weight: float, additional_weight: float) -> float:
    """Calculate total weight including additional items."""
    return base_weight * 2 + additional_weight / 3
```

---

### 2. 遵循SOLID原则

**S**ingle Responsibility - 单一职责
**O**pen/Closed - 开闭原则
**L**iskov Substitution - 里氏替换
**I**nterface Segregation - 接口隔离
**D**ependency Inversion - 依赖倒置

---

### 3. 错误处理

**Prompt:**
```markdown
请为以下代码添加错误处理：

## 代码
[粘贴代码]

## 要求
1. 添加适当的异常捕获
2. 提供有意义的错误消息
3. 记录关键错误
4. 确保资源正确释放
```

---

### 4. 日志记录

**日志级别：**
- **ERROR** - 需要立即处理的错误
- **WARN** - 潜在问题
- **INFO** - 关键操作
- **DEBUG** - 调试信息

**Prompt:**
```markdown
请为以下代码添加日志记录：

## 代码
[粘贴代码]

## 要求
1. 在关键操作处添加INFO日志
2. 在错误处添加ERROR日志（带上下文）
3. 在边界情况添加WARN日志
4. 日志格式统一
```

---

## 🚨 常见陷阱

### 陷阱1: 过度信任AI

**症状：**
- 直接提交AI生成的代码
- 不做代码审查
- 不运行测试

**预防：**
- 始终审查代码
- 运行测试验证
- 理解每一行代码

---

### 陷阱2: 一次性写太多

**症状：**
- 一个commit包含太多改动
- 难以审查
- 难以回滚

**预防：**
- 小步提交
- 每个commit独立可验证
- 清晰的commit message

---

### 陷阱3: 忽略测试

**症状：**
- 写代码后忘记测试
- 测试覆盖率低
- 边界情况未覆盖

**预防：**
- TDD（测试驱动）
- 每个功能都有测试
- 定期检查覆盖率

---

### 陷阱4: 硬编码

**症状：**
- 配置值硬编码
- 魔法数字
- 环境相关代码

**预防：**
- 使用配置文件
- 提取常量
- 环境变量

---

## 📚 参考资料

### 工具
- **GitHub Copilot** - AI辅助编码
- **SonarQube** - 代码质量分析
- **CodeClimate** - 代码质量平台

### 最佳实践
- **Clean Code** - Robert C. Martin
- **Refactoring** - Martin Fowler
- **The Pragmatic Programmer** - Andrew Hunt

---

**下一阶段：** [04-testing-validation.md](04-testing-validation.md)

**回到总览：** [README.md](../README.md)
