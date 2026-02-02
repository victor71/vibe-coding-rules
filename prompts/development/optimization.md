# Optimization Prompt Template

性能优化的 prompt 模板。

## 场景

- 数据库查询优化
- 算法优化
- 缓存策略
- 减少内存使用
- 加速慢接口

## Prompt 模板

```
Optimize [component/area] for [goal: speed/memory/throughput].

Current performance:
- [Benchmark results or description of slowness]
- [Specific bottlenecks if known]

Target:
- [Desired performance metrics]

Context:
- [How this component is used]
- [Constraints: can't change DB schema, must maintain API compatibility, etc.]

Steps:
1. Profile and identify bottlenecks
2. Propose optimization strategy
3. Implement changes
4. Add benchmarks to verify improvement
5. Ensure correctness with existing tests

Requirements:
- Maintain backward compatibility
- Don't sacrifice correctness for speed
- Add comments explaining optimizations
```

## 实际例子

### 例子 1：优化慢 API 响应

```
Optimize the /api/users endpoint. Currently takes 5-10 seconds for large datasets.

Current performance:
- Response time: 5-10s for 1000+ users
- Database queries: 1000+ queries (N+1 problem)
- Memory usage: High due to loading all data

Target:
- Response time: <1s for 1000+ users
- Database queries: <10 queries
- Memory usage: Reduced by streaming results

Context:
- This endpoint is used by the admin dashboard
- Returns paginated user list with profile data
- Currently loads all users, then filters

Requirements:
- Keep the same API contract
- Maintain pagination
- Don't break existing filters

Steps:
1. Read the current implementation (api/users.py)
2. Identify the N+1 query problem
3. Optimize using JOINs or subqueries
4. Add database indexes if needed
5. Stream results instead of loading all
6. Add benchmarks before/after
7. Ensure tests still pass
```

### 例子 2：减少内存使用

```
Optimize the image processing service for memory usage.

Current performance:
- Processing 100 images: 2GB RAM usage
- Frequent OOM errors with large batches
- Images loaded entirely into memory

Target:
- Processing 100 images: <500MB RAM
- Handle batches of 500+ images
- Process images in chunks or streaming

Context:
- Service processes uploaded images
- Applies resize, watermark, compression
- Currently loads full image into memory
- Uses PIL (Pillow)

Requirements:
- Maintain image quality
- Keep the same API
- Support async processing

Steps:
1. Profile memory usage with memory_profiler
2. Identify memory leaks or allocations
3. Process images in chunks or streams
4. Reuse image buffers
5. Add memory monitoring
6. Add benchmarks for memory usage
7. Ensure visual quality is maintained
```

## 优化策略

### 数据库优化

```
Optimize database queries in [area].

Check for:
- N+1 query problems
- Missing indexes
- Inefficient JOINs
- Unnecessary data fetching
- Suboptimal WHERE clauses

After optimization, add:
- EXPLAIN ANALYZE output before/after
- Index usage statistics
- Query time benchmarks
```

### 算法优化

```
Optimize the [algorithm/function] for better performance.

Current implementation: [describe]
Time complexity: [if known]

Target:
- Improve time complexity if possible
- Reduce constant factors
- Consider caching results if applicable

After optimization:
- Add benchmarks comparing before/after
- Document the algorithm improvement
- Ensure correctness with edge cases
```

### 缓存策略

```
Add caching to [component] to improve performance.

Current performance: [describe slowness]
Cache targets: [what should be cached]

Requirements:
- Cache invalidation strategy
- TTL (time-to-live) for different data
- Cache hit/miss metrics
- Fallback when cache fails

Implement:
1. Choose caching solution (Redis, in-memory, etc.)
2. Cache key design
3. Invalidation logic
4. Fallback handling
5. Metrics and monitoring
6. Tests for cache behavior
```

## 性能分析 Prompt

### 1. 识别瓶颈

```
Profile [component] to identify performance bottlenecks.

Use:
- cProfile or py-spy for Python
- Chrome DevTools for JavaScript
- flamegraphs if available

Provide:
1. Top 5 slow functions/methods
2. Memory usage patterns
3. I/O bottlenecks (DB, network, disk)
4. Suggested optimization priorities
```

### 2. 基准测试

```
Create benchmarks for [component].

Define:
- Key performance metrics (latency, throughput, memory)
- Test scenarios (small, medium, large datasets)
- Baseline performance

Implement:
1. Benchmark suite using [pytest-benchmark, benchmark.js, etc.]
2. Multiple test cases
3. Statistical significance (run multiple iterations)

Output:
- Baseline metrics
- Benchmark results in markdown table
- Charts if possible
```

## 常见优化模式

### 1. 批量操作
```
Instead of:
for item in items:
    save_item(item)  # Individual DB calls

Use:
save_items(items)  # Single batch operation
```

### 2. 惰性加载
```
Instead of:
def get_data():
    data = heavy_computation()
    return data

Use:
def get_data():
    if not hasattr(get_data, '_cached'):
        get_data._cached = heavy_computation()
    return get_data._cached
```

### 3. 并行处理
```
Instead of:
for item in items:
    process(item)  # Sequential

Use:
with ThreadPoolExecutor() as executor:
    executor.map(process, items)  # Parallel
```

### 4. 预计算
```
Instead of computing on every request:
def get_report():
    data = compute_heavy_report()
    return data

Use:
# Precompute and cache
def get_report():
    return load_from_cache() or compute_heavy_report()
```

## 优化检查清单

```
Review the code for optimization opportunities:

Algorithm:
□ Can we improve time complexity?
□ Can we reduce constant factors?
□ Are we doing unnecessary work?

Database:
□ Do we have N+1 queries?
□ Are indexes being used?
□ Can we reduce the data fetched?

Memory:
□ Are we holding references unnecessarily?
□ Can we process in streams instead of loading all?
□ Are there memory leaks?

Caching:
□ Can we cache expensive computations?
□ Can we cache database results?
□ Is the cache invalidated correctly?

I/O:
□ Are we making redundant network calls?
□ Can we batch operations?
□ Can we use asynchronous I/O?
```
