# Code Review Prompt Template

Template for reviewing code.

## Scenarios

- PR review
- Code quality checks
- Security audits
- Best practices checks

## Prompt Template

```
Review this code: [diff or file]

Focus on:
- Bugs and potential issues
- Security vulnerabilities
- Performance problems
- Code style and best practices
- Test coverage

For each issue found:
1. Severity: Critical/High/Medium/Low
2. Description: Clear explanation
3. Suggested fix: Code example if possible
4. Location: File and line number

Format: Provide as a markdown report with sections for each issue.

Context:
- [Relevant context: this is payment code, user-facing, etc.]
```

## Best Practices

### 1. Be Thorough but Constructive

Focus on code quality, not personal style.

### 2. Provide Clear Feedback

For each issue:
- What's the problem?
- Why is it a problem?
- How to fix it?

### 3. Use Severity Levels

| Level | When to use |
|--------|-------------|
| Critical | Security issues, data loss risks |
| High | Bugs affecting users, performance issues |
| Medium | Code quality, maintainability |
| Low | Style improvements, nitpicks |

### 4. Include Code Examples

Show exactly what the fix should look like.

## Review Dimensions

### Security 🔒
- SQL injection
- XSS
- CSRF
- Authentication bypass
- Privilege escalation
- Sensitive data leaks

### Correctness 🐛
- Edge conditions
- Error handling
- Concurrency issues
- Data consistency
- Null/undefined handling

### Performance ⚡
- Unnecessary loops
- N+1 queries
- Memory leaks
- Missing indexes
- Inefficient algorithms

### Maintainability 📝
- Naming clarity
- Comments adequacy
- Code organization
- DRY (don't repeat)
- Testing coverage

## Output Format

```markdown
# Code Review Report

## Critical Issues (Must Fix)
### Issue #1: SQL Injection Risk
- **Severity**: Critical
- **Location**: `auth.py:45`
- **Description**: User input is directly concatenated into SQL query...
- **Fix**: Use parameterized queries:
  ```python
  cursor.execute("SELECT * FROM users WHERE id = %s", (user_id,))
  ```

## High Priority Issues
### Issue #1: Missing Error Handling
...

## Medium Priority Issues
...

## Low Priority Issues
...

## What's Done Well 🌟
- Good use of type hints
- Comprehensive test coverage
...
```

## Reference

- [phases/04-testing-validation.md](../../phases/04-testing-validation.md) - Testing methodology
