# Complex Refactor Prompt Template

Template for large refactoring tasks.

## Scenarios

- Refactor entire modules
- Change architecture or design patterns
- Performance optimization
- Technical debt cleanup

## Prompt Template

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

## Best Practices

1. **Analyze before action** - Require Claude to provide a plan first
2. **Clear goals and constraints** - Know where you're going, what you can't do
3. **Maintain compatibility** - Try not to break existing functionality
4. **Complete testing** - New code must have tests
5. **Document changes** - CHANGELOG, comments, README

## Phased Strategy

For especially large refactors, split into multiple phases:

```
Phase 1: Analysis and Planning
Analyze current codebase and propose refactoring plan.

Phase 2: Core Infrastructure
Build new structure while keeping old code working.

Phase 3: Incremental Migration
Migrate module by module, testing each step.

Phase 4: Cleanup
Remove old code and finalize.
```
