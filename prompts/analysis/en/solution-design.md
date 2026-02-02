# Solution Design Prompt Template

Template for generating and comparing technical solutions.

## Scenarios

- Design technical solutions for requirements
- Compare multiple technical solutions
- Architecture design
- Technology selection

## Prompt Template

### Basic Template (generate 2-3 solutions)

```markdown
Please design 2-3 technical solutions for the following requirement:

## Requirement Description
[requirement content]

## Constraints
- Technology stack: [existing stack]
- Time: [time limit]
- Resources: [resource limits]
- Performance requirements: [if any]

## Please provide:

### Solution 1: [Solution Name]
- **Overview:** Brief description of the approach
- **Architecture:** Core components and interactions
- **Technology choice:** Key technologies
- **Pros:** 3-5 advantages
- **Cons:** 3-5 disadvantages
- **Effort estimate:** Low/Medium/High
- **Major risks:** Potential issues

### Solution 2: ...

### Solution 3: ...

## Comparison Table
| Dimension | Solution 1 | Solution 2 | Solution 3 |
|-----------|-------------|-------------|-------------|
| Completeness | ✅/⚠️/❌ | ... | ... |
| Performance | Rating/5 | ... | ... |
| Dev Cost | Rating/5 | ... | ... |
| Maintenance | Rating/5 | ... | ... |
| Risk | Low/Med/High | ... | ... |

## Recommended Solution
- **Choice:** Solution X
- **Reason:** Key reasons for choosing this solution
- **Trade-offs:** What we're giving up
```

## Best Practices

### 1. Consider Multiple Dimensions

- **Functional:** Does it meet all requirements?
- **Performance:** Will it meet performance targets?
- **Scalability:** Can it handle growth?
- **Maintainability:** Is the code clean?
- **Security:** Any security concerns?
- **Cost:** Development + operational cost
- **Time:** Can we deliver on time?

### 2. Document Trade-offs

For every choice, document what you're gaining and what you're sacrificing.

## Reference

- [phases/02-solution-design.md](../../phases/02-solution-design.md) - Solution design methodology
