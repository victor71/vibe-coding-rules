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
修复 [文件/区域] 中的 bug：[简洁的bug描述]

## Bug 描述
[详细说明bug的表现和影响]

## 上下文
- 文件：[相关文件路径]
- 行号：[如果有，指出大概位置]
- 报错信息：[错误日志或截图]

## 步骤
1. 读取文件并理解问题
2. 进行修复
3. 添加测试以防止回归（如果适用）
4. 运行测试确保不破坏现有功能

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
修复以下bug：

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

完成后，运行：
openclaw gateway wake --text "✅ Bug修复完成：[bug名称]" --mode now
```

---

## 实际例子

### 例子 1：修复超时配置

**输入：**
```
修复 auth_service.py 中的登录超时问题。

超时时间当前设置为5秒，但对于慢速网络连接应该设置为30秒。

步骤：
1. 读取 auth_service.py
2. 找到超时配置
3. 从5改为30秒
4. 添加注释说明为什么30秒是合适的
5. 这是配置更改，不需要测试

背景：网络较慢的用户频繁遇到超时问题。
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
        # 从5秒增加到30秒，以适应慢速网络连接
        # 远程地区的用户在5秒时经常超时
        self.timeout = 30  # seconds
```

---

### 例子 2：修复空指针崩溃

**输入：**
```
修复 user_profile.js 中的空指针崩溃问题。

当 user.profile 未定义时，应用在第45行崩溃。

步骤：
1. 读取 user_profile.js 并找到问题
2. 在访问 profile 属性前添加空值检查
3. 添加一个 profile 未定义的测试用例
4. 运行所有测试确保不破坏现有功能

背景：这对用户注册流程很关键。
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
  // 处理 profile 不存在的情况
  if (!user || !user.profile) {
    return '<div>Profile not found</div>';
  }

  const profile = user.profile;
  return `
    <h1>${profile.name}</h1>
    <p>${profile.bio}</p>
  `;
}

// 测试用例
test('renderUserProfile handles undefined profile', () => {
  const result = renderUserProfile({});
  expect(result).toBe('<div>Profile not found</div>');
});
```

---

### 例子 3：修复API错误响应

**输入：**
```
修复 auth/api.py 中的错误响应问题。

当用户提供无效凭据时，API返回200 OK
而不是401 Unauthorized。

步骤：
1. 在 auth/api.py 中找到登录端点
2. 检查无效凭据的响应状态码
3. 将其从200改为返回401
4. 添加适当的错误消息
5. 为无效凭据情况添加测试

背景：这是安全问题 - 无效凭据不应返回200。
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
        return jsonify({'error': 'Invalid credentials'})  # BUG: 返回200!
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
        # 为无效凭据返回401 Unauthorized
        return jsonify({
            'error': 'Invalid credentials',
            'message': 'Username or password is incorrect'
        }), 401

# 测试用例
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

❌ Bad: "Fix login bug"
✅ Good: "Fix login timeout: change timeout from 5s to 30s in auth_service.py:45"

### 3. 添加测试

简单测试可以防止回归。

```python
# 测试修复
def test_timeout_handling():
    service = AuthService()
    assert service.timeout == 30  # 修复后的值
```

### 4. 添加注释

说明修改原因，方便后续维护。

```python
# 从5秒改为30秒以适应慢速网络连接
# 相关issue: #123
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
修复位置：auth/api.py, 第45-50行
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
claude 'Fix login timeout bug in auth_service.py...'

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
