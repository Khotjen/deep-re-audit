# How Deep-Re-Audit works

Deep-Re-Audit is a protocol followed by an AI agent. The numbered stages below are a readable explanation of v2.7. The governing rules remain in [SKILL.md](../SKILL.md) and its four references.

![Detailed workflow](assets/deep-re-audit-workflow.png)

[Open the vector infographic](assets/deep-re-audit-workflow.svg) for enlargement without raster pixelation. The PNG is 2160 × 3840 pixels, in a 9:16 portrait format. The infographic summarizes the workflow; it does not replace the full protocol.

## 1. Define the question and freeze the reviewed state

Identify the target, version or commit, scope, permission boundary, and requested claim. Choose `TARGETED`, `CLOSURE`, or `RUNTIME-VERIFICATION`. State whether the requested assurance concerns a design, implementation, or production deployment.

Inventory the supplied and expected-but-missing artifacts. A snapshot or recorded hash provides identity, not an atomic lock. Recheck the current target before a verdict or authorized write. If it changed, reconcile concurrent changes and invalidate affected evidence.

## 2. Establish authority and obligations

Resolve which rules govern the target. Trace invariants, sources of truth, owners, state transitions, contracts, transactions, and dependent consumers. Agreement between supplied documents does not prove that required behavior is complete.

## 3. Run three audit passes

| Pass | Main questions | Governing reference |
| --- | --- | --- |
| Structural | Who owns state? Which contracts and invariants must hold? Are transitions, concurrency, and compatibility defined? | [Core audit protocol](../references/core-audit-protocol.md) |
| Failure/security | What happens under retries, interruption, exhaustion, abuse, or boundary crossing? Are recovery and controls adequate? | [Failure and security](../references/security-failure-and-distributed-systems.md) |
| Evidence/challenge | Can the claims be falsified? Are observations fresh, relevant, reproducible, sufficiently independent, and adequately covered? | [Evidence governance](../references/evidence-governance-and-closure.md) |

All passes contribute to one snapshot-bound finding registry. Repeating a conclusion or collecting majority votes adds no authority. Keep lifecycle, verification, causal judgment, repair disposition, re-audit outcome, and audit decision distinct.

## 4. Expand Blast Web Analysis only when material

**Blast Web Analysis (BWA)** maps material relationships among causes, shared components, controls, and affected outcomes. It can activate during any audit pass for multiple causes, fan-out/convergence, shared resources, feedback, conflicting evidence, or cross-boundary effects. A simple isolated defect can remain a small audit.

Before expanding a relationship, ask whether it could change diagnosis, severity, repair choice, closure, or verification scope. Require traceable evidence or an explicit unresolved mechanism. Prune nonmaterial speculative branches with reasons while retaining contradictory and negative evidence. A truncated material search remains partial.

Topology is not proof: a connected node is not automatically a cause, a central node is not automatically severe, and a causal finding does not grant permission to mutate anything.

## 5. Decide whether a repair is authorized

The default is **read-only**. Without authorization, report findings, proposed corrections, and limitations.

When editing is explicitly authorized and allowed by the host, select one evidenced cause or inseparable correction set. Preserve proven-working behavior unless the governing exception is evidenced. Record the affected surface, regression obligations, and rollback or forward-repair protection. Avoid fixing an expected answer or fixture instead of governing behavior.

## 6. Verify the correction and re-audit its boundaries

Absorb accepted changes into the authoritative target. Verify the requested obligations with a compatible before/after snapshot and test oracle. Inspect actual inbound and outbound boundaries of the new change, including newly introduced dependencies; the old map is only a guide.

Each post-repair comparison records exactly one outcome:

| `re_audit_outcome` | Meaning |
| --- | --- |
| `RESOLVED` | Original obligation no longer fails under adequate coverage; acceptance/regression checks pass |
| `SAME_DEFECT` | Same material defect and closure obligation remain |
| `NEW_DEFECT` | Distinct defect is discovered without evidence that the repair caused it |
| `SECONDARY_EFFECT` | Adverse effect is evidenced as introduced or exposed by the change |
| `FALSE_POSITIVE` | Adequate evidence disproves the original candidate finding or causal claim |
| `NO_PROGRESS` | Repeated equivalent attempts produce no material improvement or discriminating evidence |
| `INCONCLUSIVE` | Evidence, coverage, measurement, or intervention fidelity is insufficient |

`RESOLVED` alone does not mean `FIXED`: authoritative absorption and scope-appropriate verification must also hold. After `NO_PROGRESS`, freeze the failed path and inspect a plausible under-explored cause. Reopen it only with material new evidence, changed conditions, or a corrected oracle. Do not disguise an unresolved failure by renaming it.

## 7. Issue a scoped decision and stop

Apply this precedence to each named requested claim:

| Priority | Decision | Use |
| --- | --- | --- |
| 1 | `REJECTED` | Evidence shows the requested candidate is prohibited or untenable within its constraints and bounded correction is insufficient |
| 2 | `BASELINE_BLOCKED` | A known mandatory baseline/readiness gate failed or is missing |
| 3 | `NOT VERIFIED` | A bounded claim lacks adequate evidence and no preceding decision applies |
| 4 | One positive decision | All applicable gates pass for the named basis and scope |

Positive decisions are `CANONICAL DESIGN BASELINE`, `IMPLEMENTATION VERIFIED`, `PRODUCTION-EQUIVALENT VERIFIED`, or `PRODUCTION VERIFIED`. Only one applies to the same claim. A design result must not substitute for requested production verification.

Stop when the requested decision is supportable, remaining items cannot change it, and limitations are explicit. Assign residual risks and nonmaterial backlog without manufacturing certainty.

## What the reader receives

A bounded audit should state the reviewed target and scope, findings and supporting evidence, remaining uncertainty, recommended or authorized corrections, verification results, and a scoped decision. Larger closure work uses additional durable registers only when they improve inspectability.

The creator and optional donation notice is shown once after a human-facing audit, subject to the host/output contract and user preference. A native pop-up is possible only if the host provides a suitable notification surface; this repository implements no pop-up or payment flow.
