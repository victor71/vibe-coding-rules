# Integration Test Prompt Template

集成测试的 prompt 模板。

## 场景

- API 端点测试
- 数据库交互测试
- 外部服务集成测试
- 消息队列测试

## Prompt 模板

```
Write integration tests for the following API:

## API Definition
- **Method:** [GET/POST/PUT/DELETE]
- **Path:** /api/[endpoint]
- **Authentication:** [Bearer Token / API Key / None]

## Request
[请求体或参数定义]

## Response
- **200 OK:** [成功响应]
- **400 Bad Request:** [参数错误]
- **401 Unauthorized:** [未认证]
- **403 Forbidden:** [权限不足]
- **404 Not Found:** [资源不存在]
- **500 Internal Server Error:** [服务器错误]

## Test Requirements
1. Test normal success case
2. Test parameter validation
3. Test authentication (if applicable)
4. Test authorization (different user roles)
5. Test database verification (record actually created/updated)
6. Test error cases

## Test Framework
[使用的框架，如 pytest+httpx/Jest+supertest]

## Mock Requirements
- [哪些可以 mock]
- [哪些需要真实调用]

## Output
Provide complete integration test code with fixtures and setup.
```

## 实际例子

### 例子：用户 API 集成测试

```
Write integration tests for the user creation API.

## API Definition
- **Method:** POST
- **Path:** /api/users
- **Authentication:** Bearer Token

## Request Body
```json
{
  "name": "string (required, max 50)",
  "email": "string (required, valid email)",
  "role": "enum [admin, user] (optional, default: user)"
}
```

## Response
- **201 Created:** Returns created user
- **400 Bad Request:** Invalid parameters
- **409 Conflict:** Email already exists
- **401 Unauthorized:** No authentication
- **403 Forbidden:** Non-admin trying to create admin user

## Test Requirements
1. Test successful user creation
2. Test parameter validation (name length, email format)
3. Test duplicate email
4. Test authorization (only admin can create admin users)
5. Test authentication (no token returns 401)
6. Test database verification (record actually written)

## Test Framework
pytest + httpx + pytest-asyncio

## Mock Requirements
- Auth middleware can be mocked
- Use test database for real DB operations

## Output
Provide complete integration test code.
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

    # Assert - HTTP response
    assert response.status_code == 201
    data = response.json()
    assert data["id"] is not None
    assert data["name"] == "John Doe"
    assert data["email"] == "john@example.com"
    assert data["role"] == "user"
    assert "password" not in data  # Don't return password

    # Assert - Database verification
    from models import User
    user = await User.get_by_id(db_session, data["id"])
    assert user is not None
    assert user.email == "john@example.com"

@pytest.mark.asyncio
async def test_create_user_invalid_email(async_client: AsyncClient):
    """Creating user with invalid email should return 400."""
    # Arrange
    payload = {
        "name": "John Doe",
        "email": "invalid-email",
        "role": "user"
    }
    headers = {"Authorization": "Bearer valid_admin_token"}

    # Act
    response = await async_client.post("/api/users", json=payload, headers=headers)

    # Assert
    assert response.status_code == 400
    assert "email" in response.json()["errors"]

@pytest.mark.asyncio
async def test_create_user_duplicate_email(async_client: AsyncClient):
    """Creating user with duplicate email should return 409."""
    # Arrange - Create first user
    payload = {
        "name": "John Doe",
        "email": "john@example.com",
        "role": "user"
    }
    headers = {"Authorization": "Bearer valid_admin_token"}
    await async_client.post("/api/users", json=payload, headers=headers)

    # Act - Try to create duplicate
    response = await async_client.post("/api/users", json=payload, headers=headers)

    # Assert
    assert response.status_code == 409
    assert "email already exists" in response.json()["message"].lower()

@pytest.mark.asyncio
async def test_create_user_without_auth(async_client: AsyncClient):
    """Creating user without authentication should return 401."""
    # Arrange
    payload = {
        "name": "John Doe",
        "email": "john@example.com"
    }

    # Act
    response = await async_client.post("/api/users", json=payload)

    # Assert
    assert response.status_code == 401

@pytest.mark.asyncio
async def test_create_admin_user_as_regular_user(async_client: AsyncClient):
    """Regular user should not be able to create admin users."""
    # Arrange
    payload = {
        "name": "John Doe",
        "email": "john@example.com",
        "role": "admin"
    }
    headers = {"Authorization": "Bearer valid_user_token"}  # Not admin

    # Act
    response = await async_client.post("/api/users", json=payload, headers=headers)

    # Assert
    assert response.status_code == 403
```

## 集成测试最佳实践

### 1. 使用测试数据库

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

### 2. 隔离测试

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
    """Return a client with authentication headers."""
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
Write integration tests for CRUD operations on [Model].

## Model
[模型定义]

## Endpoints
- POST /api/[resource] - Create
- GET /api/[resource]/:id - Read
- PUT /api/[resource]/:id - Update
- DELETE /api/[resource]/:id - Delete

## Test Requirements
1. Create with valid data
2. Create with invalid data (validation errors)
3. Read existing resource
4. Read non-existing resource (404)
5. Update existing resource
6. Update non-existing resource
7. Delete existing resource
8. Delete non-existing resource
9. Verify database state after each operation

## Output
Provide complete integration tests.
```

### 外部 API 集成

```
Write integration tests for external service integration.

## External Service
[服务名称，例如：Stripe, Twilio, SendGrid]

## Integration Points
[哪些功能依赖外部服务]

## Test Requirements
1. Mock external service for unit tests
2. Test with actual external service (optional, with test credentials)
3. Test error handling (service down, timeout)
4. Test rate limiting
5. Test retry logic

## Output
Provide test code with mocks and integration tests.
```

**示例：**

```python
# test_email_service.py
from unittest.mock import patch, AsyncMock
import pytest

class TestEmailService:
    """Test email service integration."""

    @patch('services.email_service.sendgrid_client.send')
    async def test_send_email_success(self, mock_send):
        """Should successfully send email via SendGrid."""
        # Arrange
        mock_send.return_value = {"status_code": 202}
        service = EmailService()

        # Act
        result = await service.send_email(
            to="user@example.com",
            subject="Welcome",
            body="Hello!"
        )

        # Assert
        assert result["success"] is True
        mock_send.assert_called_once()

    @patch('services.email_service.sendgrid_client.send')
    async def test_send_email_retry_on_failure(self, mock_send):
        """Should retry on SendGrid failure."""
        # Arrange
        mock_send.side_effect = [
            Exception("Temporary failure"),
            {"status_code": 202}
        ]
        service = EmailService(max_retries=2)

        # Act
        result = await service.send_email(
            to="user@example.com",
            subject="Welcome",
            body="Hello!"
        )

        # Assert
        assert result["success"] is True
        assert mock_send.call_count == 2

    @patch('services.email_service.sendgrid_client.send')
    async def test_send_email_max_retries_exceeded(self, mock_send):
        """Should return failure after max retries."""
        # Arrange
        mock_send.side_effect = Exception("Persistent failure")
        service = EmailService(max_retries=3)

        # Act
        result = await service.send_email(
            to="user@example.com",
            subject="Welcome",
            body="Hello!"
        )

        # Assert
        assert result["success"] is False
        assert mock_send.call_count == 3
```

### 消息队列测试

```
Write integration tests for message queue handling.

## Queue System
[RabbitMQ / Redis / Kafka / AWS SQS]

## Messages
[消息类型和格式]

## Test Requirements
1. Test message publishing
2. Test message consumption
3. Test error handling (invalid messages, processing failures)
4. Test retry / dead-letter queue behavior
5. Test concurrent processing

## Output
Provide test code with queue fixtures.
```

**示例：**

```python
# test_queue_consumer.py
import pytest
from unittest.mock import AsyncMock

@pytest.mark.asyncio
async def test_process_user_created_message(queue, consumer, db_session):
    """Should process user creation message correctly."""
    # Arrange
    message = {
        "type": "user.created",
        "data": {
            "user_id": 123,
            "email": "test@example.com",
            "name": "Test User"
        }
    }
    await queue.publish("users", message)

    # Act
    await consumer.process_next_message()

    # Assert
    user = await User.get_by_id(db_session, 123)
    assert user is not None
    assert user.email == "test@example.com"

@pytest.mark.asyncio
async def test_invalid_message_to_dead_letter(queue, consumer):
    """Should send invalid messages to dead-letter queue."""
    # Arrange
    invalid_message = {"type": "invalid.type"}
    await queue.publish("users", invalid_message)

    # Act
    await consumer.process_next_message()

    # Assert - Message should be in dead-letter queue
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
    """Create async HTTP client for testing."""
    async with AsyncClient(app=app, base_url="http://test") as client:
        yield client

@pytest.fixture
async def db_session():
    """Create database session for testing."""
    engine = create_async_engine("sqlite+aiosqlite:///:memory:")
    async with engine.begin() as conn:
        await conn.run_sync(Base.metadata.create_all)

    async_session = AsyncSession(engine)
    yield async_session

    await async_session.close()
    await engine.dispose()

@pytest.fixture
def test_user(db_session):
    """Create a test user."""
    user = User(name="Test User", email="test@example.com")
    db_session.add(user)
    db_session.commit()
    return user
```

## 集成测试检查清单

```
Integration Test Checklist:

Database:
□ Use separate test database
□ Clean up data after each test
□ Verify actual DB state
□ Test transactions (commit/rollback)

API:
□ Test all status codes
□ Test response body structure
□ Test authentication/authorization
□ Test rate limiting (if applicable)

External Services:
□ Mock external calls when possible
□ Test error handling (service down)
□ Test timeouts
□ Test retry logic

Test Isolation:
□ Tests can run independently
□ Tests can run in any order
□ No shared state between tests
□ Each test has its own data

Performance:
□ Add performance benchmarks if needed
□ Monitor query counts
□ Check for N+1 queries
```
