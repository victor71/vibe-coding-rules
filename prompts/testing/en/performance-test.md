# Performance Test Prompt Template

Template for writing performance test prompts.

## Scenarios

- API performance testing
- Load testing
- Stress testing
- Spike testing
- Soak testing

## Prompt Template

```
Write performance test scripts for the following API:

## API Definition
- **Method:** [GET/POST/PUT/DELETE]
- **Path:** /api/[endpoint]
- **Headers:** [Auth headers, etc.]

## Performance Targets
- **Response Time (p50):** < X ms
- **Response Time (p95):** < Y ms
- **Response Time (p99):** < Z ms
- **Throughput:** > X req/s
- **Error Rate:** < Y %

## Test Scenarios
1. **Baseline test** - 10 concurrent users
2. **Normal load** - 100 concurrent users
3. **Stress test** - 1000 concurrent users
4. **Spike test** - Sudden spike to 2000 users
5. **Soak test** - Sustained load for 30 minutes

## Test Tool
[k6/JMeter/Locust/Gatling]

## Requirements
1. Provide complete test script
2. Include result analysis logic
3. Generate performance report
4. Compare against targets

## Output
Provide test script and usage instructions.
```

## Example: k6 Script

```javascript
import http from 'k6/http';
import { check, sleep } from 'k6';

export const options = {
  thresholds: {
    http_req_duration: ['p(95)<200'],
    http_req_failed: ['rate<0.01'],
  },
};

export default function () {
  const response = http.get('https://api.example.com/products');

  check(response, {
    'status is 200': (r) => r.status === 200,
    'response time < 500ms': (r) => r.timings.duration < 500,
  });

  sleep(1);
}
```

## Reference

- [phases/04-testing-validation.md](../../phases/04-testing-validation.md) - Testing methodology
