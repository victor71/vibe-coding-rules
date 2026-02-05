# 集成测试 Prompt 模板

集成测试的 prompt 模板。

## 场景

- API 端点测试
- 数据库交互测试
- 外部服务集成测试
- 消息队列测试

## Prompt 模板

```
为以下 API 编写集成测试：

## API 定义
- **方法：** [GET/POST/PUT/DELETE]
- **路径：** /api/[endpoint]
- **认证：** [Bearer Token / API Key / None]

## 请求
[请求体或参数定义]

## 响应
- **200：** [成功响应]
- **400：** [参数错误]
- **401：** [未认证]
- **403：** [权限不足]
- **404：** [资源不存在]
- **500：** [服务器错误]

## 测试要求
1. 测试正常成功情况
2. 测试参数验证
3. 测试认证（如适用）
4. 测试授权（不同用户角色）
5. 测试数据库验证（记录实际已创建/更新）
6. 测试错误情况

## 测试框架
[使用的框架，如 pytest+httpx/Jest+supertest]

## Mock 要求
- [哪些可以 mock]
- [哪些需要真实调用]

## 输出
提供完整的集成测试代码，包括 fixtures 和设置。
```

## 实际例子

### 例子：用户 API 集成测试

```
为用户创建 API 编写集成测试。

## API 定义
- **方法：** POST
- **路径：** /api/users
- **认证：** Bearer Token

## 请求体
```json
{
  "name": "string (必填，最大 50)",
  "email": "string (必填，有效邮箱)",
  "role": "enum [admin, user] (可选，默认：user)"
}
```

## 响应
- **201：** 返回创建的用户
- **400：** 无效参数
- **409：** 邮箱已存在
- **401：** 未认证
- **403：** 非管理员尝试创建管理员用户

## 测试要求
1. 测试成功创建用户
2. 测试参数验证（名称长度、邮箱格式）
3. 测试重复邮箱
4. 测试授权（只有管理员可以创建管理员用户）
5. 测试认证（无 token 返回 401）
6. 测试数据库验证（记录实际已写入）

## 测试框架
pytest + httpx + pytest-asyncio

## Mock 要求
- 可以 mock 认证中间件
- 使用测试数据库进行真实数据库操作

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
    """创建有效用户应返回 201。"""
    # 准备
    payload = {
        "name": "John Doe",
        "email": "john@example.com",
        "role": "user"
    }
    headers = {"Authorization": "Bearer valid_admin_token"}

    # 执行
    response = await async_client.post("/api/users", json=payload, headers=headers)

    # 断言 - HTTP 响应
    assert response.status_code == 201
    data = response.json()
    assert data["id"] is not None
    assert data["name"] == "John Doe"
    assert data["email"] == "john@example.com"
    assert data["role"] == "user"
    assert "password" not in data  # 不返回密码

    # 断言 - 数据库验证
    from models import User
    user = await User.get_by_id(db_session, data["id"])
    assert user is not None
    assert user.email == "john@example.com"

@pytest.mark.asyncio
async def test_create_user_invalid_email(async_client: AsyncClient):
    """使用无效邮箱创建用户应返回 400。"""
    # 准备
    payload = {
        "name": "John Doe",
        "email": "invalid-email",
        "role": "user"
    }
    headers = {"Authorization": "Bearer valid_admin_token"}

    # 执行
    response = await async_client.post("/api/users", json=payload, headers=headers)

    # 断言
    assert response.status_code == 400
    assert "email" in response.json()["errors"]

@pytest.mark.asyncio
async def test_create_user_duplicate_email(async_client: AsyncClient):
    """使用重复邮箱创建用户应返回 409。"""
    # 准备 - 创建第一个用户
    payload = {
        "name": "John Doe",
        "email": "john@example.com",
        "role": "user"
    }
    headers = {"Authorization": "Bearer valid_admin_token"}
    await async_client.post("/api/users", json=payload, headers=headers)

    # 执行 - 尝试创建重复
    response = await async_client.post("/api/users", json=payload, headers=headers)

    # 断言
    assert response.status_code == 409
    assert "email already exists" in response.json()["message"].lower()

@pytest.mark.asyncio
async def test_create_user_without_auth(async_client: AsyncClient):
    """未认证创建用户应返回 401。"""
    # 准备
    payload = {
        "name": "John Doe",
        "email": "john@example.com"
    }

    # 执行
    response = await async_client.post("/api/users", json=payload)

    # 断言
    assert response.status_code == 401

@pytest.mark.asyncio
async def test_create_admin_user_as_regular_user(async_client: AsyncClient):
    """普通用户不应能够创建管理员用户。"""
    # 准备
    payload = {
        "name": "John Doe",
        "email": "john@example.com",
        "role": "admin"
    }
    headers = {"Authorization": "Bearer valid_user_token"}  # 非管理员

    # 执行
    response = await async_client.post("/api/users", json=payload, headers=headers)

    # 断言
    assert response.status_code == 403
```

## 集成测试最佳实践

### 1. 使用测试数据库

```python
@pytest.fixture
async def test_db():
    """为每个测试创建测试数据库。"""
    async with create_test_database() as db:
        yield db
        # 测试后清理

@pytest.mark.asyncio
async def test_user_creation(test_db):
    user = await User.create(name="John", email="john@example.com", db=test_db)
    assert user.id is not None
```

### 2. 隔离测试

```python
@pytest.mark.asyncio
async def test_feature_a(test_db):
    # 测试 A
    pass

@pytest.mark.asyncio
async def test_feature_b(test_db):
    # 测试 B - 不应依赖测试 A
    pass
```

### 3. Mock 外部依赖

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

### 4. 使用 Fixture 管理资源

```python
@pytest.fixture
def authenticated_client(async_client):
    """返回带有认证头的客户端。"""
    async with async_client as client:
        client.headers.update({"Authorization": "Bearer test_token"})
        yield client

@pytest.mark.asyncio
async def test_protected_endpoint(authenticated_client):
    response = await authenticated_client.get("/api/protected")
    assert response.status_code == 200
```

## 不同场景的测试模板

### 数据库 CRUD 操作

```
为 [模型] 的 CRUD 操作编写集成测试。

## 模型
[模型定义]

## 端点
- POST /api/[resource] - 创建
- GET /api/[resource]/:id - 读取
- PUT /api/[resource]/:id - 更新
- DELETE /api/[resource]/:id - 删除

## 测试要求
1. 使用有效数据创建
2. 使用无效数据创建（验证错误）
3. 读取现有资源
4. 读取不存在的资源（404）
5. 更新现有资源
6. 更新不存在的资源
7. 删除现有资源
8. 删除不存在的资源
9. 每次操作后验证数据库状态

## 输出
提供完整的集成测试。
```

### 外部 API 集成

```
为外部服务集成编写集成测试。

## 外部服务
[服务名称，例如：Stripe, Twilio, SendGrid]

## 集成点
[哪些功能依赖外部服务]

## 测试要求
1. 为单元测试模拟外部服务
2. 使用实际外部服务测试（可选，使用测试凭据）
3. 测试错误处理（服务宕机、超时）
4. 测试速率限制
5. 测试重试逻辑

## 输出
提供包含模拟和集成测试的测试代码。
```

**示例：**

```python
# test_email_service.py
from unittest.mock import patch, AsyncMock
import pytest

class TestEmailService:
    """测试邮件服务集成。"""

    @patch('services.email_service.sendgrid_client.send')
    async def test_send_email_success(self, mock_send):
        """应通过 SendGrid 成功发送邮件。"""
        # 准备
        mock_send.return_value = {"status_code": 202}
        service = EmailService()

        # 执行
        result = await service.send_email(
            to="user@example.com",
            subject="Welcome",
            body="Hello!"
        )

        # 断言
        assert result["success"] is True
        mock_send.assert_called_once()

    @patch('services.email_service.sendgrid_client.send')
    async def test_send_email_retry_on_failure(self, mock_send):
        """SendGrid 失败时应重试。"""
        # 准备
        mock_send.side_effect = [
            Exception("Temporary failure"),
            {"status_code": 202}
        ]
        service = EmailService(max_retries=2)

        # 执行
        result = await service.send_email(
            to="user@example.com",
            subject="Welcome",
            body="Hello!"
        )

        # 断言
        assert result["success"] is True
        assert mock_send.call_count == 2

    @patch('services.email_service.sendgrid_client.send')
    async def test_send_email_max_retries_exceeded(self, mock_send):
        """达到最大重试次数后应返回失败。"""
        # 准备
        mock_send.side_effect = Exception("Persistent failure")
        service = EmailService(max_retries=3)

        # 执行
        result = await service.send_email(
            to="user@example.com",
            subject="Welcome",
            body="Hello!"
        )

        # 断言
        assert result["success"] is False
        assert mock_send.call_count == 3
```

### 消息队列测试

```
为消息队列处理编写集成测试。

## 队列系统
[RabbitMQ / Redis / Kafka / AWS SQS]

## 消息
[消息类型和格式]

## 测试要求
1. 测试消息发布
2. 测试消息消费
3. 测试错误处理（无效消息、处理失败）
4. 测试重试 / 死信队列行为
5. 测试并发处理

## 输出
提供包含队列 fixtures 的测试代码。
```

**示例：**

```python
# test_queue_consumer.py
import pytest
from unittest.mock import AsyncMock

@pytest.mark.asyncio
async def test_process_user_created_message(queue, consumer, db_session):
    """应正确处理用户创建消息。"""
    # 准备
    message = {
        "type": "user.created",
        "data": {
            "user_id": 123,
            "email": "test@example.com",
            "name": "Test User"
        }
    }
    await queue.publish("users", message)

    # 执行
    await consumer.process_next_message()

    # 断言
    user = await User.get_by_id(db_session, 123)
    assert user is not None
    assert user.email == "test@example.com"

@pytest.mark.asyncio
async def test_invalid_message_to_dead_letter(queue, consumer):
    """应将无效消息发送到死信队列。"""
    # 准备
    invalid_message = {"type": "invalid.type"}
    await queue.publish("users", invalid_message)

    # 执行
    await consumer.process_next_message()

    # 断言 - 消息应该在死信队列中
    dlq_messages = await queue.get_messages("users.dlq")
    assert len(dlq_messages) == 1
    assert dlq_messages[0] == invalid_message
```

## 测试环境配置

### Fixture 配置示例

```python
# conftest.py
import pytest
from httpx import AsyncClient
from sqlalchemy.ext.asyncio import create_async_engine, AsyncSession

@pytest.fixture
async def async_client():
    """创建用于测试的异步 HTTP 客户端。"""
    async with AsyncClient(app=app, base_url="http://test") as client:
        yield client

@pytest.fixture
async def db_session():
    """创建用于测试的数据库会话。"""
    engine = create_async_engine("sqlite+aiosqlite:///:memory:")
    async with engine.begin() as conn:
        await conn.run_sync(Base.metadata.create_all)

    async_session = AsyncSession(engine)
    yield async_session

    await async_session.close()
    await engine.dispose()

@pytest.fixture
def test_user(db_session):
    """创建测试用户。"""
    user = User(name="Test User", email="test@example.com")
    db_session.add(user)
    db_session.commit()
    return user
```

## 集成测试检查清单

```
集成测试检查清单：

数据库：
□ 使用单独的测试数据库
□ 每个测试后清理数据
□ 验证实际的数据库状态
□ 测试事务（提交/回滚）

API：
□ 测试所有状态码
□ 测试响应体结构
□ 测试认证/授权
□ 测试速率限制（如适用）

外部服务：
□ 尽可能模拟外部调用
□ 测试错误处理（服务宕机）
□ 测试超时
□ 测试重试逻辑

测试隔离：
□ 测试可以独立运行
□ 测试可以按任意顺序运行
□ 测试之间无共享状态
□ 每个测试有自己的数据

性能：
□ 如需要，添加性能基准测试
□ 监控查询数量
□ 检查 N+1 查询
```
