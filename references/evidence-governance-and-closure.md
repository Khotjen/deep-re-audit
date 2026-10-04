# Evidence, Governance, and Closure

Use this reference during Pass 3, baseline closure, or any audit that makes readiness claims.

Navigation: provenance/levels (§1–1A); freshness, conflict and causal states (§2–3B); assumptions/traceability/intervention (§4–5A); correction/coverage/convergence (§6–8A); closure/decisions (§9–13A); evidence quality and repair ledger (§14–15C); research/risk acceptance (§16–17).

## 1. Evidence Provenance Record

For each material evidence item record as available:

- Evidence ID;
- audit snapshot ID and target identity;
- claim(s) supported;
- evidence level E0-E5;
- source/artifact;
- version/commit/digest/document revision;
- environment and relevant configuration;
- timestamp/freshness window;
- command/procedure;
- expected result;
- actual result;
- log/artifact reference;
- limitations and known blind spots;
- invalidation triggers;
- BWA node/edge/path IDs supported, when applicable;
- coverage state (`COMPLETE`, `PARTIAL`, `SAMPLED`, or `UNKNOWN`) and relevant retention/sampling/detection assumptions.

Prefer evidence that another reviewer can reproduce or independently inspect. When BWA is active, every material causal edge must resolve to one or more evidence records with sufficient provenance; an edge label alone is not evidence.

### 1A. Canonical Evidence Levels

Use these operational observation classes; they are DRA policy, not an external certification scale. Classify the actual item, not its intended use:

| `evidence_level` | Observed basis | What it does not establish |
| --- | --- | --- |
| `E0` | Assertion, hypothesis, narrative, or reference whose underlying artifact was not inspected. | A verified fact or causal claim. |
| `E1` | Inspected governing document, source code, configuration, static analysis, or checkable derivation/counterexample. | Executed implementation or deployment behavior. |
| `E2` | Defined test/proof obligation with inputs/preconditions, procedure, oracle and acceptance criterion, not executed. | A passing result. |
| `E3` | Executed controlled test/check of implementation behavior with exact target/configuration, expected and observed result, and inspectable artifacts. | Untested hostile/failure paths or production equivalence. |
| `E4` | Executed relevant integration, concurrency, adversarial, fault-injection, recovery or rollback scenario with a valid oracle and controlled conditions. | Every failure mode, generalization, or production deployment. |
| `E5` | Inspected operational evidence from production or a justified production-equivalent environment, bound to deployment/configuration, workload and observation window. | Adequate coverage, causal discrimination, or production readiness merely from its origin. |

A static checker's execution remains E1 for a runtime-behavior claim; executing a tool is not executing the claimed behavior. Split a planned test from its executed result. Missing provenance/coverage cannot be repaired by a high label: describe the observation origin, mark the affected claim `NOT VERIFIED`, and do not let it satisfy a gate. E4/E5 may satisfy an execution obligation only when their actual content covers that exact claim. E5 never cancels a contradicting relevant E1/E4 result. No averaging, automatic promotion, or numerical `E >= n` shortcut.

## 2. Evidence Freshness and Invalidation

A passing result is evidence for the version/configuration it actually tested.

Revalidate when a relevant change affects:

- code in the dependency cone;
- schema/migration;
- contracts/events/APIs;
- security/authorization policy;
- runtime/dependency version;
- deployment configuration;
- test fixture or test oracle;
- environment assumptions.

Do not rerun everything automatically. Use impact analysis to select the smallest sufficient regression scope.

## 3. Conflicting Evidence

When evidence conflicts:

1. verify identity/version/environment;
2. verify the claim each item actually supports;
3. inspect test oracle and data/fixture validity;
4. prefer direct, reproducible, higher-fidelity evidence for the same claim;
5. preserve contradictory evidence in the record until reconciled.

Do not select the convenient result merely because it enables closure.

## 3A. BWA Snapshot, Provenance, and Contradiction

A BWA map is valid only for the identity and temporal state it actually describes. Bind material nodes, edges, gates, paths, boundary assignments, and evidence to the audit target/snapshot as available:

- audit/target identity;
- version, commit, digest, document revision, or deployment identity;
- environment/configuration;
- state epoch or material lifecycle phase when relevant (for example before/after failover, migration, leader change, rollback, cache rebuild, or reconciliation);
- observation time window and ordering relation;
- evidence source and collection method;
- coverage/sampling/retention limitations.

Do not silently merge evidence from incompatible target versions **or incompatible state epochs/time windows** into one causal claim. The same version/configuration observed across materially different runtime epochs is not automatically one compatible snapshot. Mark mismatched material as `STALE_EVIDENCE`, `CROSS_VERSION_EVIDENCE`, or `CROSS_EPOCH_EVIDENCE` as appropriate and use it only for historical comparison or an explicitly reasoned cross-snapshot hypothesis.

Use `causal_state` for edge/hypothesis claims. DRA reconciles the state across all passes using this policy:

| State | Admission / transition rule |
| --- | --- |
| `HYPOTHESIS` | A plausible mechanism or proposed role; no discriminating proof yet. Planner/model/prior-auditor narratives start here, not as facts. |
| `SUPPORTED` | Relevant compatible evidence supports the mechanism, but material alternatives, role, or coverage remain unresolved. Repetition/correlation alone is insufficient. |
| `VERIFIED` | Inspectable provenance, compatible snapshot, correct oracle and adequate coverage establish the exact mechanism, role, preconditions and bounded claim through a checkable proof or discriminating observation. Record competing explanations and why they do not defeat that claim; proving a contributory cause does not require excluding every other cause. |
| `CONTESTED` | Material relevant evidence conflicts and identity/oracle/coverage reconciliation has not resolved it. Preserve both sides. Majority vote or later-pass precedence cannot resolve it. |
| `REJECTED` | Adequate relevant evidence falsifies the precise bounded claim. Reject that claim/role, not every weaker or adjacent claim. Missing telemetry alone is insufficient. |

Promote only when the next state's conditions hold; direct HYPOTHESIS-to-VERIFIED is allowed only with the full verification record. On changed target or invalidated proof, withdraw current verification to SUPPORTED/HYPOTHESIS as warranted; staleness alone is not a contradiction or disproof. Resolve CONTESTED to SUPPORTED/VERIFIED/REJECTED only with documented reconciliation. Reopen a rejected claim only on material new evidence, changed conditions, or a corrected oracle; preserve its historical rejection. Never remove inconvenient evidence.

Negative telemetry means `NOT_OBSERVED` unless the evidence record establishes adequate coverage, sensitivity, retention, sampling, and opportunity to observe. Graph coverage and epistemic state are separate fields.

If the graph crosses scope or an unavailable dependency, preserve an `OUT_OF_SCOPE` or `UNKNOWN` boundary node/path. The strongest permitted negative conclusion is limited to the observed boundary (for example, “no downstream impact verified within the audited boundary”), not a global absence claim.

## 3B. Baseline Variation and Noise Floor

Calibrate a noise floor before claiming improvement when results are stochastic, timing-sensitive, sampled, environment-dependent, model-dependent, or historically variable. Use repeated baseline observations sufficient for the stakes and record the distribution/range, environment, sample size, and acceptance threshold. A change within expected variation is `NOT VERIFIED improvement`; it may still be accepted for another independently proven benefit.

Do not force repeated runs onto deterministic checks with stable exact outputs unless flakiness or environmental variation is observed. Do not use a wide noise band to conceal a regression.

## 4. Assumption Register

Material assumptions should include:

- ID and statement;
- why it is needed;
- affected scope;
- risk if false;
- evidence/confirmation owner;
- expiry/reevaluation trigger;
- assumption_status: CONFIRMED, PROVISIONAL, REJECTED, EXPIRED, NOT_VERIFIED.

An unresolved assumption blocks only the decisions that materially depend on it. It blocks full baseline closure when it affects critical integrity/security/recovery semantics.

## 5. Requirement-to-Evidence Traceability

For critical behavior, trace as far as applicable:

`Requirement -> Canonical Rule -> Owning Component -> Enforcement -> Test -> Runtime Evidence`

A design audit may legitimately stop before implementation/runtime evidence exists, but must mark downstream links `NOT VERIFIED` rather than pretending they exist.

## 5A. Causal Intervention Check

When remediation is authorized and the causal claim is material, specify the exact claim/role, target snapshot, intervention, expected observable effect and acceptance criterion, observation window/delay, alternative active causes/gates, control conditions, measurement coverage/noise floor, and intervention-fidelity check before acting. Prefer reversible, safe, bounded interventions; analysis never grants tool authority.

| Tested role | Appropriate prediction in the stated context |
| --- | --- |
| `NECESSARY` | Removing the cause removes the consequence, once residual state/delays are accounted for. A verified independent sufficient cause contradicts necessity in that context. |
| `SUFFICIENT` | Activating that input under its fixed stated preconditions produces the consequence. One possible outcome or a sampled rate change does not prove sufficiency. Removing it need not eliminate an effect produced by another ANY input. |
| `CONTRIBUTORY` | A justified change in the relevant path, probability, frequency or magnitude when alternatives are controlled and measurements can discriminate it. Saturation/redundancy may mask marginal improvement. |
| `ENABLES` | The specified path becomes unavailable under the disabling conditions; other paths may remain. |
| `AMPLIFIES` / `MITIGATES` | The named amplification/deviation changes as predicted; binary effect presence alone is insufficient. |

Verify the intervention actually took effect, relevant state was drained/reset where required, preconditions/control window matched, and the measurement was sensitive enough. If these are unverified or uncontrolled alternate causes make the prediction non-discriminating, record the causal test as inconclusive; retain the observation but do not automatically reject the cause. Evaluate `repair_disposition` separately against the declared correction outcome. A verified failure of a discriminating prediction contests or rejects the precise tested claim under §3A. In `ANY(A,D) -> B`, A disabled with D active and B persisting rejects an unsupported necessary/exclusive claim about A, not an independently verified contributory/sufficient path from A.

Evaluate repair success separately: unchanged B can demonstrate failure of a repair whose required outcome was eliminating B, even though A remains a valid cause. Success strengthens only the tested claim/scope; it does not prove root-cause exclusivity, regression safety, or full closure. Do not change predictions after observing the outcome or protect a favored diagnosis because of sunk cost. Preserve fidelity failures, confounders, successful and failed predictions in the existing repair ledger, then run core protocol §14 boundary rediscovery and residual review.

## 6. Change Classification

Classify material corrections:

- A — document-only;
- B — backward-compatible contract change;
- C — behavioral change;
- D — breaking change;
- E — data correction/migration;
- F — security emergency.

For C-F assess migration, rollback/recovery, compatibility, rollout, validation, containment, operator impact, and post-change verification. Irreversible operations require a recoverability plan and explicit approval appropriate to the environment.

## 7. Canonical Absorption

A correction proposal is not automatically a fixed system.

Track only affected governing layers, such as:

- owner decision/invariant;
- contract/schema;
- state machine/transaction rule;
- security/failure rule;
- module specification;
- implementation;
- tests;
- operator/runbook material.

Use `absorption_status`: ABSORBED, N/A, MISSING.

Do not require irrelevant layers merely to complete a form.

For authorized edits to existing artifacts, absorption requires evidence that the intended authoritative artifact was updated, not a duplicate or detached copy. Preserve the same stable file/repository identity when available, record the observed version before and after the edit, and state whether the change is document-only or also implemented. A replaced document version supports document-level closure only; implementation and production claims still require their own evidence.

## 8. Coverage Ledger

For large corpora record:

- reviewed exhaustively;
- reviewed by risk-based sampling;
- searched/queried but not exhaustively read;
- unavailable;
- out of scope.

Sampling must identify the selection rule and residual risk. A sampled audit cannot silently become an exhaustive-coverage claim. For BWA, record whether graph expansion was complete, risk-bounded, sampled, truncated, or blocked by out-of-scope/unknown dependencies; `PARTIAL` or `SAMPLED` topology cannot support an exhaustive no-propagation claim.

Track material areas repeatedly changed versus materially under-explored areas. If progress remains within the noise floor or the same hypothesis/change class fails repeatedly, declare `NO_PROGRESS` for that path and inspect the highest-risk blind spot. Do not rotate components performatively; exploration must be tied to a plausible unresolved causal path.

### 8A. Re-Audit Outcome Evidence Tests

Use core §14 `re_audit_outcome` only after comparing the same named claim/finding across a compatible before/after snapshot and oracle:

- `RESOLVED` requires the prior reproduction/obligation to fail to recur under adequate opportunity/sensitivity **and** the declared acceptance/regression obligations to pass. Absence without adequate coverage is `INCONCLUSIVE`, not resolved.
- `SAME_DEFECT` requires stable finding identity plus materially the same violated rule/mechanism or closure obligation; cosmetic symptom changes do not create a new defect.
- `NEW_DEFECT` requires evidence of a distinct violated rule/mechanism/closure obligation and insufficient evidence that the repair caused it. If causally linked to the repair, use `SECONDARY_EFFECT`.
- `SECONDARY_EFFECT` requires compatible timing plus evidence linking the adverse effect to the repair/change; temporal sequence alone is insufficient.
- `FALSE_POSITIVE` requires disconfirming evidence strong enough to invalidate the original observation/claim or a demonstrated faulty oracle/measurement. Mere failure to reproduce with poor coverage is `INCONCLUSIVE`.
- `NO_PROGRESS` requires repeated materially equivalent hypothesis/change attempts or measured improvement remaining within the justified noise floor, with no new discriminating evidence. Preserve the negative evidence and valid reopen condition.
- `INCONCLUSIVE` is mandatory when fidelity, alternate causes, coverage, sensitivity, snapshot compatibility, or evidence quality prevents a defensible classification.

These labels describe **re-audit progression**, not finding lifecycle, causal truth, repair acceptance, or audit decision. Never collapse them into `finding_status`, `causal_state`, `repair_disposition`, or `audit_decision`.

## 9. Design Closure

Design closure can be declared only for the reviewed scope when material rules are authoritative, deterministic, internally consistent, testable, and challenge/regression review has no unresolved BLOCKER and every material MAJOR is FIXED for that scope or eligible ACCEPTED_RISK under §17. An unresolved finding lifecycle (including OPEN, DEFERRED, REMEDIATION_PROPOSED, or PARTIALLY_FIXED) or `verification_state=NOT_VERIFIED|CONTESTED` cannot hide a MAJOR closure obligation. Mandatory semantics/evidence gates cannot be risk-accepted away.

Runtime claims remain `NOT VERIFIED` until implementation evidence exists.

Do not require E3 merely to close a pre-implementation design. Instead require an adequate test obligation and measurable acceptance criterion.

## 10. Implementation Verification

For each critical implementation claim, require executed behavior evidence (E3), with E4 scenario coverage where failure, concurrency, recovery, security, or integration risk warrants it. Record which actual results satisfy each obligation; an E4/E5 result can fulfill E3's execution obligation when it covers the same claim/version, not merely because its label is larger. Inspect conformity with governing rules and regressions, not just the failing reproduction.

A single happy-path unit test is not evidence of recovery/security correctness.

## 11. Production Verification

Use `PRODUCTION VERIFIED` only for relevant E5 evidence from the actual named production deployment, with the applicable design, implementation, regression and failure/recovery obligations satisfied for the stated scope/environment.

A justified production-equivalent environment may receive `PRODUCTION-EQUIVALENT VERIFIED`, not `PRODUCTION VERIFIED`. Equivalence must be demonstrated against material workload, data characteristics, configuration, permissions, dependencies, topology, and operational failure behavior; record differences and restrict the claim accordingly. Production origin or equivalence alone cannot bypass an unmet safety or closure gate.

Record `verification_environment: ACTUAL_PRODUCTION | PRODUCTION_EQUIVALENT`, the observation time window, and material differences from production when applicable.

## 12. Post-Freeze Change

A material post-freeze change should identify:

- reason/business need;
- defect versus enhancement;
- affected invariants/contracts/modules;
- data/security/compatibility impact;
- migration/rollback/recovery;
- required evidence;
- approval/owner;
- target version.

Re-audit the affected dependency cone, not the entire system by reflex.

## 13. Objective Stop Rule

Stop when:

- the requested decision is supportable;
- remaining open items cannot change that decision; and
- limitations are explicit.

For full design closure, require:

- unresolved BLOCKER = 0; historical FIXED BLOCKER records remain with their severity and scope-specific proof;
- every material MAJOR is FIXED for the requested scope or validly ACCEPTED_RISK under §17;
- material authority conflicts = 0;
- material open decisions = 0;
- critical traceability sufficient for implementation;
- canonical absorption of accepted corrections;
- regression and challenge review pass;
- residual risks have owner/status;
- unproven runtime behavior is marked NOT VERIFIED.

Move nonmaterial improvements to backlog.

### 13A. Audit Decision Precedence

Record one `audit_decision` for each explicitly named requested subject/claim, snapshot, scope and verification basis (design, implementation, or production). Apply in order:

1. `REJECTED` — adequate evidence shows the requested candidate/correction is prohibited or untenable within its stated constraints and bounded correction is insufficient. Rejecting one edge or repair is not rejecting the whole audit target.
2. `BASELINE_BLOCKED` — a known mandatory gate for the requested baseline/readiness decision is failed or missing, including material unresolved semantics, critical evidence or coverage. CLOSURE with an unmet mandatory closure gate uses this decision, not generic NOT VERIFIED.
3. `NOT VERIFIED` — the bounded claim lacks adequate evidence and no preceding target-level rejection or baseline gate applies. TARGETED diagnosis normally uses this for an unproved cause; requested runtime readiness with a known missing mandatory execution obligation is BASELINE_BLOCKED.
4. A positive decision only when all applicable gates pass: `CANONICAL DESIGN BASELINE` for design; `IMPLEMENTATION VERIFIED` for the exact implementation; `PRODUCTION-EQUIVALENT VERIFIED` for a justified equivalent environment; `PRODUCTION VERIFIED` only for the actual named production deployment, with relevant E5 and all other applicable obligations.

Positive decisions are mutually exclusive for the same requested claim. Do not substitute a design-only positive outcome when production verification was requested and failed; report any design result as a separately scoped subordinate claim. A known failed gate dominates unrelated missing evidence. Evaluate causality, finding status, and repair disposition in their own fields before applying this decision policy. Partial graph expansion blocks only claims dependent on completeness, not every useful bounded result.

## 14. Versioning

Use semantic or project-compatible versioning consistently. Each baseline revision should identify reviewed inputs, changes, finding transitions, correction/absorption summary, regression evidence, and final decision.

Preserve superseded baselines for auditability. Never let a superseded baseline silently regain authority.

When the requested operation is to overwrite or repair an existing file, prefer replacing that file's current version over creating a new artifact. Create a copy only when the user asks for a separate copy, the target has no writable identity, or splitting is necessary for auditability. If copies or split files exist, mark exactly which artifact is canonical and which artifacts are historical/supporting.

## 15. Evidence Quality Dimensions

E0-E5 describes **where/how evidence was observed**, not certainty by itself. Evaluate each material claim on:

- relevance/directness to the exact claim;
- source authority and provenance;
- independence from other evidence;
- reproducibility and test-oracle quality;
- freshness/version/environment match;
- coverage and sensitivity to the failure being sought.

Correlated evidence is not independent evidence. Ten reports that copy one upstream assertion count as one provenance chain, not ten confirmations. Evidence produced by the same system under audit can be useful but is not automatically independent evidence.

Absence of evidence is not evidence of absence unless the search/monitor/test had adequate coverage, sensitivity, retention, and opportunity to observe the condition. Record those assumptions when using a negative result.

Higher evidence level does not automatically defeat lower-level contradictory evidence. An E5 production log that is only loosely related to a claim can be weaker than an E4 failure-injection result directly testing that claim.

## 15A. Positive and Negative Evidence Ledger

For each material repair attempt retain the finding/hypothesis and snapshot IDs, affected component, exact change/diff identity, role-aware prediction, fidelity/measurement/alternate-cause checks, observed result, causal-state update, regression/cost impact, residual graph/boundary coverage, `repair_disposition` (`ACCEPTED`, `REJECTED`, or `INCONCLUSIVE`), and reason. ACCEPTED requires the declared correction outcome and relevant regression obligations to pass; REJECTED requires demonstrated failure/harm against those obligations; inadequate or non-discriminating proof is INCONCLUSIVE. Rejected or inconclusive attempts are retained evidence, not universal disproof. Reopen them only on material new evidence, changed conditions, or a corrected oracle. Finding FIXED still requires canonical absorption and the requested verification scope.

## 15B. Complexity Tax and Acceptance

Record material complexity introduced by a correction: files, abstractions, dependencies, agents, contracts/envelopes, rules, durable state, runtime/tokens, operational burden, and maintenance ownership. Accept added complexity only when evidence shows a decision-relevant benefit and a simpler viable correction is insufficient. Never weaken correctness, security, data integrity, Host authority, compatibility, recoverability, or output validity merely to improve an aggregate score.

Treat a metric as a **non-compensatory guard** only when an authoritative invariant or non-bypassable constraint defines its floor. A violation rejects the candidate regardless of gains elsewhere. Preferences and soft targets remain explicit trade-offs; do not convert them into hidden hard gates.

## 15C. Held-Out and Cross-Context Re-Audit

When claiming a generalized fix, or when the correction affects a high-risk reusable mechanism, test at least one materially unseen case and relevant cross-context dimensions such as provider/model, task size, mode, environment, ordering, concurrency, or failure timing. Select dimensions from the actual causal and blast-radius analysis, not a generic matrix.

For a narrow deterministic correction with no generalization claim, targeted regression evidence may be sufficient. A passed original reproduction proves that case only; label broader behavior `NOT VERIFIED` until held-out evidence exists.

## 16. Deep-Research Handoff

When an audit conclusion depends on an unresolved external/current factual claim, delegate that specific claim to `deep-research` and consume its research packet. Keep the affected claim at `verification_state=NOT_VERIFIED` and apply the appropriate audit decision until adequate sourced evidence returns. Do not reproduce source-discovery, chronology, external triangulation, or causal-research procedures here.

A handoff back to `deep-research` after it has already answered the dependency is warranted only if **new material evidence** changes the question or exposes a new external factual gap.

## 17. Risk Acceptance Governance

A material risk acceptance record must identify:

- risk/finding ID and exact scope;
- authorized risk owner and evidence of authority;
- rationale and compensating controls;
- residual impact/exposure;
- acceptance date, review trigger, and expiry or explicit reason no expiry is appropriate;
- invalidation conditions (architecture, exposure, regulation, threat, ownership, or environment changes).

`ACCEPTED_RISK` is not `FIXED` and does not reduce severity. A MAJOR is eligible for design closure only as a known residual operational/business exposure with deterministic governing semantics and a valid acceptance record; surface it in the verdict. Acceptance cannot close missing/ambiguous authority, invariant, deterministic semantics, unresolved rule conflicts, or evidence/coverage required by a mandatory decision gate. BLOCKER and non-bypassable violations cannot be accepted into closure. A changed authorized requirement must be explicitly revised and re-audited, not hidden as risk acceptance. Other unresolved MAJOR statuses block closure. Never extrapolate design risk acceptance into implementation or production verification; those claims must separately meet all applicable gates.
