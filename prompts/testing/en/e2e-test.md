# E2E Test Prompt Template

Template for writing end-to-end (E2E) test prompts.

## Scenarios

- Critical user flow testing
- Cross-module functionality testing
- Complete feature chain validation before deployment
- Real browser automation testing

## Prompt Template

```
Write E2E tests for the following user flow:

## User Flow
[Describe the complete user journey]

## Flow Steps
1. [Step 1]
2. [Step 2]
3. [Step 3]
...

## Test Requirements
1. Use real browser automation (Playwright/Cypress/Puppeteer)
2. Test complete flow (don't skip steps)
3. Verify key elements and text
4. Handle async operations (loading, emails)
5. Clean up test data

## Test Framework
[E2E framework, e.g., Playwright/Cypress]

## Test Data
[Prerequisite data]

## Output
Provide complete E2E test code.
```

## Best Practices

### 1. Use Page Object Model

```javascript
// pages/LoginPage.js
class LoginPage {
  constructor(page) {
    this.page = page;
    this.emailInput = page.locator('input[name="email"]');
    this.passwordInput = page.locator('input[name="password"]');
    this.loginButton = page.locator('button[type="submit"]');
  }

  async login(email, password) {
    await this.emailInput.fill(email);
    await this.passwordInput.fill(password);
    await this.loginButton.click();
  }
}
```

### 2. Handle Async Operations

```javascript
test('handle loading states', async ({ page }) => {
  await page.goto('/products');

  // Wait for loading to finish
  await page.waitForSelector('.product-list', { state: 'visible' });
  await expect(page.locator('.loading-spinner')).not.toBeVisible();
});
```

### 3. Use Stable Selectors

```javascript
// ❌ Bad
await page.click('div > div:nth-child(2) > button');

// ✅ Good
await page.getByRole('button', { name: 'Submit' }).click();
```

## Reference

- [phases/04-testing-validation.md](../../phases/04-testing-validation.md) - Testing methodology
