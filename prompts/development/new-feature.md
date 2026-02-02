# New Feature Prompt Template

实现新功能的 prompt 模板。

## 场景

- 实现新功能
- 添加新 API 端点
- 集成新服务
- 添加 UI 组件

## Prompt 模板

```
Implement a new feature: [feature description]

Requirements:
- [Detailed requirement list]

Technical specifications:
- [Technology stack, APIs, constraints]

Implementation steps:
1. [High-level step 1]
2. [High-level step 2]
3. [High-level step 3]

Deliverables:
- Feature implementation
- Unit tests
- Integration tests (if applicable)
- Documentation (API docs, user guide)
- Update CHANGELOG.md

Context:
- [How this fits into existing codebase]
- [Dependencies on other modules]
```

## 实际例子

### 例子：实现用户通知系统

```
Implement a user notification system.

Requirements:
1. Users can subscribe to different notification types (email, push, SMS)
2. Admin can send broadcast notifications
3. Users can manage their notification preferences
4. Notifications are queued and sent asynchronously
5. Failed notifications are retried

Technical specifications:
- Use Redis for queue
- Use existing email service (in utils/email.py)
- Use Firebase Cloud Messaging for push
- Use Twilio for SMS
- REST API endpoints for all operations
- WebSocket for real-time updates

Implementation steps:
1. Design the notification schema (models/)
2. Implement notification service (services/notifications/)
3. Create REST API endpoints (api/notifications/)
4. Implement queue workers (workers/)
5. Add WebSocket support (websockets/)
6. Build admin interface
7. Write comprehensive tests
8. Document API endpoints

Deliverables:
- Complete implementation with all features
- Unit tests for each component
- Integration tests for API
- API documentation in docs/api.md
- CHANGELOG.md entry

Context:
- This integrates with existing auth system
- Uses existing database models where possible
- Must be production-ready with error handling and logging
```

## 实现最佳实践

### 1. 明确需求
```
✅ 好：
Users can upload images up to 10MB. Must validate file type (JPG, PNG, GIF).
Resize to max 1920x1080. Store in S3.

❌ 差：
Implement image upload.
```

### 2. 分步实施
```
✅ 好：
Phase 1: Database schema and models
Phase 2: Storage service (S3 integration)
Phase 3: API endpoints
Phase 4: Validation and security
Phase 5: Tests and documentation

❌ 差：
Build everything at once.
```

### 3. 测试覆盖
```
✅ 好：
- Unit tests for each function
- Integration tests for API
- Edge case testing (invalid files, oversized files)
- Error handling tests

❌ 差：
Write some tests.
```

### 4. 文档完整
```
✅ 好：
- API documentation with examples
- Configuration guide
- Deployment instructions
- CHANGELOG entry with migration notes

❌ 差：
Feature done.
```

## 复杂功能的分阶段 Prompt

对于大型功能，使用多个 prompt：

### Phase 1: 设计
```
Design the architecture for [feature].

Provide:
1. Database schema
2. File structure
3. API endpoints
4. Key components and their responsibilities
5. Dependencies on existing code

Don't write code yet, just the design.
```

### Phase 2: 核心实现
```
Implement the core components based on the design.

Focus on:
- Models and database schema
- Core business logic
- Storage layer

Skip: API endpoints, tests, documentation for now.
```

### Phase 3: API 和测试
```
Implement the REST API and tests.

Based on the core implementation, add:
- API endpoints (POST/GET/PUT/DELETE)
- Input validation
- Error handling
- Unit tests
- Integration tests
```

### Phase 4: 文档和清理
```
Complete the feature:

1. Write comprehensive API documentation
2. Add code comments where needed
3. Update CHANGELOG.md
4. Add any missing tests
5. Code cleanup (formatting, naming)
```

## 集成第三方服务

```
Integrate [Service Name] for [purpose].

Requirements:
- [What the integration should do]
- [Configuration needs]
- [Error handling requirements]

Service details:
- API: [API documentation link or description]
- Authentication: [How to authenticate]
- Rate limits: [If any]
- Pricing/quotas: [If relevant]

Implementation:
1. Read the service documentation (if available locally)
2. Design the integration architecture
3. Implement the service client
4. Add configuration for API keys
5. Implement retry logic
6. Add error handling and logging
7. Write tests (use mocks for external calls)
8. Document configuration and usage
```
