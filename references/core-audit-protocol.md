# Core Audit Protocol

Use this reference during Pass 1 or when a structural/cross-document defect is suspected.

Navigation: scope/registry (§1–1A); authority and invariants (§2–5); execution/contracts (§6–10); diagnosis/severity (§11–11A); BWA/blast (§12); correction and rediscovery (§13–14); output (§15–16).

## 1. Scope and Inventory

Record the audited object, objective, reviewed files/versions, excluded or unavailable material, expected authority sources, and the closure mode. For TARGETED mode, a lightweight reviewed-set list is enough. For CLOSURE mode, use a reproducible manifest and hashes/digests when available.

Before analysis, record the exact input versions or digests and use a stable snapshot where available. Recheck the authoritative target identity/version before issuing a verdict and immediately before an authorized write; a recorded hash alone is not a lock. Use an atomic expected-version check for replacement where supported. If the target changed, reconcile the changes, invalidate only affected evidence, and re-audit the affected dependency cone before claiming the current target is verified. Preserve unrelated concurrent edits; do not overwrite a version conflict. If stability or a write guard cannot be established, disclose the limitation and keep affected current-state claims `NOT VERIFIED`; do not present a pre-write check as an atomic guarantee. For intended audit corrections, record the new version and verify that revised snapshot.

Do not infer completeness from the number of files supplied. Identify expected-but-missing artifacts separately.

**Consistency does not prove completeness.** Derive expected obligations from actors, invariants, lifecycle states, failure modes, data flows, external dependencies, and stated goals; flag material requirements that are absent even when all supplied documents agree.

### 1A. One Canonical Registry Across Passes

Use one audit-local finding registry for the frozen snapshot. DRA owns its reconciliation; each pass adds evidence, challenges a claim, or refines the same records. A later pass has no automatic precedence and repeating an earlier conclusion adds no independent evidence.

Retain stable finding IDs. Merge only when the violated rule, causal mechanism, affected boundary, remediation owner, and closure obligation are materially the same; preserve all locations and provenance. Shared symptoms or a common dependency alone do not justify merging. Group related findings under a common cause without losing distinct owners or verification duties. On split, retain the original ID as a group/reference and link the new distinct records; never silently close the original. Preserve unresolved disagreement as uncertainty and, for causal claims, `CONTESTED` under evidence governance §3A.

Keep decision fields distinct: `finding_status`, `verification_state`, `causal_state`, `repair_disposition`, `re_audit_outcome`, and `audit_decision`. An edge's `REJECTED` does not reject the audit; an accepted repair does not by itself fix a finding. Read legacy labels such as Status/Disposition/Decision only when their subject is explicit; emit the namespaced fields in new records. Never guess the subject of an unqualified status.

`finding_status` is lifecycle only: OPEN (unresolved), REMEDIATION_PROPOSED (not applied), PARTIALLY_FIXED (some obligations unmet), FIXED (absorbed and verified for the declared scope), ACCEPTED_RISK (valid evidence §17 acceptance, not a fix), or DEFERRED (unresolved backlog). Use `verification_state`: VERIFIED, NOT_VERIFIED, or CONTESTED for the evidentiary state of the finding/claim. An OPEN finding may be VERIFIED as a defect; an OPEN hypothesis may be NOT_VERIFIED. Legacy `finding_status=NOT_VERIFIED` remains readable for backward compatibility but must be normalized on write to lifecycle OPEN (or omitted when no finding lifecycle applies) plus `verification_state=NOT_VERIFIED`. Reopen affected records when proof is invalidated or acceptance expires; preserve prior states and reasons. Deferral never satisfies a mandatory gate. A disproved hypothesis does not silently close the underlying observed defect.

If a proposed defect is adequately disproved and no violated rule or observed defect remains, retain its ID as a reference to the explicitly REJECTED causal/assumption record; document the evidence and reclassification. Omit inapplicable finding_status rather than inventing FIXED or leaving a false defect OPEN. This does not discharge any separate observed-defect or evidence-gap obligation.

## 2. Authority Review

Resolve authority at the **rule level**, not only the document level.

Use this authority order: system/platform safety -> Host/runtime and applicable non-bypassable legal constraints -> explicit authorized user/Owner decisions -> invariants/canonical decisions -> contracts -> module specs -> examples -> superseded/unverified material. Verify the applicability of any claimed external constraint; do not invent it. A changed authorized decision requires explicit absorption and re-audit of dependent rules, not an implicit override. Audit findings and graph topology never grant mutation permission.

Check:

- duplicate rules with different wording or behavior;
- one term used for different concepts;
- multiple terms for the same canonical concept;
- local/module rules that silently override invariants;
- examples or old reports treated as authority;
- superseded material re-entering decisions;
- external standards treated as mandatory without explicit adoption.

## 3. Invariant Review

For each material invariant identify:

- invariant ID and statement;
- owning authority;
- protected data/business property;
- enforcement layer;
- allowed exceptions;
- failure consequence;
- test/evidence obligation.

Typical invariants include uniqueness, balanced accounting, nonnegative/controlled inventory, single-consumption approvals, event immutability, tenant isolation, idempotent command identity, and monotonic state constraints.

## 4. Source of Truth

For each critical state define:

- authoritative source;
- derived projections/caches;
- writer(s);
- reader(s);
- rebuild/reconciliation path;
- consistency model;
- recovery behavior.

A projection, cache, analytics model, AI narrative, or dashboard must not silently become mutation authority.

## 5. Ownership and Mutation Boundaries

For each module/domain define:

- owned state;
- permitted commands;
- emitted events;
- consumed events;
- legal mutation path;
- forbidden direct writes;
- cross-module contract.

Two independent mutation authorities must not own the same state unless the conflict-resolution protocol is explicit and proven.

## 6. State Machines

For every material lifecycle define:

- states;
- legal/illegal transitions;
- transition owner;
- preconditions/guards;
- side effects;
- durable state change;
- retry behavior;
- timeout/cancel behavior;
- terminal/reversal/compensation behavior;
- recovery from interrupted transitions.

Check stale version, duplicate transition, replay, reordered event, and repeated approval.

## 7. Transactions, Concurrency, and Idempotency

For each high-risk flow define:

- atomic boundary;
- isolation/concurrency rule;
- idempotency identity and retention window;
- duplicate request behavior;
- lost-response behavior after commit;
- retry owner and retry budget;
- external side-effect ordering;
- compensation/reconciliation;
- exactly-once claims and their real enforcement.

Do not equate "exactly once" messaging with exactly-once business effect unless the end-to-end invariant proves it.

## 8. Database Enforcement

Check whether critical invariants rely only on application code when the database can safely enforce them.

Review as relevant:

- primary/foreign/unique/check constraints;
- tenant/branch keying;
- row-level security or equivalent isolation;
- transaction isolation;
- optimistic/pessimistic concurrency controls;
- durable idempotency/approval-consumption records;
- immutable/auditable ledger semantics;
- migration compatibility and rollback.

## 9. Cross-Document Contract Comparison

Build a conflict matrix only for material dimensions, such as:

- naming and identifiers;
- ownership;
- states/transitions;
- API/event schemas;
- transaction boundaries;
- security/tenant rules;
- error/retry semantics;
- version compatibility;
- recovery ownership.

Prefer one canonical rule over parallel synonyms. If multiple public aliases must remain for backward compatibility, define one canonical internal meaning and explicit adapters.

## 10. Business Logic Review

Verify business rules are deterministic, auditable, reversible where required, and consistent across money, inventory, tax, payment, approvals, refunds, discounts, payroll, and settlement when those domains are in scope.

Do not treat a mathematically valid formula as correct if its ownership, rounding, timing, versioning, or reversal semantics are ambiguous.

## 11. Diagnosis Before Remediation

Separate diagnosis from the proposed fix. Do not let an existing implementation, preferred redesign, or suggested patch define the root cause. First state the failing invariant/requirement, trigger, and causal path; then compare the smallest viable remediations. This reduces anchoring and sunk-cost bias.

When practical, record initial findings from requirements, authoritative rules, constraints, and primary evidence before reading previous auditors' conclusions or preferred fixes; then reconcile with earlier findings without dropping unresolved issues or changing stable IDs. Never withhold governing context or safety constraints to simulate a blind review. If prior conclusions have already been seen, acknowledge that exposure and use an adversarial counterexample pass; do not claim clean-slate review. This ordering reduces anchoring but does not prove independence, and using a different model alone does not establish independent evidence. Keep this review method separate from audit scope; it does not require another agent or a new register.

### 11A. Canonical Severity Policy

Classify a finding against the requested scope/claim, separately from evidence strength, blast tier, correction risk, and acceptance. Record the violated rule, credible trigger, reachability, consequence, containment, and recoverability in `severity_reason`. A credible path has inspected evidence of applicable preconditions/mechanism, not merely an imagined outcome. A demonstrated contradiction in a design is itself evidence of implementation ambiguity; it does not require a production incident.

Identify critical required behavior and mandatory gates from the target's adopted requirements. If those classifications or acceptance criteria are unavailable, record the uncertainty; do not invent universal loss, load, sampling or recovery thresholds.

Apply the first matching gate:

| Severity | Gate |
| --- | --- |
| `BLOCKER` | A demonstrated breach of a non-bypassable constraint; or a credible reachable path to critical integrity/security/safety loss or critical outage without adequate containment/recovery; or missing/conflicting authority, invariant, or deterministic semantics that prevents safe implementation of a critical required behavior. |
| `MAJOR` | A credible material defect, exposure, or required verification gap that can change correctness, security, availability, compatibility, or readiness, but does not meet a BLOCKER gate. |
| `MINOR` | A demonstrated bounded defect with no material effect on the requested decision or critical behavior. |
| `ENHANCEMENT` | No violated requirement/invariant or credible defect; an optional improvement. |

If reachability/impact is unresolved, record the conditional severity and its assumptions as provisional/`NOT VERIFIED`; do not assert a demonstrated defect or inflate severity from a theoretical catastrophe. A proven excluded precondition cannot justify that failure path. Missing evidence for a mandatory decision gate still blocks that decision, irrespective of the provisional severity. Risk acceptance does not lower severity. A verified test result also does not prove low impact outside its coverage.

## 12. Root Cause and Blast Radius

Useful **system/root-cause** classes:

- authority ambiguity;
- missing/contradictory invariant;
- duplicate ownership;
- incomplete state machine;
- weak database enforcement;
- transaction-boundary defect;
- unsafe retry/idempotency model;
- missing failure semantics;
- inconsistent terminology;
- hidden dependency;
- security/trust-boundary defect;
- missing recovery ownership;
- backward-compatibility gap.

Keep **assurance/evidence failures** separate: stale or mismatched evidence, invalid oracle, insufficient coverage, provenance gaps, or incompatible observation windows. These can block or weaken an audit decision but are not causal roots of the audited system failure unless separate evidence establishes such a mechanism.

Assess blast radius only across materially coupled areas. Avoid performative checklists for unrelated modules.

### Blast Web Analysis (BWA)

Activate BWA when material evidence or a plausible unresolved mechanism indicates multiple causes, a shared resource, fan-out, convergence, cross-boundary propagation, retry/resource feedback, contradictory causal evidence, or multiple findings involving the same component. Do not wait until a linear explanation has already failed; omit BWA for an isolated deterministic defect with no such indication.

Maintain one **bounded canonical BWA map per audit snapshot**, with multiple finding/seed IDs and filtered per-finding views. Canonicalize shared entities once so cross-finding convergence and common causes remain visible. This is an audit-local analytical representation, not a shared mutable execution graph. BWA extends this section; DRA still owns findings, severity, blast classification, verdicts, and remediation analysis/disposition. Tool use, mutation, and other side effects remain exclusively under authorized Host/Owner control. In CWE, consume version-bound evidence as a read-only projection; CWE Host retains tool/side-effect authority and Planner retains execution strategy. Never treat Planner conclusions as verified causal evidence.

**Canonical entities**

- **Node** — stable node ID, canonical entity key, type, instance and state, with snapshot binding. Resolve aliases from identity evidence; include material tenant/environment/region/instance distinctions. Similar names do not prove identity. Preserve unresolved identity rather than merging different instances or versions.
- **Edge** — stable edge ID, endpoints, relation, `causal_state`, evidence references, applicable conditions, causal role, effect, and temporal semantics. Unknown values stay explicit; absent causal role never means necessary or sufficient.
- **Gate** — stable `gate_id`, kind (`ALL`, `ANY`, or `SEQUENCE`), ordered/input references, output/consequence reference, applicable conditions/window, completeness, `causal_state`, and evidence references. Gate identity is snapshot-bound; changing membership, order, conditions, or output creates a successor gate version rather than silently mutating prior evidence.
- **Path** — stable `path_id` within the snapshot, `seed_id`/claim reference, ordered node/edge/gate references, conditions/window, weakest material causal state, and coverage state. A path is an evidence-bearing view of a route, not an independent source of causality.
- **Impact assignment** — `seed_id`, claim/finding ID, `node_id`, `path_id`, boundary assignment reference, graph distance, and DRA blast tier with rationale/evidence. One shared node may have different evidenced effects for different seeds; do not merge their impact assignments.
- **Boundary assignment** — stable assignment of `IN_SCOPE`, `OUT_OF_SCOPE`, or `UNKNOWN` to a seed/claim/path at a named node or interface. Scope is decision-relative: the same canonical node may be in scope for one seed and out of scope for another. Never use one global node boundary to satisfy another seed's coverage/closure claim.

**Minimum relation vocabulary and traversal policy**

| Relation | Structural/dependency view | Causal propagation | Hypothesis search | May directly justify repair |
| --- | --- | --- | --- | --- |
| `CAUSES` | yes | yes | yes | only with adequate evidence |
| `ENABLES` | yes | yes when evidence supports the mechanism | yes | conditional |
| `AMPLIFIES` | yes | yes | yes | often containment candidate, not automatically root cause |
| `MITIGATES` | yes | yes as a control effect | yes | control/barrier candidate |
| `DEPENDS_ON` | yes | no by itself | yes | no |
| `SHARES_RESOURCE` | yes | no by itself | yes | no |
| `CORRELATED` | no causal inference | no | yes | never |

Do not traverse every edge type for every analysis. A dependency or shared-resource edge may justify investigation of a causal path, but it must not be silently promoted into causation.

**Materiality admission and relevance pruning**

Before an edge/path expands the active BWA search, ask whether it can plausibly change at least one decision-relevant dimension: diagnosis/causal discrimination, severity or blast assignment, mandatory closure/coverage, correction choice, or verification scope. Require either traceable compatible evidence or an explicit unresolved mechanism tied to an observed symptom/finding. Topology, naming similarity, proximity, or generic possibility alone is insufficient.

A bounded `HYPOTHESIS` may be retained for investigation, but branches that become nonmaterial, duplicative, incompatible with the snapshot, or unable to change the requested decision are pruned from **active traversal** with a reason. Pruning is not deletion: preserve provenance, contradictory/negative evidence, and reopen conditions so the same branch is not rediscovered repeatedly. Never prune merely because evidence is inconvenient or because a preferred hypothesis has stronger support. Hitting a search limit remains `GRAPH_TRUNCATED`/`PARTIAL`, not relevance pruning.

**Evidence/epistemic state**

Use `causal_state`: `HYPOTHESIS`, `SUPPORTED`, `VERIFIED`, `CONTESTED`, or `REJECTED`. Apply the single promotion/demotion policy in evidence governance §3A; traversal does not promote evidence. A causal-path claim must state its weakest material link, gate/path identity, seed/claim scope, temporal compatibility, and coverage/conditions. Hypothesis-only or contested paths support investigation, not verified propagation or repair authority. A separately evidenced defect or safe authorized containment can justify action without pretending its root cause is verified.

**Causal role and effect**

For `CAUSES`, use `CONTRIBUTORY` unless stronger `NECESSARY` or `SUFFICIENT` semantics are demonstrated for the stated context; even a contributory claim needs evidence. NECESSARY means the effect requires that input in the declared context; SUFFICIENT means that input with the fixed stated preconditions entails the effect, not merely that the effect is possible. A cause can be both necessary and sufficient only with evidence for each. `ENABLES`, `AMPLIFIES`, and `MITIGATES` keep their relation-specific roles. Record `effect`: `PROMOTES`, `INHIBITS`, `BLOCKS`, or `UNKNOWN`, relative to a named state/quantity and conditions. `BLOCKS` describes a proved unavailable path under those conditions, not merely an attempted protection. Do not infer causal role/effect from naming or topology.

**Competing and hidden-cause hypotheses**

For a material unexplained effect, retain plausible competing hypotheses long enough for evidence to discriminate them. If A and B are associated but direct causation is not supported, allow a `COMMON_CAUSE_CANDIDATE` / unknown confounder rather than forcing `A -> B` or `B -> A`. Unknown means unknown; do not invent the hidden node.

**Temporal and compound causation**

Use `ALL` when all inputs are required, `ANY` when one input is sufficient, and `SEQUENCE` when order is part of the failure condition. Bind the gate's input set, preconditions, observation window, and completeness to evidence. Do not treat an unenumerated ANY input as absent or a claimed ALL condition as proved necessary. When time/order matters, record `before`, `after`, `concurrent`, or `unknown` with delay/window where relevant; ordering alone does not prove causation.

**Fan-out, convergence, and cycles**

- High fan-out identifies a propagation surface, not severity by itself.
- High convergence identifies a shared dependency or choke point, not a repair target by itself.
- Mark a graph cycle as `CYCLE_CANDIDATE`. Classify a `REINFORCING_FAILURE_LOOP` only with evidenced effect direction on every material link, relevant timing/delay, and observed net harmful amplification. Positive polarity alone does not prove harmful amplification; a balancing/mitigating loop needs observed net reduction of the named deviation. Unknown polarity, missing dynamics, or contradictory evidence leaves a candidate; even inhibitory feedback can oscillate or become unstable with delay. Do not classify dynamic behavior from SCC detection or sign counting alone.
- Distinguish `CONVERGENCE_POINT`, `CONTAINMENT_CANDIDATE`, `REPAIR_CANDIDATE`, and causal/root factor. They are not synonyms.

**Blast assignment**

DRA assigns blast tier from credible impact scope, authority/business boundaries, affected users/data/services, reversibility, and recovery implications. Graph distance is stored separately. A one-hop edge may cross to a high blast tier; a long internal chain may remain low blast.

Use an explicitly adopted target blast policy when present, recording its version. Otherwise use this DRA policy; never mix two tier meanings in one audit:

| Tier | Evidenced propagation scope |
| --- | --- |
| `B1` | One bounded entity/component; no material propagation outside it. |
| `B2` | Multiple flows/modules inside one ownership and isolation boundary. |
| `B3` | Multiple authority domains/services or shared data/control within the audited system. |
| `B4` | Propagation across tenant, trust, organizational, or external system boundaries. |
| `B5` | Systemic cascading impact across multiple systems/organizations or critical shared infrastructure. |

Choose the highest justified scope, with trigger/path and recovery rationale. Treat unproved continuations as candidate tiers/`NOT VERIFIED`, not as confirmed impact; missing coverage is not B1. These are impact-scope categories, not severity scores or permission grants.

**Bounded expansion**

Prefer the smallest dependency cone that can change the audit decision. Apply practical limits to nodes, edges, depth, hypothesis expansion, or external-boundary exploration. Hitting a limit must yield `GRAPH_TRUNCATED` / `PARTIAL` coverage, never an implicit PASS or claim that no further path exists.

**Containment and intervention**

A containment point may be preferable to changing a causal node, but it is not automatically the root cause. Use the role-aware intervention procedure in evidence governance §5A, including alternate sufficient causes, intervention fidelity, and measurement adequacy. Recompute and rediscover boundaries under §14; do not reject a contributory cause merely because another ANY input keeps the effect present.

For each material finding, distinguish within the existing finding record:

- **Defect impact:** the credible consequences if the defect remains, with trigger, causal path, affected dependencies, and evidence or uncertainty.
- **Correction risk:** the credible consequences introduced by the proposed change, including compatibility, state/data migration, concurrent or in-flight work, and recovery where relevant. Specify the smallest sufficient regression evidence and rollback, compensation, or forward-repair approach; do not imply that reverting code reverses committed data or external effects.

Use `N/A` with a reason for irrelevant dimensions and `NOT VERIFIED` for missing proof. Do not force a five-level cascade, speculate about unrelated impacts, promise zero disruption, or duplicate the finding register.

## 13. Correction

Correct the highest authoritative layer that is actually wrong, then propagate to affected downstream artifacts.

Examples:

- Wrong invariant -> fix invariant, then contracts/tests/docs that depend on it.
- Correct invariant but wrong example -> fix the example only.
- Correct design but defective implementation -> fix implementation/tests, not the design merely to match the bug.

Preserve working behavior and compatibility when possible. Broad rewrites require evidence that a bounded correction is insufficient.

Apply a **stable-surface hard guard** before mutation. A proven-working component, contract, behavior, or path is outside the repair set unless at least one of these is evidenced: (a) it materially contributes to the defect; (b) an authoritative upstream correction necessarily requires a compatible downstream change; or (c) it violates a mandatory correctness/security/integrity invariant. Convenience, cleanup, stylistic consistency, graph centrality, or “while we are here” are not sufficient. When the guard is crossed, record why the change is necessary, the smallest affected surface, regression obligations, and rollback/compensation/forward-repair protection.

Converge toward **sparse repair**: one root cause or one inseparable dependency cluster, one minimal change set, then attributable verification. Do not bundle unrelated findings merely to reduce iteration count. Before acceptance, perform a leakage check: the correction must repair the authoritative rule, invariant, contract, or causal path rather than recognize the current test name, fixture, expected value, benchmark, or example.

When the user explicitly authorizes edits, treat mutation as part of the audit evidence chain:

- freeze the exact target files, versions, and scope before editing;
- patch the existing authoritative files rather than creating parallel replacements;
- preserve filenames, Library identity, repository path, or other durable identity when available;
- change only the affected rule set and its necessary downstream references;
- do not add research, new documents, or auxiliary reports unless they are required for inspectability or the user asked for them;
- if a file is split because it is too large to audit safely, keep a canonical index/source-of-truth map and remove ambiguity about which file governs which rule;
- run a post-edit challenge pass over the changed dependency cone before calling a finding closed.

A written correction proposal is `REMEDIATION_PROPOSED`; an applied document edit is document-level absorption only. It is not implementation or production verification.

## 14. Regression Re-Audit

Re-check the changed dependency cone for:

- authority conflicts;
- invariant breakage;
- compatibility regressions;
- tenant/security regressions;
- concurrency/order/idempotency defects;
- recovery defects;
- accounting/inventory/payment divergence;
- migration/rollback damage;
- stale evidence.

For BWA, create a successor snapshot after repair and retain the previous map as historical evidence. Recompute changed nodes/relations/gates and rediscover inbound and outbound boundaries from the actual new diff, contracts, dependencies, configuration, state changes, and trace evidence. The old graph is an initial guide, not the full search space. Inspect at least the immediate boundary in both directions; expand new material paths until a justified stable boundary is reached or declared limits apply. Revalidate shared nodes, cross-finding paths, controls, and seed impact assignments touched by the change; explicit equivalence is required to reuse unaffected evidence. `GRAPH_TRUNCATED`/unknown material boundaries remain partial and block claims that depend on their completeness. Record the residual graph, unresolved paths, and newly introduced risks.

Stable finding IDs are preferred. A new ID must not hide an unresolved prior defect.

Classify each post-repair result with exactly one `re_audit_outcome`, without replacing the existing namespaced states:

- `RESOLVED` — the original defect/obligation is no longer reproduced and its declared verification/regression obligations pass for scope.
- `SAME_DEFECT` — the same finding identity and materially same failure mechanism/closure obligation persist. Preserve the ID; do not disguise it as a new finding.
- `NEW_DEFECT` — a distinct defect with a different violated rule/mechanism or closure obligation is discovered and is not evidenced as caused by the repair. Create/link a new finding only when the core §1A split criteria are met.
- `SECONDARY_EFFECT` — a materially adverse effect is evidenced as introduced or exposed by the repair/change. Link it to the repair attempt and correction risk; do not count it as successful convergence.
- `FALSE_POSITIVE` — the original candidate finding/causal claim is disproved by adequate evidence, corrected oracle, or invalidated observation. Preserve the historical record; do not apply a repair to make the false signal disappear.
- `NO_PROGRESS` — repeated attempts in the same hypothesis/change class produce no decision-relevant improvement beyond the applicable noise floor or no new discriminating evidence. Freeze that path and inspect a plausible under-explored cause; do not repeat the same change class without a valid reopen condition.
- `INCONCLUSIVE` — evidence, intervention fidelity, coverage, or measurement is insufficient to choose another outcome.

`RESOLVED` does not by itself mean `finding_status=FIXED`; canonical absorption and requested-scope verification still apply. `SAME_DEFECT`, `SECONDARY_EFFECT`, `NO_PROGRESS`, and `INCONCLUSIVE` cannot be reported as convergence. Evidence governance §8A defines the admission tests for these labels.

Where removal is authorized and evidence supports it, identify obsolete fallbacks, duplicate validation, dead guards, redundant retries, unused abstractions, and expired compatibility shims introduced by earlier repairs. Remove them only as a separate, attributable change with regression evidence; otherwise record them as bounded follow-up work.

## 15. Determinism and Implementer Test

Ask whether two competent implementers using the same material would derive materially equivalent behavior.

If a second independent reviewer/agent is unavailable, perform an adversarial alternate-interpretation pass and mark true independence as `NOT VERIFIED`; do not pretend independence occurred.

## 16. Output Profiles

### TARGETED

Only produce registers that directly help the requested question. Keep the core answer compact.

### CLOSURE

Material registers may include:

- document/provenance manifest;
- canonical authority/conflict register;
- findings register;
- invariant/source-of-truth/ownership matrices;
- material state-machine and transaction-flow registers;
- failure/security/recovery register;
- assumption/open-decision/deferred-evidence registers;
- correction absorption and regression record;
- final change log and closure statement.

Do not split into many files merely to satisfy a template. Split only when it improves inspectability or auditability.
