# Unit Test Prompt Template

单元测试的 prompt 模板。

## 场景

- 测试纯函数
- 测试业务逻辑
- 测试工具函数
- 测试复杂算法

## Prompt 模板

```
Write unit tests for the following code:

## Code
[粘贴代码]

## Test Framework
[使用的测试框架，如 Jest/Pytest/PHPUnit]

## Requirements
1. Test normal cases (Happy Path)
2. Test edge cases (empty input, max/min values)
3. Test error cases (invalid input, null/undefined)
4. Use descriptive test names
5. Use given-when-then pattern (optional)
6. At least 5 test cases

## Output Format
Provide the complete test file, including:
- All test cases
- Necessary setup/teardown
- Mocks/Stubs (if needed)
- Clear comments explaining test intent
```

## 实际例子

### 例子：测试用户工具函数

```
Write unit tests for these utility functions:

```python
def validate_email(email):
    if not email or not isinstance(email, str):
        return False
    if '@' not in email:
        return False
    parts = email.split('@')
    if len(parts) != 2:
        return False
    local, domain = parts
    if not local or not domain:
        return False
    if '.' not in domain:
        return False
    return True

def calculate_age(birth_date):
    from datetime import date
    today = date.today()
    age = today.year - birth_date.year
    if (today.month, today.day) < (birth_date.month, birth_date.day):
        age -= 1
    return age
```

## Test Framework
pytest

## Requirements
1. Test normal cases (valid email, adult age)
2. Test edge cases (empty, boundary dates)
3. Test error cases (invalid formats, None)
4. Use descriptive test names
5. Use given-when-then pattern
6. At least 5 test cases per function

## Output Format
Provide the complete test file with:
- All test cases
- Fixtures if needed
- Clear comments explaining what each test validates
```

**示例输出：**

```python
# test_user_utils.py
import pytest
from datetime import date, timedelta
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
        # Given
        birth_date = date.today() - timedelta(days=365 * 30)

        # When
        age = calculate_age(birth_date)

        # Then
        assert age == 30

    def test_age_calculation_for_child(self):
        """Calculate age for child."""
        # Given
        birth_date = date.today() - timedelta(days=365 * 5)

        # When
        age = calculate_age(birth_date)

        # Then
        assert age == 5

    def test_age_calculation_for_today(self):
        """Age should be 0 for today's birth."""
        # Given
        birth_date = date.today()

        # When
        age = calculate_age(birth_date)

        # Then
        assert age == 0

    def test_age_calculated_before_birthday_this_year(self):
        """Should be one year less if birthday hasn't occurred yet."""
        # Given - birth date 30 years ago, but birthday is tomorrow
        today = date(2024, 6, 15)
        birth_date = date(1994, 6, 16)

        # When
        age = today.year - birth_date.year
        if (today.month, today.day) < (birth_date.month, birth_date.day):
            age -= 1

        # Then
        assert age == 29
```

## 最佳实践

### 1. 测试命名要清晰

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

### 2. 使用 AAA 模式（Arrange-Act-Assert）

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

### 3. 测试一个行为

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

### 4. 避免测试实现细节

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

## TDD（测试驱动开发）

```
Implement [feature] using TDD.

Requirements:
[功能需求]

## TDD Process
1. **Red**: Write a failing test
2. **Green**: Write simplest code to make test pass
3. **Refactor**: Improve code while keeping tests passing

## Requirements
1. Follow TDD cycle step by step
2. Write only one test per cycle
3. Show test code and implementation code for each step
4. Provide complete code at the end

## Output Format
### Cycle 1: Test Case X
**Test Code:**
```python
```

**Implementation Code:**
```python
```

**Status:** ✅ Pass / ❌ Fail

### Cycle 2: ...
```

## Mock/Stub 测试

```
Write unit tests for [function/class] that depends on external services.

## Function to Test
[粘贴代码]

## Dependencies
- [外部服务 1]
- [外部服务 2]

## Requirements
1. Mock all external dependencies
2. Test that dependencies are called correctly
3. Test error handling when dependencies fail
4. Use pytest-mock/unittest.mock

## Output Format
Provide test file with:
- Proper mock setup
- Verification of mock calls
- Tests for both success and failure cases
```

**示例：**

```python
# test_payment_service.py
from unittest.mock import Mock, patch
import pytest

class TestPaymentService:
    """Test payment service with mocked external API."""

    @patch('services.payment_gateway.charge')
    def test_successful_payment(self, mock_charge):
        """Should return success when payment gateway returns success."""
        # Arrange
        mock_charge.return_value = {"status": "success", "transaction_id": "12345"}
        service = PaymentService()

        # Act
        result = service.process_payment(user_id=1, amount=100)

        # Then
        assert result["status"] == "success"
        assert result["transaction_id"] == "12345"
        mock_charge.assert_called_once_with(amount=100)

    @patch('services.payment_gateway.charge')
    def test_failed_payment(self, mock_charge):
        """Should handle payment gateway failures."""
        # Arrange
        mock_charge.return_value = {"status": "failed", "error": "Insufficient funds"}
        service = PaymentService()

        # Act
        result = service.process_payment(user_id=1, amount=100)

        # Then
        assert result["status"] == "failed"
        assert "Insufficient funds" in result["error"]

    @patch('services.payment_gateway.charge')
    def test_payment_gateway_timeout(self, mock_charge):
        """Should retry on timeout."""
        # Arrange
        mock_charge.side_effect = [TimeoutError("Gateway timeout"), {"status": "success"}]
        service = PaymentService()

        # Act
        result = service.process_payment(user_id=1, amount=100, max_retries=2)

        # Then
        assert result["status"] == "success"
        assert mock_charge.call_count == 2
```

## 测试覆盖率分析

```
Analyze the test coverage for [module/function].

## Coverage Report
[粘贴覆盖率报告]

## Requirements
1. Identify untested critical code paths
2. Identify areas needing additional tests
3. Suggest specific test cases

## Output Format
### Coverage Summary
- Overall Coverage: X%
- Line Coverage: X%
- Branch Coverage: X%

### Untested Critical Code
1. [File:Line]
   - Functionality: [description]
   - Risk: [why this needs testing]
   - Suggestion: [how to test]

### Test Suggestions
1. [Suggestion 1]
2. [Suggestion 2]
```
