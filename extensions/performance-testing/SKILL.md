---
name: performance-testing
description: Domain extension for load and performance testing. Use when the project has high-traffic, latency-sensitive, or performance-critical requirements. Loaded by devforge-test-execution when PRD contains performance tags.
---

# Performance Testing Extension

## Overview

This extension augments `devforge-test-execution` with performance-specific test generation, baseline configuration, and regression detection. It bridges the gap between functional testing and production performance validation.

## When to Load

- PRD mentions: high traffic, low latency, performance requirements, load testing
- Project characteristic tags include: `performance-critical`, `high-traffic`, `latency-sensitive`

## Overlay Rules

### Extension Outputs

| Output | Path | Description |
|--------|------|-------------|
| Load Test Scripts | `performance/k6/{scenario}.js` | k6 load test scripts |
| Artillery Configs | `performance/artillery/{scenario}.yaml` | Artillery scenario definitions |
| Baseline Config | `performance/baseline-config.yaml` | Target thresholds and load parameters |
| Regression Check | `performance/regression-check.yaml` | CI-integrated regression detection rules |
| CI Workflow | `.github/workflows/performance-regression.yml` | Automated performance regression pipeline |

### Baseline Configuration Template

```yaml
baseline:
  concurrent_users: 1000
  ramp_up: 5m
  sustained_duration: 10m
  thresholds:
    p99_latency: 200ms
    error_rate: 0.1%
    rps_per_core: 500
```

### Test Types Generated

1. **Load Test** — Verify system handles expected load
2. **Stress Test** — Find breaking point under extreme load
3. **Spike Test** — Verify behavior under sudden traffic spikes
4. **Soak Test** — Detect memory leaks and degradation over time

### Regression Detection

- Compare current run against last baseline
- Flag regression if deviation > 10% for any threshold
- Auto-fail CI pipeline on performance regression

## References

- `references/performance-testing-guide.md` — Load testing patterns and anti-patterns
