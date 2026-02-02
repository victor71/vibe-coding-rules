# Optimization Prompt Template

Template for performance optimization.

## Scenarios

- Database query optimization
- Algorithm optimization
- Caching strategies
- Reduce memory usage
- Speed up slow endpoints

## Prompt Template

```
Optimize the [component/area] for [goal: speed/memory/throughput].

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

## Optimization Strategies

### Database Optimization

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

### Algorithm Optimization

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

### Caching Strategies

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

## Common Optimization Patterns

### 1. Batch Operations

```
Instead of:
for item in items:
    save_item(item)  # Individual DB calls

Use:
save_items(items)  # Single batch operation
```

### 2. Lazy Loading

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

### 3. Parallel Processing

```
Instead of:
for item in items:
    process(item)  # Sequential

Use:
with ThreadPoolExecutor() as executor:
    executor.map(process, items)  # Parallel
```

### 4. Precomputation

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
