# E2E Test Prompt Template

端到端（E2E）测试的 prompt 模板。

## 场景

- 关键用户流程测试
- 跨模块功能测试
- 上线前完整功能链路验证
- 真实浏览器自动化测试

## Prompt 模板

```
Write E2E tests for the following user flow:

## User Flow
[描述完整的用户旅程]

## Flow Steps
1. [步骤 1]
2. [步骤 2]
3. [步骤 3]
...

## Test Requirements
1. Use real browser automation (Playwright/Cypress/Puppeteer)
2. Test complete flow (don't skip steps)
3. Verify key elements and text
4. Handle async operations (loading, emails)
5. Clean up test data

## Test Framework
[使用的 E2E 框架，如 Playwright/Cypress]

## Test Data
[需要预置的数据]

## Output
Provide complete E2E test code.
```

## 实际例子

### 例子：用户注册流程 E2E 测试

```
Write E2E tests for the user registration and login flow.

## User Flow
1. User visits the website
2. User clicks "Register"
3. User fills registration form
4. User submits form
5. User receives confirmation email
6. User clicks email confirmation link
7. User can login with credentials
8. User sees welcome dashboard

## Test Requirements
1. Use Playwright for browser automation
2. Test complete flow (don't skip steps)
3. Verify key elements and text
4. Handle email verification (mock or real)
5. Clean up test data after test

## Test Framework
Playwright with JavaScript/TypeScript

## Test Data
Use unique emails for each test run (include timestamp)

## Output
Provide complete E2E test code.
```

**示例输出：**

```javascript
// e2e/user-registration.spec.js
const { test, expect } = require('@playwright/test');

test.describe('User Registration Flow', () => {
  test.beforeEach(async ({ page }) => {
    // Setup: navigate to base URL
    await page.goto('https://example.com');
  });

  test('complete user registration from signup to dashboard', async ({ page }) => {
    // Step 1: Navigate to website
    await expect(page).toHaveTitle(/Welcome/);

    // Step 2: Click register button
    await page.click('button:has-text("Register")');
    await expect(page).toHaveURL(/.*\/register/);
    await expect(page.locator('h1')).toContainText('Create Account');

    // Step 3: Fill registration form
    const timestamp = Date.now();
    const email = `testuser${timestamp}@example.com`;

    await page.fill('input[name="name"]', 'John Doe');
    await page.fill('input[name="email"]', email);
    await page.fill('input[name="password"]', 'SecurePass123!');
    await page.fill('input[name="confirmPassword"]', 'SecurePass123!');

    // Step 4: Submit form
    await page.click('button[type="submit"]');

    // Step 5: Verify success message
    await expect(page.locator('.success-message')).toContainText('Registration successful');
    await expect(page.locator('.success-message')).toBeVisible();

    // Step 6: Check email (in real scenario, verify email was sent)
    // For E2E test, we might mock this or use test email service
    await expect(page.locator('.email-confirmation')).toContainText('Check your email');

    // Step 7: Simulate email confirmation (click confirmation link)
    // In real scenario, parse email and click link
    await page.goto(`https://example.com/verify-email?token=${timestamp}`);

    // Step 8: Navigate to login
    await page.click('button:has-text("Login")');
    await expect(page).toHaveURL(/.*\/login/);

    // Step 9: Login with new credentials
    await page.fill('input[name="email"]', email);
    await page.fill('input[name="password"]', 'SecurePass123!');
    await page.click('button[type="submit"]');

    // Step 10: Verify logged in
    await expect(page).toHaveURL(/.*\/dashboard/);
    await expect(page.locator('.user-name')).toContainText('John Doe');
    await expect(page.locator('.welcome-banner')).toBeVisible();
  });

  test('registration with invalid email shows validation error', async ({ page }) => {
    await page.click('button:has-text("Register")');

    await page.fill('input[name="name"]', 'John Doe');
    await page.fill('input[name="email"]', 'invalid-email');
    await page.fill('input[name="password"]', 'SecurePass123!');
    await page.fill('input[name="confirmPassword"]', 'SecurePass123!');

    await page.click('button[type="submit"]');

    // Verify error message
    await expect(page.locator('.error-message')).toContainText('Invalid email format');
    await expect(page).toHaveURL(/.*\/register/); // Stay on same page
  });

  test('password mismatch shows error', async ({ page }) => {
    await page.click('button:has-text("Register")');

    await page.fill('input[name="name"]', 'John Doe');
    await page.fill('input[name="email"]', 'john@example.com');
    await page.fill('input[name="password"]', 'Password123!');
    await page.fill('input[name="confirmPassword"]', 'DifferentPassword123!');

    await page.click('button[type="submit"]');

    await expect(page.locator('.error-message')).toContainText('Passwords do not match');
  });

  test('short password shows validation error', async ({ page }) => {
    await page.click('button:has-text("Register")');

    await page.fill('input[name="name"]', 'John Doe');
    await page.fill('input[name="email"]', 'john@example.com');
    await page.fill('input[name="password"]', 'short'); // Too short
    await page.fill('input[name="confirmPassword"]', 'short');

    await page.click('button[type="submit"]');

    await expect(page.locator('.error-message')).toContainText('Password must be at least 8 characters');
  });
});

test.describe('Login Flow', () => {
  test('successful login redirects to dashboard', async ({ page }) => {
    await page.goto('https://example.com/login');

    await page.fill('input[name="email"]', 'existing-user@example.com');
    await page.fill('input[name="password"]', 'CorrectPassword123!');
    await page.click('button[type="submit"]');

    await expect(page).toHaveURL(/.*\/dashboard/);
    await expect(page.locator('.user-greeting')).toContainText('Hello, User');
  });

  test('invalid credentials shows error', async ({ page }) => {
    await page.goto('https://example.com/login');

    await page.fill('input[name="email"]', 'nonexistent@example.com');
    await page.fill('input[name="password"]', 'WrongPassword');
    await page.click('button[type="submit"]');

    await expect(page.locator('.error-message')).toContainText('Invalid email or password');
    await expect(page).toHaveURL(/.*\/login/);
  });

  test('logout functionality', async ({ page }) => {
    // First login
    await page.goto('https://example.com/login');
    await page.fill('input[name="email"]', 'existing-user@example.com');
    await page.fill('input[name="password"]', 'CorrectPassword123!');
    await page.click('button[type="submit"]');
    await expect(page).toHaveURL(/.*\/dashboard/);

    // Then logout
    await page.click('button:has-text("Logout")');
    await expect(page).toHaveURL(/.*\/login/);
    await expect(page.locator('.login-form')).toBeVisible();
  });
});
```

## 常见 E2E 测试场景

### 购买流程

```
Write E2E tests for the e-commerce checkout flow.

## User Flow
1. User browses products
2. User adds product to cart
3. User views cart
4. User proceeds to checkout
5. User enters shipping address
6. User selects payment method
7. User confirms order
8. User sees order confirmation
9. User receives order confirmation email

## Test Requirements
1. Test complete happy path
2. Test cart persistence (after login)
3. Test validation (empty cart, invalid address)
4. Test payment flow
5. Test order creation in database

## Output
Provide complete E2E test code.
```

### 社交媒体互动

```
Write E2E tests for social media interactions.

## User Flows to Test
1. User creates a post
2. User likes a post
3. User comments on a post
4. User shares a post
5. User edits own post
6. User deletes own post

## Test Requirements
1. Test as logged-in user
2. Test as anonymous user (should be blocked)
3. Test permissions (can't edit others' posts)
4. Test real-time updates (if applicable)
5. Verify database state

## Output
Provide complete E2E test code.
```

### 管理员操作

```
Write E2E tests for admin panel operations.

## Admin Flows to Test
1. Admin logs in
2. Admin views user list
3. Admin creates new user
4. Admin edits user permissions
5. Admin deletes user
6. Admin views analytics dashboard
7. Admin performs bulk operations

## Test Requirements
1. Test admin authentication
2. Test authorization (regular users blocked)
3. Test CRUD operations
4. Test validation and error handling
5. Test audit logs (if applicable)

## Output
Provide complete E2E test code.
```

## E2E 测试最佳实践

### 1. 使用 Page Object Model

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

test('login with valid credentials', async ({ page }) => {
  const loginPage = new LoginPage(page);
  await loginPage.goto();
  await loginPage.login('user@example.com', 'password123');

  await page.waitForURL('**/dashboard');
});
```

### 2. 处理异步操作

```javascript
test('handle loading states', async ({ page }) => {
  await page.goto('/products');

  // Wait for loading to finish
  await page.waitForSelector('.product-list', { state: 'visible' });
  await expect(page.locator('.loading-spinner')).not.toBeVisible();

  // Click a button that triggers async operation
  await page.click('button:has-text("Load More")');

  // Wait for new content to load
  await page.waitForResponse('**/api/products?page=2');
  await expect(page.locator('.product-item')).toHaveCount(20); // 10 + 10 more
});
```

### 3. 测试数据清理

```javascript
test.describe('User Management', () => {
  let testUserEmail;

  test.beforeEach(async ({ page }) => {
    testUserEmail = `test-${Date.now()}@example.com`;
  });

  test('create user', async ({ page }) => {
    // Create user
    await page.goto('/users/create');
    await page.fill('input[name="email"]', testUserEmail);
    await page.click('button[type="submit"]');

    await expect(page.locator('.success')).toBeVisible();
  });

  test.afterEach(async ({ page, request }) => {
    // Clean up test data via API
    await request.delete(`/api/users?email=${testUserEmail}`);
  });
});
```

### 4. 处理多标签页/窗口

```javascript
test('handle multiple tabs', async ({ context }) => {
  const page1 = await context.newPage();
  await page1.goto('/');

  // Open link in new tab
  const [newPage] = await Promise.all([
    context.waitForEvent('page'),
    page1.click('a[target="_blank"]')
  ]);

  await newPage.waitForLoadState();
  await expect(newPage).toHaveURL(/external-site/);

  // Switch back to original page
  await page1.bringToFront();
  await expect(page1).toHaveURL(/^\/$/);
});
```

### 5. 截图和录屏

```javascript
test('with screenshot on failure', async ({ page }) => {
  try {
    await page.goto('/dashboard');
    await page.click('button:has-text("Generate Report")');

    // Wait for report generation
    await page.waitForSelector('.report-content', { timeout: 5000 });
  } catch (error) {
    // Take screenshot on failure
    await page.screenshot({ path: `screenshots/failure-${Date.now()}.png` });
    throw error;
  }
});

// Or use test.fail() to take screenshot automatically
test.fail('known issue to be fixed', async ({ page }) => {
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
// ❌ Bad: Hardcoded waits
await page.waitForTimeout(5000);

// ✅ Good: Smart waits
await page.waitForSelector('.result', { state: 'visible' });
await page.waitForResponse('**/api/data');

// ❌ Bad: Flaky selectors based on DOM structure
await page.click('div > div:nth-child(2) > button');

// ✅ Good: Stable selectors
await page.click('button[data-testid="submit-button"]');
await page.getByRole('button', { name: 'Submit' }).click();
```

### 重试策略

```javascript
// playwright.config.js
module.exports = {
  use: {
    retries: 2, // Retry failed tests 2 times
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
test('should be accessible', async ({ page }) => {
  await page.goto('/');

  // Check for proper ARIA labels
  await expect(page.getByRole('button', { name: 'Submit' })).toBeVisible();
  await expect(page.getByRole('heading', { level: 1 })).toBeVisible();

  // Check alt text on images
  const images = await page.locator('img').all();
  for (const img of images) {
    await expect(img).toHaveAttribute('alt');
  }
});
```

## 视觉回归测试

```javascript
test('visual regression test', async ({ page }) => {
  await page.goto('/homepage');

  // Take screenshot and compare with baseline
  await expect(page).toHaveScreenshot('homepage.png', {
    maxDiffPixels: 100, // Allow small differences
  });
});
```

## E2E 测试检查清单

```
E2E Test Checklist:

Test Coverage:
□ Critical user flows tested
□ Happy path tested
□ Error paths tested
□ Edge cases tested
□ Cross-browser testing (if applicable)

Test Quality:
□ Tests are isolated (don't depend on each other)
□ Tests use unique test data
□ Tests clean up after themselves
□ Tests are not flaky
□ Tests have clear, descriptive names

Best Practices:
□ Use Page Object Model
□ Use stable selectors (data-testid, aria-label)
□ Avoid hardcoded waits
□ Handle async operations properly
□ Take screenshots on failure

Performance:
□ Tests run in a reasonable time (<5 min)
□ Parallel execution configured
□ Unnecessary page loads avoided

Maintenance:
□ Tests are easy to understand
□ Test data is well-organized
□ Configuration is centralized
□ Documentation is up to date
```
