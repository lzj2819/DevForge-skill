---
name: devforge-threat-modeling
description: Use when a system architecture has been designed and the user needs structured threat modeling using STRIDE methodology. Trigger when user says [THREAT_MODEL] or after design-review for high-security projects.
---

# DevForge Threat Modeling

## Overview

Perform structured threat modeling using the STRIDE methodology on an approved architecture. This skill bridges the gap between design-review's adversarial inspection and security-audit's code-level scanning by producing a system-level threat matrix with mitigations and security test cases.

## VCMF Checkpoints

| Principle | Checkpoint in this Skill | Inherited from |
|-----------|--------------------------|----------------|
| Design as Contract | Every threat must map to a specific Module/Interface in architecture.xml | Inherited from architecture-design |
| Interface as Boundary | Threat model must cover all cross-module trust boundaries | Inherited from architecture-design |
| Reality as Baseline | Mitigations must be implementable with available technology | New |
| State as Responsibility | Threats involving state must reference StateModel ownership | Inherited from architecture-design |
| XML as Authority | ThreatModel output must update architecture.xml's Security/ThreatModel node | Inherited from architecture-design |

## When to Use

- User inputs `[THREAT_MODEL]`
- `design-review` completed and project has `high-security` tag
- Do NOT use if `architecture.xml` has not been approved

## Precondition Check

See `skill/tools/precondition-checker.md`. Acceptable phases: `design_review_completed`, `architecture_validated`, `module_design_completed`.
- If phase is earlier than `design_review_completed`, stop and instruct the user to complete `devforge-design-review` first.
- If `architecture.xml` is missing, stop and instruct the user to complete `devforge-architecture-design` first.

## Language Adaptation

See `skill/tools/language-adaptation.md`.

## Workflow

1. **Load architecture context**
   - Read `architecture.xml`, `PRD.md`, `INTERFACE_CONTRACT.md`, `security.xml`
   - Extract all Module IDs, Interface definitions, and trust boundaries
   - Identify external dependencies and third-party integrations

2. **STRIDE analysis per module**
   - For each Module in architecture.xml, evaluate across 6 STRIDE dimensions:
     | Dimension | Question | Key Interfaces to Review |
     |-----------|----------|------------------------|
     | Spoofing | Can an attacker impersonate this module? | Authentication interfaces |
     | Tampering | Can data flowing to/from this module be modified? | All Input/Output interfaces |
     | Repudiation | Can actions by this module be denied? | Audit logging interfaces |
     | Information Disclosure | Can sensitive data be exposed? | Interfaces handling PII/secrets |
     | DoS | Can this module be made unavailable? | Public-facing interfaces |
     | Elevation | Can privileges be escalated? | Admin/authorization interfaces |

3. **Risk rating**
   - For each identified threat, rate using Likelihood × Impact matrix:
     - Likelihood: {Likely, Possible, Unlikely} based on attack complexity
     - Impact: {High, Medium, Low} based on business impact
     - Result: {Critical, High, Medium, Low}

4. **Mitigation generation**
   - For each Critical/High threat, propose specific mitigations:
     - Technical controls (code changes, configuration)
     - Process controls (monitoring, alerting)
     - Compensating controls (if primary mitigation infeasible)

5. **Security test case generation**
   - Convert each mitigation into testable security requirements:
     ```markdown
     - **Test**: TC-SEC-{NNN}
     - **Threat**: {STRIDE category} against {module_id}
     - **Verification**: {specific test scenario}
     - **Expected Result**: {pass criteria}
     ```

6. **Output generation**
   - Write `THREAT_MODEL_REPORT.md` to `PROJECT_SCAFFOLD/docs/architecture/validation/`
   - Update `architecture.xml` — populate `Security/ThreatModel` node with findings
   - Update `security.xml` — add `Mitigations` section

7. **Self-validation**
   - Verify every threat cites a specific Module ID from architecture.xml
   - Verify every mitigation is implementable (no theoretical-only controls)
   - Verify STRIDE coverage: at least one threat identified per dimension across the system

8. **Human Gate**
   - Present threat summary: "威胁建模已完成。识别威胁 X 个（Critical Y 个，High Z 个）。"
   - List available commands:
     ```
     可用命令：
     - [APPROVE] — 标记威胁建模完成
     - [FIX {threat_id}] — 要求修复特定威胁后重新评估
     - [PAUSE] — 暂停当前阶段
     - [SKIP] — 跳过威胁建模（仅推荐用于低安全需求项目）
     ```

## State Update

See `skill/tools/state-updater.md`. This skill transitions phase to `threat_modeling_completed`.
