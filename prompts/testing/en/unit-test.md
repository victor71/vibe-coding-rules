# Unit Test Prompt Template

Template for writing unit test prompts.

## Scenarios

- Testing pure functions
- Testing business logic
- Testing utility functions
- Testing complex algorithms

## Prompt Template

```
Write unit tests for the following code:

## Code
[paste code]

## Test Framework
[Testing framework, e.g., Jest/Pytest/PHPUnit]

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

## Best Practices

### 1. Clear Test Names

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

### 2. Use AAA Pattern (Arrange-Act-Assert)

```python
def test_calculate_discount_for_vip_user(self):
    # Arrange
    user = User(type="VIP")
    product = Product(price=100)

    # Act
    discounted_price = calculate_discount(user, product)

    # Assert
    assert discounted_price == 80  # VIP 20% off
```

### 3. Test One Behavior

**❌ Bad:** Testing multiple behaviors in one test
```python
def test_user_operations(self):
    user = User("john")
    assert user.name == "john"
    user.set_age(30)
    assert user.age == 30
    user.birthday()
    assert user.age == 31
```

**✅ Good:** One behavior per test
```python
def test_user_name_initialization(self):
    user = User("john")
    assert user.name == "john"

def test_user_age_setting(self):
    user = User("john")
    user.set_age(30)
    assert user.age == 30
```

## Reference

- [phases/04-testing-validation.md](../../phases/04-testing-validation.md) - Testing methodology
