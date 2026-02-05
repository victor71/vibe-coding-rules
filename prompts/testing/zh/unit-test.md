# Unit Test Prompt Template

单元测试的 prompt 模板。

## 场景

- 测试纯函数
- 测试业务逻辑
- 测试工具函数
- 测试复杂算法

## Prompt 模板

```
为以下代码编写单元测试：

## 代码
[粘贴代码]

## 测试框架
[使用的测试框架，如 Jest/Pytest/PHPUnit]

## 要求
1. 测试正常情况（Happy Path）
2. 测试边界情况（空输入、最大值/最小值）
3. 测试异常情况（无效输入、null/undefined）
4. 使用描述性测试名称
5. 使用 given-when-then 模式（可选）
6. 至少5个测试用例

## 输出格式
提供完整的测试文件，包括：
- 所有测试用例
- 必要的 setup/teardown
- Mocks/Stubs（如需要）
- 清晰的注释说明测试意图
```

## 实际例子

### 例子：测试用户工具函数

```
为...编写单元测试这些工具函数：

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

## 测试框架
pytest

## 要求
1. 测试正常情况（有效邮箱、成年人年龄）
2. 测试边缘情况（空值、边界日期）
3. 测试错误情况（无效格式、None）
4. 使用描述性测试名称
5. 使用 given-when-then 模式
6. 每个函数至少 5 个测试用例

## 输出格式
提供完整的测试文件，包括：
- 所有测试用例
- 如需要则提供 fixtures
- 清晰的注释说明每个测试验证的内容
```

**示例输出：**

```python
# test_user_utils.py
import pytest
from datetime import date, timedelta
from user_utils import validate_email, calculate_age

class TestValidateEmail:
    """测试邮箱验证函数。"""

    def test_valid_email_returns_true(self):
        """有效邮箱应返回 True。"""
        # Given
        email = "user@example.com"

        # When
        result = validate_email(email)

        # Then
        assert result is True

    def test_email_without_at_sign_returns_false(self):
        """没有 @ 的邮箱应返回 False。"""
        # Given
        email = "userexample.com"

        # When
        result = validate_email(email)

        # Then
        assert result is False

    def test_empty_email_returns_false(self):
        """空邮箱应返回 False。"""
        assert validate_email("") is False

    def test_none_email_returns_false(self):
        """None 邮箱应返回 False。"""
        assert validate_email(None) is False

    def test_email_with_special_chars_valid(self):
        """带点和加号的邮箱应该是有效的。"""
        assert validate_email("user.name+tag@example.com") is True


class TestCalculateAge:
    """测试年龄计算函数。"""

    def test_age_calculation_for_adult(self):
        """计算成年人的年龄。"""
        # Given
        birth_date = date.today() - timedelta(days=365 * 30)

        # When
        age = calculate_age(birth_date)

        # Then
        assert age == 30

    def test_age_calculation_for_child(self):
        """计算儿童的年龄。"""
        # Given
        birth_date = date.today() - timedelta(days=365 * 5)

        # When
        age = calculate_age(birth_date)

        # Then
        assert age == 5

    def test_age_calculation_for_today(self):
        """今天出生的年龄应为 0。"""
        # Given
        birth_date = date.today()

        # When
        age = calculate_age(birth_date)

        # Then
        assert age == 0

    def test_age_calculated_before_birthday_this_year(self):
        """如果生日还未发生，应少一年。"""
        # Given - 30 年前出生，但生日是明天
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

**❌ 不好：**
```python
def test_1():
    assert add(2, 3) == 5
```

**✅ 好：**
```python
def test_add_two_positive_numbers_returns_sum(self):
    """添加两个正数应返回它们的和。"""
    assert add(2, 3) == 5
```

### 2. 使用 AAA 模式（Arrange-Act-Assert）

```python
def test_calculate_discount_for_vip_user(self):
    # Arrange（准备）
    user = User(type="VIP")
    product = Product(price=100)

    # Act（执行）
    discounted_price = calculate_discount(user, product)

    # Assert（断言）
    assert discounted_price == 80  # VIP 20% 折扣
```

### 3. 测试一个行为

**❌ 不好：** 一个测试测多个行为
```python
def test_user_operations(self):
    user = User("john")
    assert user.name == "john"
    user.set_age(30)
    assert user.age == 30
    user.birthday()
    assert user.age == 31
```

**✅ 好：** 每个行为一个测试
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

**❌ 不好：**
```python
def test_user_name_storage(self):
    user = User("john")
    assert user._name == "john"  # 测试私有属性
```

**✅ 好：**
```python
def test_user_name_retrieval(self):
    user = User("john")
    assert user.get_name() == "john"  # 测试公共接口
```

## TDD（测试驱动开发）

```
使用 TDD 实现 [功能]。

需求：
[功能需求]

## TDD 流程
1. **Red**：编写一个失败的测试
2. **Green**：编写最简单的代码使测试通过
3. **Refactor**：改进代码同时保持测试通过

## 要求
1. 逐步遵循 TDD 周期
2. 每个周期只写一个测试
3. 显示每个步骤的测试代码和实现代码
4. 最后提供完整代码

## 输出格式
### 周期 1：测试用例 X
**测试代码：**
```python
```

**实现代码：**
```python
```

**状态：** ✅ 通过 / ❌ 失败

### 周期 2：...
```

## Mock/Stub 测试

```
为...编写单元测试 [函数/类]，该函数/类依赖外部服务。

## 要测试的函数
[粘贴代码]

## 依赖
- [外部服务 1]
- [外部服务 2]

## 要求
1. Mock 所有外部依赖
2. 测试依赖是否被正确调用
3. 测试依赖失败时的错误处理
4. 使用 pytest-mock/unittest.mock

## 输出格式
提供测试文件，包括：
- 正确的 mock 设置
- mock 调用的验证
- 成功和失败情况的测试
```

**示例：**

```python
# test_payment_service.py
from unittest.mock import Mock, patch
import pytest

class TestPaymentService:
    """使用模拟外部 API 测试支付服务。"""

    @patch('services.payment_gateway.charge')
    def test_successful_payment(self, mock_charge):
        """当支付网关返回成功时应返回成功。"""
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
        """应处理支付网关失败。"""
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
        """应在超时时重试。"""
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
分析 [模块/函数] 的测试覆盖率。

## 覆盖率报告
[粘贴覆盖率报告]

## 要求
1. 识别未测试的关键代码路径
2. 识别需要额外测试的区域
3. 建议具体的测试用例

## 输出格式
### 覆盖率汇总
- 整体覆盖率：X%
- 行覆盖率：X%
- 分支覆盖率：X%

### 未测试的关键代码
1. [文件：行号]
   - 功能：[描述]
   - 风险：[为什么需要测试]
   - 建议：[如何测试]

### 测试建议
1. [建议 1]
2. [建议 2]
```
