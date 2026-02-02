# Requirement Clarification Prompt Template

Template for clarifying and refining requirements.

## Scenarios

- Requirements are vague or incomplete
- Need more info from PM/users
- Need to identify assumptions and risks
- Need to convert requirements to technical specs

## Prompt Template

### Basic Template

```markdown
I received a requirement but it's incomplete. Please help analyze and generate clarification questions.

## Requirement Description
[paste original requirement]

## Known Information
[if any, list known info, constraints, background]

## Please analyze:
1. Vague or unclear parts of the requirement
2. Missing key information
3. Potential assumption risks
4. Suggested questions to ask (prioritized: Critical/High/Medium/Low)

For each question, explain:
- Why we need to ask
- Consequences of not asking
- How to validate the answers
```

### Advanced Template (for complex requirements)

```markdown
Please help clarify the following requirement:

## Requirement Description
[requirement content]

## Background
- [Business context]
- [User roles]
- [Usage scenarios]

## Please provide:
1. Requirement completeness check
   - What key information is missing?
   - Any contradictions or inconsistencies?

2. Assumption identification
   - Hidden assumptions in the requirement
   - Which assumptions need verification?

3. Clarification question list
   - Critical (must ask): Wrong direction if not asked
   - High (should ask): Rework if not asked
   - Medium (nice to ask): Optimize details
   - Low (optional): Edge cases

4. Requirement categorization (MoSCoW)
   - Must have: Core functionality
   - Should have: Important but can be delayed
   - Could have: Nice to have
   - Won't have: Explicitly not doing

5. Acceptance criteria suggestions
   - How to judge requirement is done?
   - How to test?
```

## Best Practices

### 1. Be Specific

**❌ Bad:** "Fix the bug"
**✅ Good:** "Fix login timeout: change timeout from 5s to 30s in auth_service.py:45"

### 2. Prioritize Questions

- **Critical:** Without this, we build the wrong thing
- **High:** Without this, we may need to redo work
- **Medium:** Optimizes details
- **Low:** Edge cases and nice-to-haves

## Reference

- [phases/01-requirement-analysis.md](../../phases/01-requirement-analysis.md) - Requirement analysis methodology
