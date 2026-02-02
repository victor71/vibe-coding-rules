# New Feature Prompt Template

Template for implementing new features.

## Scenarios

- Implement new features
- Add new API endpoints
- Integrate new services
- Add UI components

## Prompt Template

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

## Best Practices

### 1. Clear Requirements

```
✅ Good:
Users can upload images up to 10MB. Must validate file type (JPG, PNG, GIF).
Resize to max 1920x1080. Store in S3.

❌ Bad:
Implement image upload.
```

### 2. Step-by-Step Implementation

```
✅ Good:
Phase 1: Database schema and models
Phase 2: Storage service (S3 integration)
Phase 3: API endpoints
Phase 4: Validation and security
Phase 5: Tests and documentation

❌ Bad:
Build everything at once.
```

### 3. Test Coverage

```
✅ Good:
- Unit tests for each function
- Integration tests for API
- Edge case testing (invalid files, oversized files)
- Error handling tests

❌ Bad:
Write some tests.
```

### 4. Complete Documentation

```
✅ Good:
- API documentation with examples
- Configuration guide
- Deployment instructions
- CHANGELOG entry with migration notes

❌ Bad:
Feature done.
```

## Phased Prompts for Complex Features

For large features, use multiple prompts:

### Phase 1: Design
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

### Phase 2: Core Implementation
```
Implement the core components based on the design.

Focus on:
- Models and database schema
- Core business logic
- Storage layer

Skip: API endpoints, tests, documentation for now.
```

### Phase 3: API and Tests
```
Implement the REST API and tests.

Based on the core implementation, add:
- API endpoints (POST/GET/PUT/DELETE)
- Input validation
- Error handling
- Unit tests
- Integration tests
```

### Phase 4: Documentation and Cleanup
```
Complete the feature:

1. Write comprehensive API documentation
2. Add code comments where needed
3. Update CHANGELOG.md
4. Add any missing tests
5. Code cleanup (formatting, naming)
```
