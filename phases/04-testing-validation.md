# Phase 4: 测试验证

## 🎯 目标

确保代码质量，验证功能正确性，发现并修复问题，保障生产稳定性。

**核心价值：**
- 及早发现bug（越早越便宜）
- 确保功能符合预期
- 防止回归（修改后破坏原有功能）
- 建立发布信心

---

## 🧪 测试金字塔

```
        /\
       /E2E\      ← 少量端到端测试
      /------\
     /  集成  \    ← 中等集成测试
    /----------\
   /   单元测试  \  ← 大量单元测试
  /--------------\
```

### 测试层次对比

| 层次 | 数量 | 速度 | 成本 | 维护成本 | 重点 |
|------|------|------|------|---------|------|
| **单元测试** | 多 | 快 | 低 | 低 | 单个函数/类 |
| **集成测试** | 中 | 中 | 中 | 中 | 模块交互 |
| **E2E测试** | 少 | 慢 | 高 | 高 | 完整用户流程 |

---

## 📋 测试策略

### 测试策略文档模板

```markdown
# 测试策略：[功能名称]

## 测试目标
[验证什么？]

## 测试范围
- 包含：[什么要测]
- 不包含：[什么不测]

## 测试类型
### 单元测试
- [ ] 功能X
- [ ] 功能Y

### 集成测试
- [ ] API测试
- [ ] 数据库交互测试

### E2E测试
- [ ] 用户注册流程
- [ ] 支付流程

## 测试环境
- 开发环境：[用于快速验证]
- 测试环境：[用于完整测试]
- 预发环境：[用于上线前验证]

## 测试数据
- 测试数据来源
- 数据清理策略
- 敏感数据处理

## 回归测试
- 需要回归的功能列表
- 自动化回归测试
- 手动回归测试范围

## 性能测试
- 响应时间要求
- 并发用户数
- 性能基准

## 安全测试
- 权限检查
- SQL注入测试
- XSS测试
```

---

## 🔬 单元测试

### 适用场景

- **纯函数** - 输入输出确定
- **业务逻辑** - 复杂算法、规则
- **工具函数** - 日期处理、字符串处理

### Prompt模板

#### 基础单元测试

```markdown
请为以下代码编写单元测试：

## 代码
[粘贴代码]

## 测试框架
[使用的测试框架，如Jest/Pytest/PHPUnit]

## 要求
1. 测试正常情况（Happy Path）
2. 测试边界情况（空输入、最大值、最小值）
3. 测试异常情况（错误输入、null/undefined）
4. 使用清晰的测试名称（描述性）
5. 使用given-when-then模式（可选）
6. 至少5个测试用例

## 输出格式
提供完整的测试文件，包括：
- 所有测试用例
- 必要的setup/teardown
- Mock/Stub（如需要）
- 清晰的注释说明测试意图
```

**示例输出：**

```python
# test_user_utils.py
import pytest
from user_utils import validate_email, calculate_age

class TestValidateEmail:
    """Test email validation function."""
    
    def test_valid_email_returns_true(self):
        """Valid email should return True."""
        # Given
        email = "user@example.com"
        
        # When
        result = validate_email(email)
        
        # Then
        assert result is True
    
    def test_email_without_at_sign_returns_false(self):
        """Email without @ should return False."""
        # Given
        email = "userexample.com"
        
        # When
        result = validate_email(email)
        
        # Then
        assert result is False
    
    def test_empty_email_returns_false(self):
        """Empty email should return False."""
        assert validate_email("") is False
    
    def test_none_email_returns_false(self):
        """None email should return False."""
        assert validate_email(None) is False
    
    def test_email_with_special_chars_valid(self):
        """Email with dots and plus should be valid."""
        assert validate_email("user.name+tag@example.com") is True


class TestCalculateAge:
    """Test age calculation function."""
    
    def test_age_calculation_for_adult(self):
        """Calculate age for adult."""
        from datetime import date, timedelta
        
        birth_date = date.today() - timedelta(days=365 * 30)
        age = calculate_age(birth_date)
        assert age == 30
    
    def test_age_calculation_for_child(self):
        """Calculate age for child."""
        from datetime import date, timedelta
        
        birth_date = date.today() - timedelta(days=365 * 5)
        age = calculate_age(birth_date)
        assert age == 5
    
    def test_age_calculation_for_today(self):
        """Age should be 0 for today's birth."""
        from datetime import date
        
        age = calculate_age(date.today())
        assert age == 0
```

---

#### TDD（测试驱动开发）

```markdown
使用TDD方式实现以下功能：

## 需求描述
[描述需要实现的功能]

## TDD流程
1. **Red**: 先写一个失败的测试
2. **Green**: 实现最简单的代码让测试通过
3. **Refactor**: 重构代码（保持测试通过）

## 要求
1. 按照TDD循环逐步实现
2. 每个循环只写一个测试
3. 展示每一步的测试代码和实现代码
4. 最后提供完整代码

## 输出格式
### 循环1: 测试用例X
**测试代码：**
```python
```

**实现代码：**
```python
```

**状态：** ✅ Pass / ❌ Fail

### 循环2: ...
```

---

### 单元测试最佳实践

#### 1. 测试命名要清晰

**❌ Bad:**
```python
def test_1():
    assert add(2, 3) == 5
```

**✅ Good:**
```python
def test_add_two_positive_numbers_returns_sum(self):
    """Adding two positive numbers should return their sum."""
    assert add(2, 3) == 5
```

---

#### 2. 使用AAA模式（Arrange-Act-Assert）

```python
def test_calculate_discount_for_vip_user(self):
    # Arrange (准备)
    user = User(type="VIP")
    product = Product(price=100)
    
    # Act (执行)
    discounted_price = calculate_discount(user, product)
    
    # Assert (断言)
    assert discounted_price == 80  # VIP 20% off
```

---

#### 3. 测试一个行为

**❌ Bad:** 一个测试测多个行为
```python
def test_user_operations(self):
    user = User("john")
    assert user.name == "john"
    user.set_age(30)
    assert user.age == 30
    user.birthday()
    assert user.age == 31
```

**✅ Good:** 每个行为一个测试
```python
def test_user_name_initialization(self):
    user = User("john")
    assert user.name == "john"

def test_user_age_setting(self):
    user = User("john")
    user.set_age(30)
    assert user.age == 30

def test_user_age_increments_on_birthday(self):
    user = User("john")
    user.set_age(30)
    user.birthday()
    assert user.age == 31
```

---

#### 4. 避免测试实现细节

**❌ Bad:**
```python
def test_user_name_storage(self):
    user = User("john")
    assert user._name == "john"  # 测试私有属性
```

**✅ Good:**
```python
def test_user_name_retrieval(self):
    user = User("john")
    assert user.get_name() == "john"  # 测试公共接口
```

---

## 🔗 集成测试

### 适用场景

- **API端点** - HTTP接口测试
- **数据库交互** - CRUD操作测试
- **外部服务** - 第三方API调用
- **消息队列** - 异步处理测试

### Prompt模板

#### API集成测试

```markdown
请为以下API编写集成测试：

## API定义
- **方法：** POST
- **路径：** /api/users
- **认证：** Bearer Token

## 请求体
```json
{
  "name": "string (required, max 50)",
  "email": "string (required, valid email)",
  "role": "enum [admin, user] (optional)"
}
```

## 响应
- **201 Created:** 返回创建的用户
- **400 Bad Request:** 参数错误
- **409 Conflict:** 邮箱已存在
- **401 Unauthorized:** 未认证
- **403 Forbidden:** 权限不足

## 测试要求
1. 测试正常创建
2. 测试参数验证（name长度、email格式）
3. 测试邮箱重复
4. 测试权限（只有admin可创建admin用户）
5. 测试认证（无token返回401）
6. 测试数据库验证（记录是否真实写入）

## 测试框架
[使用的框架，如pytest+httpx/Jest+supertest]

## Mock要求
- 认证中间件可以mock
- 数据库使用test database

## 输出
提供完整的集成测试代码。
```

**示例输出：**

```python
# test_users_api.py
import pytest
from httpx import AsyncClient
from sqlalchemy.ext.asyncio import AsyncSession

@pytest.mark.asyncio
async def test_create_user_success(async_client: AsyncClient, db_session: AsyncSession):
    """Creating a valid user should return 201."""
    # Arrange
    payload = {
        "name": "John Doe",
        "email": "john@example.com",
        "role": "user"
    }
    headers = {"Authorization": "Bearer valid_admin_token"}
    
    # Act
    response = await async_client.post("/api/users", json=payload, headers=headers)
    
    # Assert
    assert response.status_code == 201
    data = response.json()
    assert data["id"] is not None
    assert data["name"] == "John Doe"
    assert data["email"] == "john@example.com"
    assert data["role"] == "user"
    assert "password" not in data  # Don't return password


@pytest.mark.asyncio
async def test_create_user_invalid_email(async_client: AsyncClient):
    """Creating user with invalid email should return 400."""
    payload = {
        "name": "John Doe",
        "email": "invalid-email",
        "role": "user"
    }
    headers = {"Authorization": "Bearer valid_admin_token"}
    
    response = await async_client.post("/api/users", json=payload, headers=headers)
    
    assert response.status_code == 400
    assert "email" in response.json()["errors"]


@pytest.mark.asyncio
async def test_create_user_duplicate_email(async_client: AsyncClient):
    """Creating user with duplicate email should return 409."""
    # Create first user
    payload = {
        "name": "John Doe",
        "email": "john@example.com",
        "role": "user"
    }
    headers = {"Authorization": "Bearer valid_admin_token"}
    await async_client.post("/api/users", json=payload, headers=headers)
    
    # Try to create duplicate
    response = await async_client.post("/api/users", json=payload, headers=headers)
    
    assert response.status_code == 409
    assert "email already exists" in response.json()["message"]


@pytest.mark.asyncio
async def test_create_user_without_auth(async_client: AsyncClient):
    """Creating user without authentication should return 401."""
    payload = {
        "name": "John Doe",
        "email": "john@example.com"
    }
    
    response = await async_client.post("/api/users", json=payload)
    
    assert response.status_code == 401


@pytest.mark.asyncio
async def test_create_non_admin_user_as_regular_user(async_client: AsyncClient):
    """Regular user should not be able to create users."""
    payload = {
        "name": "John Doe",
        "email": "john@example.com"
    }
    headers = {"Authorization": "Bearer valid_user_token"}  # Not admin
    
    response = await async_client.post("/api/users", json=payload, headers=headers)
    
    assert response.status_code == 403
```

---

### 集成测试最佳实践

#### 1. 使用测试数据库

```python
@pytest.fixture
async def test_db():
    """Create a test database for each test."""
    async with create_test_database() as db:
        yield db
        # Clean up after test


@pytest.mark.asyncio
async def test_user_creation(test_db):
    user = await User.create(name="John", email="john@example.com", db=test_db)
    assert user.id is not None
```

---

#### 2. 隔离测试

```python
@pytest.mark.asyncio
async def test_feature_a(test_db):
    # Test A
    pass

@pytest.mark.asyncio
async def test_feature_b(test_db):
    # Test B - should not depend on Test A
    pass
```

---

#### 3. Mock外部依赖

```python
from unittest.mock import patch, AsyncMock

@pytest.mark.asyncio
async def test_payment_success():
    with patch('services.payment_gateway.charge', new_callable=AsyncMock) as mock_charge:
        mock_charge.return_value = {"status": "success"}
        
        result = await process_payment(user_id=1, amount=100)
        
        assert result["status"] == "success"
        mock_charge.assert_called_once_with(amount=100)
```

---

## 🌐 E2E测试

### 适用场景

- **关键用户流程** - 注册、登录、购买
- **跨模块功能** - 涉及多个服务的功能
- **上线前验证** - 完整功能链路

### Prompt模板

```markdown
请为以下用户流程编写E2E测试：

## 用户流程
[描述完整的用户旅程]

## 流程步骤
1. 用户访问网站
2. 用户点击注册
3. 用户填写表单
4. 用户提交
5. 用户收到确认邮件
6. 用户登录
7. 用户查看欢迎消息

## 测试要求
1. 使用真实的浏览器自动化（如Playwright/Cypress）
2. 测试完整流程（不跳过步骤）
3. 验证关键元素和文案
4. 处理异步操作（如邮件发送）
5. 清理测试数据

## 测试框架
[使用的E2E框架，如Playwright/Cypress/Puppeteer]

## 输出
提供完整的E2E测试代码。
```

**示例输出：**

```javascript
// e2e/user-registration.spec.js
const { test, expect } = require('@playwright/test');

test.describe('User Registration Flow', () => {
  test('complete user registration from signup to login', async ({ page }) => {
    // Step 1: Navigate to website
    await page.goto('https://example.com');
    await expect(page).toHaveTitle(/Welcome/);
    
    // Step 2: Click register button
    await page.click('button:has-text("Register")');
    await expect(page).toHaveURL(/.*\/register/);
    
    // Step 3: Fill registration form
    await page.fill('input[name="name"]', 'John Doe');
    await page.fill('input[name="email"]', `john${Date.now()}@example.com`);
    await page.fill('input[name="password"]', 'SecurePassword123!');
    await page.fill('input[name="confirmPassword"]', 'SecurePassword123!');
    
    // Step 4: Submit form
    await page.click('button[type="submit"]');
    
    // Step 5: Verify success message
    await expect(page.locator('.success-message')).toContainText('Registration successful');
    await expect(page.locator('.success-message')).toBeVisible();
    
    // Step 6: Verify email sent (mock or real check)
    // In real scenario, you might check email service or test inbox
    
    // Step 7: Navigate to login
    await page.click('button:has-text("Login")');
    await expect(page).toHaveURL(/.*\/login/);
    
    // Step 8: Login with new credentials
    const email = `john${Date.now()}@example.com`;
    await page.fill('input[name="email"]', email);
    await page.fill('input[name="password"]', 'SecurePassword123!');
    await page.click('button[type="submit"]');
    
    // Step 9: Verify logged in
    await expect(page).toHaveURL(/.*\/dashboard/);
    await expect(page.locator('.user-name')).toContainText('John Doe');
  });
  
  test('registration with invalid email shows error', async ({ page }) => {
    await page.goto('https://example.com/register');
    
    await page.fill('input[name="name"]', 'John Doe');
    await page.fill('input[name="email"]', 'invalid-email');
    await page.fill('input[name="password"]', 'SecurePassword123!');
    await page.fill('input[name="confirmPassword"]', 'SecurePassword123!');
    
    await page.click('button[type="submit"]');
    
    // Verify error message
    await expect(page.locator('.error-message')).toContainText('Invalid email format');
    await expect(page).toHaveURL(/.*\/register/); // Stay on same page
  });
  
  test('password mismatch shows error', async ({ page }) => {
    await page.goto('https://example.com/register');
    
    await page.fill('input[name="name"]', 'John Doe');
    await page.fill('input[name="email"]', 'john@example.com');
    await page.fill('input[name="password"]', 'Password123!');
    await page.fill('input[name="confirmPassword"]', 'DifferentPassword123!');
    
    await page.click('button[type="submit"]');
    
    await expect(page.locator('.error-message')).toContainText('Passwords do not match');
  });
});
```

---

## 🎯 性能测试

### 测试类型

| 类型 | 目标 | 工具 |
|------|------|------|
| **负载测试** | 正常负载下的性能 | JMeter, k6 |
| **压力测试** | 极限负载下的表现 | JMeter, k6 |
| ** Spike测试** | 突发流量 | JMeter |
| **持久测试** | 长时间运行的稳定性 | Locust |

### Prompt模板

```markdown
请为以下API编写性能测试脚本：

## API定义
- **方法：** GET
- **路径：** /api/products

## 性能目标
- **响应时间（p95）:** < 200ms
- **响应时间（p99）:** < 500ms
- **吞吐量:** > 1000 req/s
- **错误率:** < 0.1%

## 测试场景
1. **基准测试** - 10 并发用户
2. **正常负载** - 100 并发用户
3. **压力测试** - 1000 并发用户
4. **持续测试** - 持续10分钟

## 测试工具
[k6/JMeter/Locust]

## 要求
1. 提供完整的测试脚本
2. 包含结果分析逻辑
3. 生成性能报告

## 输出
提供测试脚本和使用说明。
```

**示例输出（k6）：**

```javascript
// performance-test.js
import http from 'k6/http';
import { check, sleep } from 'k6';
import { Rate, Trend } from 'k6/metrics';

// Custom metrics
const errorRate = new Rate('errors');
const responseTime = new Trend('response_time');

export const options = {
  stages: [
    { duration: '1m', target: 10 },   // Ramp up to 10 users
    { duration: '2m', target: 10 },   // Stay at 10 users
    { duration: '1m', target: 100 },  // Ramp up to 100 users
    { duration: '2m', target: 100 },  // Stay at 100 users
    { duration: '1m', target: 1000 }, // Ramp up to 1000 users
    { duration: '2m', target: 1000 }, // Stay at 1000 users
    { duration: '1m', target: 0 },    // Ramp down
  ],
  thresholds: {
    http_req_duration: ['p(95)<200', 'p(99)<500'], // Response time
    errors: ['rate<0.01'],                           // Error rate < 1%
  },
};

export default function () {
  // Make API request
  const response = http.get('https://api.example.com/products', {
    headers: {
      'Authorization': 'Bearer valid_token',
    },
  });

  // Check response
  const isSuccess = check(response, {
    'status is 200': (r) => r.status === 200,
    'response time < 500ms': (r) => r.timings.duration < 500,
    'has products array': (r) => JSON.parse(r.body).products !== undefined,
  });

  // Record metrics
  errorRate.add(!isSuccess);
  responseTime.add(response.timings.duration);

  // Sleep between requests (simulate real user)
  sleep(1);
}

// After all tests
export function handleSummary(data) {
  return {
    'performance-report.json': JSON.stringify(data),
    'stdout': JSON.stringify({
      'avg_response_time': data.metrics.http_req_duration.values.avg,
      'p95_response_time': data.metrics.http_req_duration.values['p(95)'],
      'p99_response_time': data.metrics.http_req_duration.values['p(99)'],
      'error_rate': data.metrics.errors.values.rate,
    }, null, 2),
  };
}
```

---

## 🔒 安全测试

### 常见安全漏洞

| 漏洞 | 说明 | 测试方法 |
|------|------|---------|
| **SQL注入** | 恶意SQL代码执行 | 输入SQL特殊字符 |
| **XSS** | 跨站脚本攻击 | 输入HTML/JS代码 |
| **CSRF** | 跨站请求伪造 | 无token提交请求 |
| **越权** | 未授权访问 | 尝试访问他人资源 |
| **敏感信息泄露** | 错误中暴露信息 | 检查错误响应 |

### Prompt模板

```markdown
请为以下API编写安全测试：

## API定义
[API定义]

## 安全测试要求
1. **SQL注入测试** - 输入SQL特殊字符
2. **XSS测试** - 输入HTML/JS代码
3. **越权测试** - 尝试访问他人资源
4. **认证测试** - 无token、过期token
5. **敏感信息测试** - 检查错误响应

## 输出
提供安全测试用例列表和测试代码。
```

---

## 📊 测试覆盖率

### 覆盖率类型

| 类型 | 说明 | 目标 |
|------|------|------|
| **行覆盖率** | 执行的代码行 | >80% |
| **分支覆盖率** | 执行的分支 | >70% |
| **函数覆盖率** | 调用的函数 | >90% |

### Prompt: 覆盖率分析

```markdown
请分析以下测试覆盖率报告：

## 覆盖率数据
[粘贴覆盖率报告]

## 要求
1. 识别未测试的关键代码
2. 识别需要添加测试的区域
3. 提供测试建议

## 输出格式
### 覆盖率汇总
- 总体覆盖率: X%
- 行覆盖率: X%
- 分支覆盖率: X%

### 未覆盖的关键代码
1. [文件名:行号]
   - 功能: [描述]
   - 风险: [为什么需要测试]
   - 建议: [如何测试]

### 测试建议
1. [建议1]
2. [建议2]
```

---

## 🔄 回归测试

### 何时需要回归测试

- [ ] 修复了bug
- [ ] 重构了代码
- [ ] 升级了依赖
- [ ] 修改了公共API
- [ ] 性能优化后

### Prompt模板

```markdown
以下修改可能影响哪些现有功能？需要回归测试什么？

## 修改描述
[描述做了什么改动]

## 修改文件
[列出修改的文件]

## 要求
1. 识别可能受影响的功能
2. 识别需要回归测试的测试用例
3. 识别可能引入的bug

## 输出格式
### 受影响功能
1. [功能名]
   - 原因: [为什么受影响]
   - 风险: [高/中/低]

### 需要回归测试
- [ ] 测试用例1
- [ ] 测试用例2
- [ ] 测试用例3

### 潜在风险
1. [风险]
   - 应对: [如何降低风险]
```

---

## ✅ 测试最佳实践

### 1. 测试金字塔原则

```
70% 单元测试
20% 集成测试
10% E2E测试
```

**原因：**
- 单元测试快、稳定、易维护
- E2E测试慢、脆弱、难维护

---

### 2. 独立性

每个测试应该：
- 不依赖其他测试
- 可以独立运行
- 顺序无关

**❌ Bad:**
```python
def test_a():
    global_state = "modified"

def test_b():
    assert global_state == "modified"  # 依赖test_a
```

**✅ Good:**
```python
def test_a():
    state = set_state("modified")
    assert state == "modified"

def test_b():
    state = set_state("modified")
    assert state == "modified"
```

---

### 3. 可读性

测试应该像文档一样，描述系统行为。

**✅ Good:**
```python
def test_vip_user_gets_20_percent_discount():
    """VIP users should receive 20% discount on all products."""
    user = User(type="VIP")
    product = Product(price=100)
    discounted = calculate_discount(user, product)
    assert discounted == 80
```

---

### 4. 快速反馈

单元测试应该：
- 运行快速（< 1秒）
- 可以频繁运行
- 在commit前运行

```bash
# Pre-commit hook
npm run test:unit
```

---

## 🚨 常见陷阱

### 陷阱1: 测试实现细节

**❌ Bad:** 测试私有方法
```python
def test_private_method():
    obj = MyClass()
    result = obj._private_method()
    assert result == expected
```

**✅ Good:** 测试公共接口
```python
def test_public_behavior():
    obj = MyClass()
    result = obj.public_method()
    assert result == expected
```

---

### 陷阱2: 测试覆盖率迷信

**高覆盖率 ≠ 高质量**

- 100%覆盖率也可能有bug
- 关注测试质量而非覆盖率
- 测试关键路径和边界情况

---

### 陷阱3: 脆弱的测试

**症状：**
- 测试经常随机失败（flaky）
- 对环境变化敏感
- 对时间敏感

**预防：**
- Mock外部依赖
- 使用固定的测试数据
- 避免依赖时间/随机性

---

### 陷阱4: 过度Mock

**❌ Bad:** Mock一切
```python
def test_user_creation():
    with patch('User.save'), \
         patch('Email.send'), \
         patch('Logger.info'):
        create_user("john@example.com")
        assert True  # 没有测试任何东西
```

**✅ Good:** 只mock外部依赖
```python
def test_user_creation():
    with patch('Email.send'):  # 只mock邮件服务
        user = create_user("john@example.com")
        assert user.id is not None
        assert user.email == "john@example.com"
```

---

## 📚 参考资料

### 工具
- **单元测试** - Jest, Pytest, JUnit
- **集成测试** - Supertest, httpx
- **E2E测试** - Playwright, Cypress, Puppeteer
- **性能测试** - k6, JMeter, Locust
- **安全测试** - OWASP ZAP, Burp Suite

### 最佳实践
- **Test-Driven Development** - Kent Beck
- **Growing Object-Oriented Software** - Steve Freeman
- **xUnit Test Patterns** - Gerard Meszaros

---

**回到总览：** [README.md](../README.md)
