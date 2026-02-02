# Architecture Review Prompt Template

Template for reviewing and improving system architecture.

## Scenarios

- Review existing system architecture
- Identify architecture issues and risks
- Propose architecture improvements
- Evaluate architecture design solutions

## Prompt Template

### Basic Template (architecture review)

```markdown
Please review the following system architecture:

## Architecture Description
[architecture overview, components, interactions]

## System Background
- Business domain: [industry/scenario]
- User scale: [DAU/concurrency]
- Data scale: [data volume]
- Technology stack: [tech stack]

## Review Dimensions

Please review from the following dimensions:

### 1. Architecture Rationality
- Are component responsibilities clear?
- Any circular dependencies?
- Is module division reasonable?
- Does it follow single responsibility principle?

### 2. Scalability
- How to handle user growth?
- How to handle feature growth?
- Horizontal/vertical scaling capability?
- Need for decoupling?

### 3. Reliability
- Single point of failure?
- Fault tolerance mechanisms?
- Degradation strategies?
- Monitoring and alerting?

### 4. Performance
- Bottlenecks?
- Caching strategy?
- Database optimization?
- Async processing?

### 5. Security
- Authentication/authorization?
- Data encryption?
- Input validation?
- Dependency security?

### 6. Maintainability
- Code organization?
- Documentation?
- Testing coverage?
- Deployment process?

### 7. Cost
- Infrastructure cost?
- Operational cost?
- Optimization opportunities?

## Output Format

### Score Summary
- Overall Architecture Score: X/10
- Critical Issues (must fix): N
- High Priority Issues: N

### Critical Issues (Must Fix)
### Issue #1: [Issue Name]
- **Severity:** Critical
- **Impact:** What's at risk
- **Fix:** Recommended solution

### High Priority Issues
### Issue #1: [Issue Name]
- **Severity:** High
- **Impact:** Potential problems
- **Fix:** Suggested improvements

### Improvements
1. [Improvement 1]
2. [Improvement 2]

### What's Done Well
- [Good aspects to keep]
```

## Reference

- [phases/02-solution-design.md](../../phases/02-solution-design.md) - Solution design methodology
