# Complex Refactor Prompt Template

大型重构任务的 prompt 模板。

## 场景

- 重构整个模块
- 更改架构或设计模式
- 性能优化
- 技术债务清理

## Prompt 模板

```
Refactor the [module/area] to [goal].

Current state:
- [Describe current architecture/structure]
- Problems: [list specific problems]

Target state:
- [Describe desired architecture/structure]
- Expected improvements: [performance, maintainability, etc.]

Requirements:
1. Maintain backward compatibility where possible
2. Add comprehensive tests for new structure
3. Document any breaking changes in CHANGELOG.md
4. Update dependent code as needed
5. Ensure all existing tests still pass

Constraints:
- [Any constraints: performance, security, compatibility]

After analysis, provide:
1. Proposed plan before making changes
2. Risk assessment
3. Estimated impact on existing functionality
```

## 实际例子

### 例子：重构数据访问层

```
Refactor the database access layer in models/db.py to use an ORM.

Current state:
- Direct SQL queries scattered throughout the codebase
- No type safety
- Difficult to test
- Prone to SQL injection

Target state:
- Use SQLAlchemy ORM
- Type-safe models
- Query builder pattern
- Automatic migrations

Requirements:
1. Maintain existing API endpoints unchanged
2. Add migration scripts
3. Add comprehensive unit tests
4. Update all direct SQL queries to use ORM
5. Performance should not degrade

Constraints:
- Database schema changes must be backwards compatible
- Cannot exceed current query performance (benchmark after)
- Must support PostgreSQL only

After analysis, provide:
1. Migration plan with steps
2. Risk assessment (what could go wrong)
3. Performance comparison plan
4. Rollback strategy if something breaks
```

## 关键原则

1. **先分析再行动** - 要求 Claude 先给计划
2. **明确目标和约束** - 知道要去哪里，不能做什么
3. **保持兼容性** - 尽量不破坏现有功能
4. **完整测试** - 新代码必须有测试
5. **文档化变更** - CHANGELOG、注释、README

## 分阶段策略

对于特别大的重构，分成多个阶段：

```
Phase 1: Analysis and Planning
Analyze current codebase and propose refactoring plan.

Phase 2: Core Infrastructure
Build the new ORM layer while keeping old code working.

Phase 3: Incremental Migration
Migrate module by module, testing each step.

Phase 4: Cleanup
Remove old code and finalize.
```
