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
Write performance test scripts for the products API.

## API Definition
- **Method:** GET
- **Path:** /api/products
- **Headers:** Authorization: Bearer [token]

## Request Parameters
- `page`: Page number (default: 1)
- `limit`: Items per page (default: 20)
- `category`: Filter by category (optional)

## Performance Targets
- **Response Time (p95):** < 200ms
- **Response Time (p99):** < 500ms
- **Throughput:** > 1000 req/s
- **Error Rate:** < 0.1%

## Test Scenarios
1. **Baseline test** - 10 concurrent users for 1 minute
2. **Normal load** - 100 concurrent users for 2 minutes
3. **Stress test** - 1000 concurrent users for 5 minutes
4. **Sustained test** - 500 concurrent users for 30 minutes

## Test Tool
k6

## Requirements
1. Provide complete test script
2. Include custom metrics (response time, error rate, throughput)
3. Generate HTML performance report
4. Compare against targets in summary

## Output
Provide test script and usage instructions.
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

## Output
Provide complete load test script.
```

### 2. 压力测试（Stress Testing）

**目标：** 找出系统崩溃点

```
Write a stress test to find the breaking point of [API].

## Test Plan
Gradually increase load until:
- Response time degrades significantly
- Error rate spikes
- System crashes or becomes unresponsive

## Ramp Up Strategy
Start at [X users], increase by [Y users] every [Z seconds]

## Output
Provide stress test script and monitoring plan.
```

### 3. Spike 测试

**目标：** 测试系统应对突发流量的能力

```
Write a spike test for [API].

## Spike Profile
- Baseline: [X users]
- Spike: [Y users] (sudden increase)
- Duration: [Z seconds]
- Recovery: Back to [X users]

## Expected Behavior
- System should handle spike gracefully
- No data loss
- Quick recovery after spike

## Output
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

## Output
Provide soak test script and monitoring setup.
```

## 性能分析和优化

### Prompt: 性能分析

```
Analyze the following performance test results:

## Test Results
[粘贴测试结果数据]

## System Info
- CPU: [specifications]
- RAM: [amount]
- Network: [bandwidth]
- Database: [type, version]

## Requirements
1. Identify bottlenecks
2. Compare against targets
3. Suggest optimization strategies
4. Prioritize improvements

## Output Format
### Performance Analysis

#### Overall Results
- Average response time: X ms (Target: Y ms) - ✅/❌
- P95 response time: X ms (Target: Y ms) - ✅/❌
- Throughput: X req/s (Target: Y req/s) - ✅/❌
- Error rate: X% (Target: Y%) - ✅/❌

#### Bottlenecks
1. [Bottleneck 1]
   - Evidence: [data]
   - Impact: [severity]
   - Suggested fix: [solution]

#### Optimization Recommendations
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

## Requirements
1. Analyze the current implementation
2. Identify optimization opportunities
3. Implement improvements
4. Add benchmarks to verify
5. Maintain correctness with tests

## Output
Provide optimized code and performance comparison.
```

## 常见性能问题

### 1. N+1 查询问题

```
Optimize this code to eliminate N+1 queries:

[粘贴代码]

The code is making a database query for each item in a loop.

## Requirements
1. Use joins or subqueries to fetch all data in fewer queries
2. Add benchmarks before and after
3. Ensure the result is the same
4. Update related tests if needed
```

### 2. 缺少索引

```
Analyze and optimize this slow query:

[SQL 查询]

## Current Performance
- Execution time: X ms
- Rows returned: Y

## Requirements
1. Use EXPLAIN ANALYZE to identify bottlenecks
2. Suggest appropriate indexes
3. Provide optimized query
4. Show performance improvement
```

### 3. 内存泄漏

```
Debug memory leak in [component].

## Symptoms
- Memory usage grows over time
- Eventually crashes with OOM

## Requirements
1. Identify the source of memory leak
2. Fix the leak
3. Add memory monitoring
4. Verify with a soak test
```

## 性能测试检查清单

```
Performance Test Checklist:

Test Design:
□ Test objectives clearly defined
□ Realistic workload simulated
□ Performance targets defined
□ Test environment matches production (as much as possible)
□ Test data is representative

Test Execution:
□ Warm-up period included
□ Tests run long enough to reach steady state
□ Multiple runs for statistical significance
□ System monitoring in place
□ Results captured and stored

Analysis:
□ Compare against baseline
□ Identify bottlenecks
□ Document findings
□ Prioritize improvements
□ Share results with team

Optimization:
□ Top bottlenecks addressed first
□ Before/after benchmarks
□ Regression testing
□ Production deployment plan
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
