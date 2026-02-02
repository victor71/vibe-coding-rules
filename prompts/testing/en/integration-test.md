# Integration Test Prompt Template

Template for writing integration test prompts.

## Scenarios

- API endpoint testing
- Database interaction testing
- External service integration testing
- Message queue testing

## Prompt Template

```
Write integration tests for the following API:

## API Definition
- **Method:** [GET/POST/PUT/DELETE]
- **Path:** /api/[endpoint]
- **Authentication:** [Bearer Token / API Key / None]

## Request
[Request body or parameters]

## Response
- **200 OK:** [Success response]
- **400 Bad Request:** [Parameter error]
- **401 Unauthorized:** [Not authenticated]
- **403 Forbidden:** [Permission denied]
- **404 Not Found:** [Resource not found]
- **500 Internal Server Error:** [Server error]

## Test Requirements
1. Test normal success case
2. Test parameter validation
3. Test authentication (if applicable)
4. Test authorization (different user roles)
5. Test database verification (record actually created/updated)
6. Test error cases

## Test Framework
[Framework, e.g., pytest+httpx/Jest+supertest]

## Mock Requirements
- [What can be mocked]
- [What needs real calls]

## Output
Provide complete integration test code.
```

## Best Practices

### 1. Use Test Database

```python
@pytest.fixture
async def test_db():
    """Create a test database for each test."""
    async with create_test_database() as db:
        yield db
        # Clean up after test
```

### 2. Isolate Tests

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

### 3. Mock External Dependencies

```python
from unittest.mock import patch, AsyncMock

@pytest.mark.asyncio
async def test_payment_success():
    with patch('services.payment_gateway.charge', new_callable=AsyncMock) as mock_charge:
        mock_charge.return_value = {"status": "success"}

        result = await process_payment(user_id=1, amount=100)

        assert result["status"] == "success"
```

## Reference

- [phases/04-testing-validation.md](../../phases/04-testing-validation.md) - Testing methodology
