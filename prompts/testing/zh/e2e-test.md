# E2E Test Prompt Template

端到端（E2E）测试的 prompt 模板。

## 场景

- 关键用户流程测试
- 跨模块功能测试
- 上线前完整功能链路验证
- 真实浏览器自动化测试

## Prompt 模板

```
为以下用户流程编写端到端测试：

## 用户流程
[描述完整的用户旅程]

## 流程步骤
1. [步骤 1]
2. [步骤 2]
3. [步骤 3]
...

## 测试要求
1. 使用真实浏览器自动化（Playwright/Cypress/Puppeteer）
2. 测试完整流程（不跳过步骤）
3. 验证关键元素和文本
4. 处理异步操作（加载、邮件）
5. 清理测试数据

## 测试框架
[使用的 E2E 框架，如 Playwright/Cypress]

## 测试数据
[需要预置的数据]

## 输出
提供完整的端到端测试代码。
```

## 实际例子

### 例子：用户注册流程 E2E 测试

```
为...编写端到端测试用户注册和登录流程。

## 用户流程
1. 用户访问网站
2. 用户点击"注册"
3. 用户填写注册表单
4. 用户提交表单
5. 用户收到确认邮件
6. 用户点击邮件确认链接
7. 用户可以使用凭据登录
8. 用户看到欢迎仪表板

## 测试要求
1. 使用 Playwright 进行浏览器自动化
2. 测试完整流程（不跳过步骤）
3. 验证关键元素和文本
4. 处理邮件验证（模拟或真实）
5. 清理测试数据

## 测试框架
Playwright with JavaScript/TypeScript

## 测试数据
为每次测试运行使用唯一的电子邮件（包含时间戳）

## 输出
提供完整的 E2E 测试代码。
```

**示例输出：**

```javascript
// e2e/user-registration.spec.js
const { test, expect } = require('@playwright/test');

test.describe('用户注册流程', () => {
  test.beforeEach(async ({ page }) => {
    // 设置：导航到基础 URL
    await page.goto('https://example.com');
  });

  test('从注册到仪表板的完整用户注册流程', async ({ page }) => {
    // 步骤 1：导航到网站
    await expect(page).toHaveTitle(/欢迎/);

    // 步骤 2：点击注册按钮
    await page.click('button:has-text("Register")');
    await expect(page).toHaveURL(/.*\/register/);
    await expect(page.locator('h1')).toContainText('创建账户');

    // 步骤 3：填写注册表单
    const timestamp = Date.now();
    const email = `testuser${timestamp}@example.com`;

    await page.fill('input[name="name"]', 'John Doe');
    await page.fill('input[name="email"]', email);
    await page.fill('input[name="password"]', 'SecurePass123!');
    await page.fill('input[name="confirmPassword"]', 'SecurePass123!');

    // 步骤 4：提交表单
    await page.click('button[type="submit"]');

    // 步骤 5：验证成功消息
    await expect(page.locator('.success-message')).toContainText('注册成功');
    await expect(page.locator('.success-message')).toBeVisible();

    // 步骤 6：检查邮件（在真实场景中，验证邮件已发送）
    // 对于 E2E 测试，我们可以模拟这个或使用测试邮件服务
    await expect(page.locator('.email-confirmation')).toContainText('检查您的邮件');

    // 步骤 7：模拟邮件确认（点击确认链接）
    // 在真实场景中，解析邮件并点击链接
    await page.goto(`https://example.com/verify-email?token=${timestamp}`);

    // 步骤 8：导航到登录
    await page.click('button:has-text("Login")');
    await expect(page).toHaveURL(/.*\/login/);

    // 步骤 9：使用新凭据登录
    await page.fill('input[name="email"]', email);
    await page.fill('input[name="password"]', 'SecurePass123!');
    await page.click('button[type="submit"]');

    // 步骤 10：验证已登录
    await expect(page).toHaveURL(/.*\/dashboard/);
    await expect(page.locator('.user-name')).toContainText('John Doe');
    await expect(page.locator('.welcome-banner')).toBeVisible();
  });

  test('无效邮箱显示验证错误', async ({ page }) => {
    await page.click('button:has-text("Register")');

    await page.fill('input[name="name"]', 'John Doe');
    await page.fill('input[name="email"]', 'invalid-email');
    await page.fill('input[name="password"]', 'SecurePass123!');
    await page.fill('input[name="confirmPassword"]', 'SecurePass123!');

    await page.click('button[type="submit"]');

    // 验证错误消息
    await expect(page.locator('.error-message')).toContainText('无效的邮箱格式');
    await expect(page).toHaveURL(/.*\/register/); // 保持在同一页面
  });

  test('密码不匹配显示错误', async ({ page }) => {
    await page.click('button:has-text("Register")');

    await page.fill('input[name="name"]', 'John Doe');
    await page.fill('input[name="email"]', 'john@example.com');
    await page.fill('input[name="password"]', 'Password123!');
    await page.fill('input[name="confirmPassword"]', 'DifferentPassword123!');

    await page.click('button[type="submit"]');

    await expect(page.locator('.error-message')).toContainText('密码不匹配');
  });

  test('短密码显示验证错误', async ({ page }) => {
    await page.click('button:has-text("Register")');

    await page.fill('input[name="name"]', 'John Doe');
    await page.fill('input[name="email"]', 'john@example.com');
    await page.fill('input[name="password"]', 'short'); // 太短
    await page.fill('input[name="confirmPassword"]', 'short');

    await page.click('button[type="submit"]');

    await expect(page.locator('.error-message')).toContainText('密码必须至少 8 个字符');
  });
});

test.describe('登录流程', () => {
  test('成功登录重定向到仪表板', async ({ page }) => {
    await page.goto('https://example.com/login');

    await page.fill('input[name="email"]', 'existing-user@example.com');
    await page.fill('input[name="password"]', 'CorrectPassword123!');
    await page.click('button[type="submit"]');

    await expect(page).toHaveURL(/.*\/dashboard/);
    await expect(page.locator('.user-greeting')).toContainText('你好，用户');
  });

  test('无效凭据显示错误', async ({ page }) => {
    await page.goto('https://example.com/login');

    await page.fill('input[name="email"]', 'nonexistent@example.com');
    await page.fill('input[name="password"]', 'WrongPassword');
    await page.click('button[type="submit"]');

    await expect(page.locator('.error-message')).toContainText('无效的邮箱或密码');
    await expect(page).toHaveURL(/.*\/login/);
  });

  test('登出功能', async ({ page }) => {
    // 首先登录
    await page.goto('https://example.com/login');
    await page.fill('input[name="email"]', 'existing-user@example.com');
    await page.fill('input[name="password"]', 'CorrectPassword123!');
    await page.click('button[type="submit"]');
    await expect(page).toHaveURL(/.*\/dashboard/);

    // 然后登出
    await page.click('button:has-text("Logout")');
    await expect(page).toHaveURL(/.*\/login/);
    await expect(page.locator('.login-form')).toBeVisible();
  });
});
```

## 常见 E2E 测试场景

### 购买流程

```
为...编写端到端测试电商结账流程。

## 用户流程
1. 用户浏览商品
2. 用户将商品添加到购物车
3. 用户查看购物车
4. 用户进行结账
5. 用户输入收货地址
6. 用户选择支付方式
7. 用户确认订单
8. 用户看到订单确认
9. 用户收到订单确认邮件

## 测试要求
1. 测试完整的 Happy Path
2. 测试购物车持久性（登录后）
3. 测试验证（空购物车、无效地址）
4. 测试支付流程
5. 测试数据库中的订单创建

## 输出
提供完整的 E2E 测试代码。
```

### 社交媒体互动

```
为...编写端到端测试社交媒体互动。

## 用户流程s to Test
1. 用户创建帖子
2. 用户点赞帖子
3. 用户评论帖子
4. 用户分享帖子
5. 用户编辑自己的帖子
6. 用户删除自己的帖子

## 测试要求
1. 以登录用户身份测试
2. 以匿名用户身份测试（应该被阻止）
3. 测试权限（无法编辑他人的帖子）
4. 测试实时更新（如适用）
5. 验证数据库状态

## 输出
提供完整的 E2E 测试代码。
```

### 管理员操作

```
为...编写端到端测试管理面板操作。

## 管理员流程测试
1. 管理员登录
2. 管理员查看用户列表
3. 管理员创建新用户
4. 管理员编辑用户权限
5. 管理员删除用户
6. 管理员查看分析仪表板
7. 管理员执行批量操作

## 测试要求
1. 测试管理员认证
2. 测试授权（普通用户被阻止）
3. 测试 CRUD 操作
4. 测试验证和错误处理
5. 测试审计日志（如适用）

## 输出
提供完整的 E2E 测试代码。
```

## E2E 测试最佳实践

### 1. 使用页面对象模型（Page Object Model）

```javascript
// pages/LoginPage.js
class LoginPage {
  constructor(page) {
    this.page = page;
    this.emailInput = page.locator('input[name="email"]');
    this.passwordInput = page.locator('input[name="password"]');
    this.loginButton = page.locator('button[type="submit"]');
    this.errorMessage = page.locator('.error-message');
  }

  async login(email, password) {
    await this.emailInput.fill(email);
    await this.passwordInput.fill(password);
    await this.loginButton.click();
  }

  async getErrorMessage() {
    return await this.errorMessage.textContent();
  }
}

// e2e/login.spec.js
const { test } = require('@playwright/test');
const LoginPage = require('../pages/LoginPage');

test('使用有效凭据登录', async ({ page }) => {
  const loginPage = new LoginPage(page);
  await loginPage.goto();
  await loginPage.login('user@example.com', 'password123');

  await page.waitForURL('**/dashboard');
});
```

### 2. 处理异步操作

```javascript
test('处理加载状态', async ({ page }) => {
  await page.goto('/products');

  // 等待加载完成
  await page.waitForSelector('.product-list', { state: 'visible' });
  await expect(page.locator('.loading-spinner')).not.toBeVisible();

  // 点击触发异步操作的按钮
  await page.click('button:has-text("Load More")');

  // 等待新内容加载
  await page.waitForResponse('**/api/products?page=2');
  await expect(page.locator('.product-item')).toHaveCount(20); // 10 + 10 更多
});
```

### 3. 测试数据清理

```javascript
test.describe('用户管理', () => {
  let testUserEmail;

  test.beforeEach(async ({ page }) => {
    testUserEmail = `test-${Date.now()}@example.com`;
  });

  test('创建用户', async ({ page }) => {
    // 创建用户
    await page.goto('/users/create');
    await page.fill('input[name="email"]', testUserEmail);
    await page.click('button[type="submit"]');

    await expect(page.locator('.success')).toBeVisible();
  });

  test.afterEach(async ({ page, request }) => {
    // 通过 API 清理测试数据
    await request.delete(`/api/users?email=${testUserEmail}`);
  });
});
```

### 4. 处理多标签页/窗口

```javascript
test('处理多个标签页', async ({ context }) => {
  const page1 = await context.newPage();
  await page1.goto('/');

  // 在新标签页中打开链接
  const [newPage] = await Promise.all([
    context.waitForEvent('page'),
    page1.click('a[target="_blank"]')
  ]);

  await newPage.waitForLoadState();
  await expect(newPage).toHaveURL(/external-site/);

  // 切换回原始页面
  await page1.bringToFront();
  await expect(page1).toHaveURL(/^\/$/);
});
```

### 5. 截图和录屏

```javascript
test('失败时截图', async ({ page }) => {
  try {
    await page.goto('/dashboard');
    await page.click('button:has-text("Generate Report")');

    // 等待报告生成
    await page.waitForSelector('.report-content', { timeout: 5000 });
  } catch (error) {
    // 失败时截图
    await page.screenshot({ path: `screenshots/failure-${Date.now()}.png` });
    throw error;
  }
});

// 或使用 test.fail() 自动截图
test.fail('待修复的已知问题', async ({ page }) => {
  await page.goto('/feature-with-bug');
  await expect(page.locator('.buggy-element')).toBeVisible();
});
```

## E2E 测试框架对比

| 特性 | Playwright | Cypress | Puppeteer |
|------|-----------|---------|-----------|
| **多浏览器支持** | ✅ Chrome, Firefox, Safari | ✅ Chrome, Firefox, Edge | ✅ Chrome |
| **多标签页支持** | ✅ 原生支持 | ⚠️ 有限支持 | ✅ 原生支持 |
| **并行执行** | ✅ 内置并行 | ✅ 内置并行 | ❌ 需要额外配置 |
| **等待策略** | ✅ 智能等待 | ✅ 智能等待 | ⚠️ 手动等待 |
| **网络拦截** | ✅ 强大的 API | ✅ 支持 | ✅ 支持 |
| **学习曲线** | 🟢 简单 | 🟢 简单 | 🟡 中等 |
| **TypeScript 支持** | ✅ 原生 | ✅ 原生 | ✅ 支持 |

## 性能和可靠性

### 避免脆弱测试

```javascript
// ❌ 不好：硬编码等待
await page.waitForTimeout(5000);

// ✅ 好：智能等待
await page.waitForSelector('.result', { state: 'visible' });
await page.waitForResponse('**/api/data');

// ❌ 不好：基于 DOM 结构的不稳定选择器
await page.click('div > div:nth-child(2) > button');

// ✅ 好：稳定的选择器
await page.click('button[data-testid="submit-button"]');
await page.getByRole('button', { name: '提交' }).click();
```

### 重试策略

```javascript
// playwright.config.js
module.exports = {
  use: {
    retries: 2, // 重试失败的测试 2 次
  },
  projects: [
    {
      name: 'chromium',
      use: { ...devices['Desktop Chrome'] },
    },
  ],
};
```

## 可访问性测试

```javascript
test('应该可访问', async ({ page }) => {
  await page.goto('/');

  // 检查正确的 ARIA 标签
  await expect(page.getByRole('button', { name: '提交' })).toBeVisible();
  await expect(page.getByRole('heading', { level: 1 })).toBeVisible();

  // 检查图片的 alt 文本
  const images = await page.locator('img').all();
  for (const img of images) {
    await expect(img).toHaveAttribute('alt');
  }
});
```

## 视觉回归测试

```javascript
test('视觉回归测试', async ({ page }) => {
  await page.goto('/homepage');

  // 截图并与基准进行比较
  await expect(page).toHaveScreenshot('homepage.png', {
    maxDiffPixels: 100, // 允许小的差异
  });
});
```

## E2E 测试检查清单

```
E2E 测试检查清单：

测试覆盖：
□ 关键用户流程已测试
□ Happy Path 已测试
□ 错误路径已测试
□ 边缘情况已测试
□ 跨浏览器测试（如适用）

测试质量：
□ 测试是隔离的（互不依赖）
□ 测试使用唯一的测试数据
□ 测试完成后清理
□ 测试不稳定
□ 测试有清晰的描述性名称

最佳实践：
□ 使用页面对象模型
□ 使用稳定的选择器（data-testid, aria-label）
□ 避免硬编码等待
□ 正确处理异步操作
□ 失败时截图

性能：
□ 测试在合理时间内运行（<5 分钟）
□ 配置了并行执行
□ 避免不必要的页面加载

可维护性：
□ 测试易于理解
□ 测试数据组织良好
□ 配置集中化
□ 文档是最新的
```
