# Performance Test Prompt Template

性能测试的 prompt 模板。

## 场景

- API 性能测试
- 负载测试
- 压力测试
- Spike 测试
- 持久测试

## Prompt 模板

```
为以下 API 编写性能测试脚本：

## API 定义
- **方法：** [GET/POST/PUT/DELETE]
- **路径：** /api/[endpoint]
- **请求头：** [认证头等]

## 性能目标
- **响应时间 (p50)：** < X ms
- **响应时间 (p95)：** < Y ms
- **响应时间 (p99)：** < Z ms
- **吞吐量：** > X req/s
- **错误率：** < Y %

## 测试场景
1. **基准测试** - 10 并发用户
2. **正常负载** - 100 并发用户
3. **压力测试** - 1000 并发用户
4. **突发测试** - 突然增加到 2000 用户
5. **持久测试** - 持续负载 30 分钟

## 测试工具
[k6/JMeter/Locust/Gatling]

## 要求
1. 提供完整的测试脚本
2. 包含结果分析逻辑
3. 生成性能报告
4. 与目标值进行比较

## 输出
提供测试脚本和使用说明。
```

## 实际例子

### 例子：API 性能测试

```
为产品 API 编写性能测试脚本。

## API 定义
- **方法：** GET
- **路径：** /api/products
- **请求头：** Authorization: Bearer [token]

## 请求参数
- `page`: 页码（默认：1）
- `limit`: 每页项目数（默认：20）
- `category`: 按类别筛选（可选）

## 性能目标
- **响应时间 (p95):** < 200ms
- **响应时间 (p99):** < 500ms
- **吞吐量：** > 1000 req/s
- **错误率：** < 0.1%

## 测试场景
1. **基准测试** - 10 并发用户持续 1 分钟
2. **正常负载** - 100 并发用户持续 2 分钟
3. **压力测试** - 1000 并发用户持续 5 分钟
4. **持久测试** - 500 并发用户持续 30 分钟

## 测试工具
k6

## 要求
1. 提供完整的测试脚本
2. 包含自定义指标（响应时间、错误率、吞吐量）
3. 生成 HTML 性能报告
4. 在汇总中与目标值进行比较

## 输出
提供测试脚本和使用说明。
```

**示例输出（k6）：**

```javascript
// performance/products-api.js
import http from 'k6/http';
import { check, sleep } from 'k6';
import { Rate, Trend, Counter } from 'k6/metrics';

// Custom metrics
const errorRate = new Rate('errors');
const responseTime = new Trend('response_time');
const requestCount = new Counter('requests');

// Test configuration
export const options = {
  stages: [
    // Stage 1: Ramp up to baseline
    { duration: '30s', target: 10 },

    // Stage 2: Stay at baseline
    { duration: '1m', target: 10 },

    // Stage 3: Ramp up to normal load
    { duration: '1m', target: 100 },

    // Stage 4: Stay at normal load
    { duration: '2m', target: 100 },

    // Stage 5: Ramp up to stress
    { duration: '2m', target: 1000 },

    // Stage 6: Stay at stress
    { duration: '5m', target: 1000 },

    // Stage 7: Spike test
    { duration: '1m', target: 2000 },

    // Stage 8: Ramp down
    { duration: '2m', target: 0 },
  ],

  // Thresholds - fail test if these aren't met
  thresholds: {
    http_req_duration: ['p(95)<200', 'p(99)<500'], // Response time
    errors: ['rate<0.01'],                           // Error rate < 1%
    http_req_failed: ['rate<0.01'],                  // Failed requests < 1%
  },
};

// Default headers
const headers = {
  'Authorization': 'Bearer valid_test_token',
  'Content-Type': 'application/json',
};

export default function () {
  // Make API request with pagination
  const page = Math.floor(Math.random() * 50) + 1; // Random page 1-50
  const url = `https://api.example.com/products?page=${page}&limit=20`;

  const response = http.get(url, { headers });

  // Record metrics
  const isSuccess = check(response, {
    'status is 200': (r) => r.status === 200,
    'response time < 500ms': (r) => r.timings.duration < 500,
    'has products array': (r) => {
      const body = JSON.parse(r.body);
      return Array.isArray(body.products);
    },
    'products count <= 20': (r) => {
      const body = JSON.parse(r.body);
      return body.products.length <= 20;
    },
  });

  // Update custom metrics
  errorRate.add(!isSuccess);
  responseTime.add(response.timings.duration);
  requestCount.add(1);

  // Sleep between requests (simulate real user behavior)
  sleep(Math.random() * 2 + 1); // 1-3 seconds
}

// Handle summary - generate report
export function handleSummary(data) {
  return {
    'performance-report.json': JSON.stringify(data),
    'stdout': JSON.stringify({
      'metrics': {
        'http_req_duration': {
          'avg': data.metrics.http_req_duration.values.avg,
          'p95': data.metrics.http_req_duration.values['p(95)'],
          'p99': data.metrics.http_req_duration.values['p(99)'],
        },
        'http_reqs': data.metrics.http_reqs.values.count,
        'vus': data.metrics.vus.values.max,
      },
      'checks': {
        pass_rate: data.metrics.checks.values.passes /
                   (data.metrics.checks.values.passes + data.metrics.checks.values.fails),
      },
    }, null, 2),
  };
}
```

### 使用说明

```bash
# 运行性能测试
k6 run performance/products-api.js

# 生成 HTML 报告
k6 run --out json=results.json performance/products-api.js
k6-reporter results.json

# 运行指定阶段
k6 run --stage '{"duration":"1m","target":100}' performance/products-api.js
```

## Locust 示例

```python
# locustfile.py
from locust import HttpUser, task, between
import random

class ProductAPIUser(HttpUser):
    wait_time = between(1, 3)  # Wait 1-3 seconds between requests

    def on_start(self):
        """Called when a user starts."""
        # Login to get token
        response = self.client.post("/api/auth/login", json={
            "email": "test@example.com",
            "password": "password123"
        })
        self.token = response.json()["token"]

    @task(3)  # This task runs 3x more than others
    def get_products(self):
        """Get products list."""
        page = random.randint(1, 50)
        self.client.get(
            f"/api/products?page={page}&limit=20",
            headers={"Authorization": f"Bearer {self.token}"}
        )

    @task(1)
    def get_product_details(self):
        """Get single product details."""
        product_id = random.randint(1, 1000)
        self.client.get(
            f"/api/products/{product_id}",
            headers={"Authorization": f"Bearer {self.token}"}
        )

    @task(1)
    def search_products(self):
        """Search products."""
        query = random.choice(["laptop", "phone", "monitor", "keyboard"])
        self.client.get(
            f"/api/products/search?q={query}",
            headers={"Authorization": f"Bearer {self.token}"}
        )
```

**运行 Locust:**

```bash
# Headless mode
locust -f locustfile.py --headless -u 1000 -r 100 -t 5m

# With UI
locust -f locustfile.py --host https://api.example.com
```

## JMeter 示例

JMeter 使用 GUI 配置，这里提供关键配置说明：

```
Thread Group (Users)
├── Number of Threads: 1000
├── Ramp-Up Period: 300 seconds
└── Loop Count: Forever

HTTP Request
├── Server: api.example.com
├── Path: /api/products
├── Method: GET
└── Header Manager
    └── Authorization: Bearer ${token}

CSV Data Set Config (for pagination)
├── Filename: pages.csv
├── Variable Names: page
└── Looping: True

Listeners
├── View Results Tree (debug)
├── Aggregate Report (summary)
├── Summary Report
└── Response Times Over Time (graph)
```

## 性能测试类型

### 1. 负载测试（Load Testing）

**目标：** 验证系统在预期负载下的性能

```
Write a load test for [API].

## Load Profile
- Concurrent users: [number]
- Test duration: [time]
- Request rate: [requests per second]

## Expected Performance
- Average response time: < X ms
- 95th percentile: < Y ms
- Error rate: < Z%

## 输出
Provide complete load test script.
```

### 2. 压力测试（Stress Testing）

**目标：** 找出系统崩溃点

```
为 [API] 编写压力测试以找出崩溃点。

## 测试计划
逐渐增加负载直到：
- 响应时间显著下降
- 错误率飙升
- 系统崩溃或无响应

## 增量策略
从 [X 用户] 开始，每 [Z 秒] 增加 [Y 用户]

## 输出
提供压力测试脚本和监控计划。
```

### 3. Spike 测试

**目标：** 测试系统应对突发流量的能力

```
为 [API] 编写突发测试。

## 突发配置
- 基准：[X 用户]
- 突发：[Y 用户]（突然增加）
- 持续时间：[Z 秒]
- 恢复：回到 [X 用户]

## Expected Behavior
- System should handle spike gracefully
- No data loss
- Quick recovery after spike

## 输出
Provide spike test script.
```

### 4. 持久测试（Soak Testing）

**目标：** 测试系统长时间运行的稳定性

```
Write a soak test for [API].

## Test Configuration
- Duration: [e.g., 8 hours, 24 hours]
- Load: [X users] (normal production load)
- Monitoring: Check for memory leaks, performance degradation

## Key Metrics to Track
- Memory usage over time
- Response time trend
- Error rate over time
- CPU usage

## 输出
Provide soak test script and monitoring setup.
```

## 性能分析和优化

### Prompt: 性能分析

```
分析以下性能测试结果：

## 测试结果
[粘贴测试结果数据]

## 系统信息
- CPU: [规格]
- RAM: [容量]
- 网络：[带宽]
- 数据库：[类型, 版本]

## 要求
1. 识别瓶颈
2. 与目标值进行比较
3. 建议优化策略
4. 确定改进优先级

## 输出格式
### 性能分析

#### 总体结果
- 平均响应时间：X ms（目标：Y ms）- ✅/❌
- P95 响应时间：X ms（目标：Y ms）- ✅/❌
- 吞吐量：X req/s（目标：Y req/s）- ✅/❌
- 错误率：X%（目标：Y%）- ✅/❌

#### 瓶颈
1. [瓶颈 1]
   - 证据：[数据]
   - 影响：[严重程度]
   - 建议修复：[解决方案]

#### 优化建议
1. [Recommendation 1]
   - Expected improvement: [estimate]
   - Effort: [low/medium/high]
   - Priority: [1-5]
```

## 性能优化 Prompt

```
Optimize [component/code] for performance.

## Current Performance
- Benchmark results: [data]
- Bottlenecks identified: [list]

## Target Performance
- Response time: < X ms
- Throughput: > Y req/s
- Memory usage: < Z MB

## Constraints
- Cannot change [X]
- Must maintain [Y]
- Limited to [Z] resources

## 要求
1. Analyze the current implementation
2. Identify optimization opportunities
3. Implement improvements
4. Add benchmarks to verify
5. Maintain correctness with tests

## 输出
Provide optimized code and performance comparison.
```

## 常见性能问题

### 1. N+1 查询问题

```
优化此代码以消除 N+1 查询：

[粘贴代码]

此代码在循环中对每个项目进行数据库查询。

## 要求
1. 使用连接或子查询以更少的查询获取所有数据
2. 添加优化前后的基准测试
3. 确保结果相同
4. 如需要，更新相关测试
```

### 2. 缺少索引

```
分析并优化这个慢查询：

[SQL 查询]

## 当前性能
- 执行时间：X ms
- 返回行数：Y

## 要求
1. 使用 EXPLAIN ANALYZE 识别瓶颈
2. 建议合适的索引
3. 提供优化的查询
4. 展示性能改进
```

### 3. 内存泄漏

```
Debug memory leak in [component].

## Symptoms
- Memory usage grows over time
- Eventually crashes with OOM

## 要求
1. Identify the source of memory leak
2. Fix the leak
3. Add memory monitoring
4. Verify with a soak test
```

## 性能测试检查清单

```
性能测试检查清单：

测试设计：
□ 测试目标明确定义
□ 模拟真实工作负载
□ 定义性能目标
□ 测试环境匹配生产环境（尽可能）
□ 测试数据具有代表性

测试执行：
□ 包含预热期
□ 测试运行足够长时间以达到稳定状态
□ 多次运行以获得统计显著性
□ 系统监控就位
□ 结果已捕获和存储

分析：
□ 与基准进行比较
□ 识别瓶颈
□ 记录发现
确定改进优先级
□ 与团队分享结果

优化：
□ 首先解决主要瓶颈
□ 优化前后基准测试
□ 回归测试
□ 生产部署计划
```

## 工具对比

| 特性 | k6 | Locust | JMeter | Gatling |
|------|-----|--------|--------|---------|
| **脚本语言** | JavaScript | Python | GUI + Java DSL | Scala |
| **学习曲线** | 🟢 简单 | 🟢 简单 | 🟡 中等 | 🔴 复杂 |
| **分布式测试** | ✅ 需要配置 | ✅ 原生支持 | ✅ 原生支持 | ✅ 原生支持 |
| **实时监控** | ✅ | ✅ UI | ✅ UI | ✅ |
| **报告** | ✅ JSON/HTML | ✅ | ✅ | ✅ HTML |
| **资源占用** | 低 | 低 | 高 | 低 |
| **CI/CD 集成** | ✅ | ✅ | ✅ | ✅ |
| **最适用场景** | API 负载测试 | 复杂用户行为 | 通用 | 高并发性能测试 |
