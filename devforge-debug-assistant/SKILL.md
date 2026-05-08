---
name: devforge-debug-assistant
description: Use when tests are failing, logs show anomalies, or the user wants code-level improvements. Provides bug diagnosis, root cause analysis, and refactoring suggestions. Trigger when user says [DEBUG] or "fix this bug" or "refactor this code".
---

# DevForge Debug Assistant

## Overview

Analyze failing tests, error logs, production incidents, or existing code to provide actionable bug fixes, refactoring suggestions, and incident reports. Supports three modes: bug diagnosis (Mode A), refactoring (Mode B), and production issue diagnosis (Mode C).

## VCMF Checkpoints

| Principle | Checkpoint in this Skill | Inherited from |
|-----------|--------------------------|----------------|
| Design as Contract | Bug fixes must not violate `INTERFACE_CONTRACT.md` or `component-spec.xml` | New |
| Interface as Boundary | Refactoring must preserve all public interfaces unless explicitly approved | New |
| Reality as Baseline | Diagnosis must be based on actual test output / logs, not speculation | New |
| State as Responsibility | Bug fixes involving state must respect `StateModel` ownership | New |
| XML as Authority | Fixes must update `component-spec.xml` if the XML spec itself is found to be the source of the bug | New |

## When to Use

- **Mode A (Bug Diagnosis)**: Tests are failing and the user needs diagnosis + fix
- **Mode B (Refactoring)**: The user wants to improve code quality
- **Mode C (Production Issue Diagnosis)**: Production logs show anomalies, alerts are firing, or user reports "production issue" / "incident" / "outage"
- The user types `[DEBUG]` or says "fix this bug" / "refactor this" / "investigate incident"
- Do NOT use if no code exists yet (use `devforge-project-scaffolding` first)

## Precondition Check

See `skill/tools/precondition-checker.md`. Acceptable phases: `scaffolding_completed`, `module_design_completed`, `iteration_planning_completed`, `test_execution_completed`.
- If phase is earlier than `scaffolding_completed`, stop and instruct the user to complete `devforge-project-scaffolding` first.
- If no code exists, stop and instruct the user to complete `devforge-project-scaffolding` first.

## Language Adaptation

See `skill/tools/language-adaptation.md`.

## Context Loading Protocol

- Follow `references/context-management-protocol.md` for artifact loading priority
- **Targeted loading**: Only load `component-spec.xml` for the affected component(s), not all modules
- **Repo index utilization**: If `repo-index.md` exists, use it to locate relevant files quickly instead of scanning the full directory tree
- **Log truncation**: For large logs, load only the last 200 lines plus the first 50 lines (head + tail pattern)
- **Module registry digest**: Use `STATE.md` Module Registry `digest` field for quick module identification without loading full module XMLs

## Workflow

### Mode A: Bug Diagnosis and Fix

1. **Collect evidence**
   - Read failing test output (from CI logs or local test run)
   - Read relevant source code files
   - Read `component-spec.xml` for the affected component
   - Read error stack traces and log excerpts
   - Read `docs/architecture/validation/TEST_REPORT.md` (if entering from test-execution)

2. **Root cause analysis**
   - Trace the failure from symptom to cause:
     - Test assertion failure → what value was expected vs actual?
     - Exception → which line threw? What was the input?
     - Timeout → which dependency was slow? Is there a retry mechanism?
   - Check against common categories:
     - Logic error (wrong condition, off-by-one)
     - State error (race condition, uninitialized state)
     - Interface mismatch (schema changed but consumer not updated)
     - Dependency failure (mock not set up, external service down)
     - XML divergence (code signature differs from `component-spec.xml`)

3. **Propose fix**
   - Provide the minimal code change to fix the bug
   - Include inline comment explaining the root cause
   - Verify the fix does not break other tests
   - If the fix requires interface changes, flag as breaking and warn user

4. **Generate debug report**
   - Output: `PROJECT_SCAFFOLD/docs/architecture/validation/DEBUG_REPORT.md`
   - Contents:
     - Symptom summary
     - Root cause (with code references)
     - Proposed fix (diff format)
     - Regression risk assessment
     - Verification steps (how to confirm the fix)

### Mode B: Refactoring Suggestion

1. **Code health scan**
   - Read source code of the target module/component
   - Read `component-spec.xml` and `INTERFACE_CONTRACT.md`
   - Read `DESIGN_REVIEW.md` for known issues

2. **Identify improvement opportunities**
   - Check for code smells:
     - Long function (> 50 lines)
     - Deep nesting (> 3 levels)
     - Duplicated logic
     - Magic numbers/strings
     - Tight coupling (direct dependency on concrete class)
     - Missing error handling paths
   - Check for architecture alignment:
     - Does code match `component-spec.xml` signatures?
     - Does state management match `StateModel`?
     - Are interfaces using the correct schemas?

3. **Propose refactorings**
   - For each issue, provide:
     - Location (file:line)
     - Problem description
     - Refactoring strategy (extract method, introduce interface, etc.)
     - Before/after code snippet
     - Risk level (low / medium / high — high = may affect behavior)

4. **Generate refactor report**
   - Output: `PROJECT_SCAFFOLD/docs/architecture/validation/REFACTOR_REPORT.md`
   - Contents:
     - Issue list with severity (Must Fix / Should Fix / Nice to Fix)
     - Before/after snippets
     - Risk assessment per refactoring
     - Suggested execution order (low risk first)

### Mode C: Production Issue Diagnosis

1. **Collect production evidence**
   - Read production logs (error logs, access logs, application logs)
   - Read monitoring metrics (CPU, memory, latency, error rate from Prometheus/Grafana/Datadog)
   - Read alert notifications (PagerDuty, OpsGenie, etc.)
   - Read distributed traces (Jaeger, Zipkin, AWS X-Ray) if available
   - Read `PROJECT_SCAFFOLD/docs/architecture/system/STATE.md` for module registry and known pitfalls
   - **Note**: Test output is NOT required for Mode C — production logs and metrics are the primary input

2. **Correlate symptoms with system architecture**
   - Map log anomalies to specific modules/components using `component-spec.xml`
   - Identify which module's interfaces are involved in the error path
   - Check if the issue matches any known pitfalls in `STATE.md`
   - Correlate timestamp across logs, metrics, and traces

3. **Root cause analysis**
   - Production-specific categories:
     - Resource exhaustion (OOM, connection pool depletion, thread starvation)
     - Cascade failure (downstream service timeout propagating upstream)
     - Deployment-related (config mismatch, rolling update issue, feature flag)
     - Data corruption (inconsistent state, migration error)
     - External dependency failure (third-party API, cloud service)
     - Traffic pattern anomaly (DDoS, thundering herd, unexpected spike)
   - Use the 5 Whys method to trace from symptom to root cause

4. **Generate production incident report**
   - Output: `PROJECT_SCAFFOLD/docs/architecture/validation/PRODUCTION_INCIDENT_REPORT.md`
   - Contents:
     - Incident summary (what, when, impact)
     - Symptom timeline (chronological log of events)
     - Root cause (with evidence references)
     - Affected components and modules
     - Immediate mitigation (if any)
     - Long-term fix proposal
     - Prevention recommendations

<HARD-GATE>
Do NOT apply refactoring changes or close a debug session until the user explicitly approves the fix strategy. In Mode A (Bug Diagnosis), the root cause analysis must be confirmed before generating the fix. In Mode B (Refactoring), the refactoring plan must be approved before applying changes. In Mode C (Production Issue Diagnosis), the incident timeline and root cause must be confirmed before proposing fixes.
</HARD-GATE>

5. **Human gate**
   - Present summary based on active mode:
     - Mode A: "Bug 诊断报告已生成。"
     - Mode B: "重构报告已生成。"
     - Mode C: "生产事故分析报告已生成。"
   - Then list all available commands:
     ```
     可用命令：
     - [APPROVE FIX] — 应用修复方案（Mode A/C）
     - [APPROVE REFACTOR] — 应用重构方案（Mode B）
     - [SPECIFIC {issue_id}] — 只处理特定问题
     - [PAUSE] — 暂停当前阶段，保留上下文
     - [ROLLBACK {step_id}] — 回滚到指定步骤重新执行
     - [EDIT {file_path}] — 手动编辑文件后让 AI 继续
     - [INJECT {context}] — 补充额外上下文约束
     - [EXPLAIN {TraceID}] — 展开解释某个决策/错误的推理链
     ```
   - If user inputs natural language feedback (e.g., "这里需要修改"), treat as modification request — do NOT treat as invalid command; analyze the feedback, apply changes, and re-present the gate with updated output
   - Do NOT proceed until [APPROVE FIX], [APPROVE REFACTOR], [SPECIFIC], or explicit continue command

## Output Specification

- `PROJECT_SCAFFOLD/docs/architecture/validation/DEBUG_REPORT.md` (for Mode A: bug diagnosis)
- `PROJECT_SCAFFOLD/docs/architecture/validation/REFACTOR_REPORT.md` (for Mode B: refactoring)
- `PROJECT_SCAFFOLD/docs/architecture/validation/PRODUCTION_INCIDENT_REPORT.md` (for Mode C: production issue diagnosis)

## Red Flags

- Do NOT propose fixes that violate `INTERFACE_CONTRACT.md` without explicit user approval
- Do NOT refactor without preserving public interface contracts
- Do NOT diagnose based on speculation; always reference actual test output, logs, or monitoring metrics
- Do NOT ignore production context in Mode C; correlate logs with architecture and known pitfalls
- Do NOT skip the human gate before applying changes
