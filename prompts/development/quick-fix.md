# 快速修复 Prompt Template

快速修复简单 bug 的 prompt 模板。

## 场景

- 简单的语法错误
- 明确的 bug（已知问题）
- 单文件或少文件修改
- 不影响整体架构
- 1-2小时内可完成

## Prompt 模板

### 基础模板

```
Fix the bug in [file/area]: [concise bug description]

## Bug 描述
[详细说明bug的表现和影响]

## 上下文
- 文件：[相关文件路径]
- 行号：[如果有，指出大概位置]
- 报错信息：[错误日志或截图]

## 步骤
1. Read the file and understand the issue
2. Make the fix
3. Add a test to prevent regression (如果适用)
4. Run tests to ensure nothing breaks

## 要求
- [ ] 最小改动原则（只改必要的）
- [ ] 添加错误处理（如果需要）
- [ ] 添加日志记录（关键操作）
- [ ] 添加注释说明改动原因

## 约束
- [不要引入新的依赖]
- [保持现有API不变]
- [确保向后兼容]
```

---

### 进阶模板（复杂bug修复）

```
Fix the following bug:

## Bug 描述
[详细的bug描述]

## 复现步骤
1. [步骤1]
2. [步骤2]
3. [步骤3]

## 期望结果
[应该发生什么]

## 实际结果
[实际发生了什么]

## 错误日志
[如果有，粘贴错误日志]

## 相关文件
[列出相关的文件和路径]

## 上下文
- [业务背景]
- [影响范围]
- [紧急程度]

## 要求
1. 定位根因
2. 修复bug
3. 添加防止回归的测试
4. 确保不破坏现有功能
5. 添加必要的错误处理和日志

## 验证
- [ ] 本地复现并确认修复
- [ ] 运行所有测试
- [ ] 检查相关功能的回归

When done, run:
openclaw gateway wake --text "✅ Bug修复完成：[bug名称]" --mode now
```

---

## 实际例子

### 例子 1：修复超时配置

**输入：**
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

Context: Users with slow networks are experiencing frequent timeouts.
```

**AI输出示例：**

```python
# auth_service.py (修改前)
class AuthService:
    def __init__(self):
        self.timeout = 5  # seconds
```

```python
# auth_service.py (修改后)
class AuthService:
    def __init__(self):
        # Increased from 5s to 30s to accommodate slow network connections
        # Users in remote areas frequently experienced timeouts at 5s
        self.timeout = 30  # seconds
```

---

### 例子 2：修复空指针崩溃

**输入：**
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

**AI输出示例：**

```javascript
// user_profile.js (修改前)
function renderUserProfile(user) {
  const profile = user.profile;
  return `
    <h1>${profile.name}</h1>
    <p>${profile.bio}</p>
  `;
}
```

```javascript
// user_profile.js (修改后)
function renderUserProfile(user) {
  // Handle case where profile might not exist
  if (!user || !user.profile) {
    return '<div>Profile not found</div>';
  }

  const profile = user.profile;
  return `
    <h1>${profile.name}</h1>
    <p>${profile.bio}</p>
  `;
}

// Test case
test('renderUserProfile handles undefined profile', () => {
  const result = renderUserProfile({});
  expect(result).toBe('<div>Profile not found</div>');
});
```

---

### 例子 3：修复API错误响应

**输入：**
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

**AI输出示例：**

```python
# auth/api.py (修改前)
def login(request):
    username = request.json.get('username')
    password = request.json.get('password')

    user = authenticate(username, password)
    if user:
        return jsonify({'token': user.token})
    else:
        return jsonify({'error': 'Invalid credentials'})  # BUG: Returns 200!
```

```python
# auth/api.py (修改后)
def login(request):
    username = request.json.get('username')
    password = request.json.get('password')

    user = authenticate(username, password)
    if user:
        return jsonify({'token': user.token}), 200
    else:
        # Return 401 Unauthorized for invalid credentials
        return jsonify({
            'error': 'Invalid credentials',
            'message': 'Username or password is incorrect'
        }), 401

# Test case
def test_login_invalid_credentials(client):
    response = client.post('/api/login', json={
        'username': 'wrong',
        'password': 'wrong'
    })
    assert response.status_code == 401
    assert 'Invalid credentials' in response.json['error']
```

---

## 关键原则

### 1. 最小改动原则

只修改必要的部分，不要大改。

❌ Bad: 重构整个模块
✅ Good: 只修复bug的那几行

### 2. 明确描述

说明具体的bug位置、表现和影响。

❌ Bad: "Fix the login bug"
✅ Good: "Fix login timeout: change timeout from 5s to 30s in auth_service.py:45"

### 3. 添加测试

简单测试可以防止回归。

```python
# Test the fix
def test_timeout_handling():
    service = AuthService()
    assert service.timeout == 30  # Fixed value
```

### 4. 添加注释

说明修改原因，方便后续维护。

```python
# Changed from 5s to 30s to accommodate slow network connections
# Related issue: #123
self.timeout = 30
```

---

## 实战技巧

### 技巧1：定位问题

提供尽可能多上下文：

```
## Bug 描述
用户登录时，如果密码错误，仍然返回200状态码

## 复现步骤
1. 发送POST /api/login，用户名正确，密码错误
2. 查看响应状态码

## 期望结果
返回401 Unauthorized

## 实际结果
返回200 OK

## 错误日志
2026-02-02 10:30:00 [ERROR] Login failed for user john
```

---

### 技巧2：指明文件位置

如果知道文件，直接指明：

```
Fix in auth/api.py, line 45-50
```

---

### 技巧3：提供错误信息

粘贴完整的错误栈：

```
## 错误日志
Traceback (most recent call last):
  File "auth_service.py", line 45, in login
    token = generate_token(user.id)
AttributeError: 'NoneType' object has no attribute 'id'
```

---

## 快速命令参考

```bash
# 1. 创建分支
git checkout -b fix/login-timeout

# 2. 修复bug（使用Claude Code）
claude 'Fix the login timeout bug in auth_service.py...'

# 3. 验证修复
git diff  # 查看改动
npm test  # 运行测试

# 4. 提交
git add .
git commit -m "fix: increase login timeout from 5s to 30s

- Accommodates slow network connections
- Users in remote areas no longer timeout

Fixes #123"

# 5. 推送
git push -u origin fix/login-timeout
```

---

## 常见陷阱

### 陷阱1：过度修复

❌ Bad: 修复bug时顺便重构整个模块
✅ Good: 只修复bug，重构单独提PR

---

### 陷阱2：不测试就提交

❌ Bad: 修复后直接提交
✅ Good: 本地测试+运行测试套件

---

### 陷阱3：引入新问题

✅ 检查：
- [ ] 相关功能是否受影响？
- [ ] 现有测试是否通过？
- [ ] 边界情况是否考虑？

---

## 参考资料

### 相关文档
- [03-code-development.md](../phases/03-code-development.md) - 代码开发方法论
- [bad-vs-good-prompts.md](../examples/bad-vs-good-prompts.md) - Prompt质量对比
