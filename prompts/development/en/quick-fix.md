# Quick Fix Prompt Template

Template for fixing simple bugs.

## Scenarios

- Simple syntax errors
- Known/明确的 bugs
- Single file or minimal changes
- Doesn't affect overall architecture
- Can be completed in 1-2 hours

## Prompt Template

```
Fix the bug in [file/area]: [concise bug description]

Steps:
1. Read the file and understand the issue
2. Make the fix
3. Add a test to prevent regression
4. Run tests to ensure nothing breaks

Context:
- [Any relevant context or constraints]
```

## Examples

### Example 1: Fix login timeout

```
Fix the login timeout issue in auth_service.py.

The timeout is currently set to 5 seconds but should be 30 seconds
for slow network connections.

Steps:
1. Read auth_service.py
2. Find the timeout configuration
3. Change it from 5 to 30 seconds
4. Add a comment explaining why 30s is appropriate
5. No tests needed, this is a configuration change
```

### Example 2: Fix null pointer crash

```
Fix the null pointer crash in user_profile.js.

When user.profile is undefined, the app crashes at line 45.

Steps:
1. Read user_profile.js and find the issue
2. Add null check before accessing profile properties
3. Add a test case with undefined profile
4. Run all tests to ensure nothing breaks

Context: This is critical for user onboarding.
```

### Example 3: Fix API error response

```
Fix the error response in auth/api.py.

When user provides invalid credentials, the API returns 200 OK
instead of 401 Unauthorized.

Steps:
1. Find the login endpoint in auth/api.py
2. Check the response status for invalid credentials
3. Change it to return 401 instead of 200
4. Add appropriate error message
5. Add test for invalid credentials case

Context: This is a security issue - invalid credentials should not return 200.
```

## Best Practices

### 1. Minimal Changes

Only modify what's necessary, don't make big changes.

❌ Bad: Refactor the entire module
✅ Good: Only fix the bug lines

### 2. Clear Description

Explain the specific bug location, symptoms, and impact.

❌ Bad: "Fix login bug"
✅ Good: "Fix login timeout: change timeout from 5s to 30s in auth_service.py:45"

### 3. Add Tests

Simple tests can prevent regression.

```python
# Test fix
def test_timeout_handling():
    service = AuthService()
    assert service.timeout == 30  # Fixed value
```

### 4. Add Comments

Explain the reason for changes for future maintenance.

```python
# Changed from 5s to 30s to accommodate slow network connections
# Related issue: #123
self.timeout = 30
```

---

## Quick Reference

```bash
# 1. Create branch
git checkout -b fix/login-timeout

# 2. Fix the bug (using Claude Code)
claude 'Fix login timeout bug in auth_service.py...'

# 3. Verify the fix
git diff  # View changes
npm test  # Run tests

# 4. Commit
git add .
git commit -m "fix: increase login timeout from 5s to 30s

- Accommodates slow network connections
- Users in remote areas no longer timeout

Fixes #123"

# 5. Push
git push -u origin fix/login-timeout
```
