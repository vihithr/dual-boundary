---
name: dual-boundary
description: Software Architecture Decision Governance powered by the Dual-Boundary Principle (双边界原理) as a Formalized Architectural Reasoning Kernel. Use whenever the user asks to review architecture, diagnose coupling or cohesion, split overloaded modules or composition roots, adjudicate circular dependencies, design layers or public contracts, prevent over-abstraction, evaluate external architecture patterns, or draft falsifiable ADRs across any programming language or tech stack.
---

# dual-boundary

Software Architecture Decision Governance based on the Dual-Boundary Principle (`references/theory.md`). Use this skill as a **Formalized Architectural Reasoning Kernel** and **Internal Architecture Decision Protocol** to diagnose architectural problems, adjudicate refactoring proposals, and define falsifiable verification gates in any codebase.

## Scope and Boundaries

What this skill is:

- An analytical and explanatory baseline for diagnosing structural and cognitive complexity.
- A structured decision protocol for adjudicating splits, layering, cycles, and contracts.
- A governance mechanism for dependencies, public contracts, and falsifiable verification.
- An optional ADR recorder when a durable architectural decision is committed.

What this skill is NOT:

- Not a general code generator or language-specific style linter.
- Not an automatic refactoring executor.
- Not a dogmatic SOLID / DDD / Clean Architecture rule enforcer.

**Authorization boundary**: Architecture diagnosis does not authorize architectural modification. Do not modify project files or enforce structural changes unless explicitly requested by the user.

## When to Use

- Reviewing module boundaries, package dependencies, or public vs. internal API surfaces.
- Breaking circular dependencies (SCCs) or deciding whether a cycle should be kept, sunk, or inverted.
- Splitting an overloaded module, god class, or composition root without destroying cognitive locality.
- Deciding whether to introduce a new layer, interface, or shared abstraction.
- Auditing refactoring proposals or external architecture patterns (SOLID, DDD, Clean Architecture) for real vs. speculative value.
- Writing concise, falsifiable Architecture Decision Records (ADRs) grounded in explicit criteria.

## Steps

### 1. Load the Reasoning Kernel First

Load the complete reasoning kernel (`references/theory.md`) using the runtime's supported skill-resource reading mechanism. Do not proceed with architectural adjudication until the required reference is available.

- Treat `references/theory.md` as the closed, domain-agnostic deductive baseline for your decision protocol, not as optional background reading.
- Use its §0 vocabulary (`模块`, `依赖图`, `SCC`, `商图`, `层`, `结构复杂度`, `最小语义单元`, `契约`, `实现形态`, `逃逸点`) and avoid vague metaphors or ungrounded slogans.
- Preserve the logic status and judgment strength of every rule (`机械可判定`, `经验可判定`, `需认知裁决`). Never present an empirical or cognitive judgment as a mechanical fact.
- Respect the framework's analytical nature (`theory.md` §2 and §3): abandoning A1/A2 exits the framework; relaxing a hard constraint (H1-H4) forfeits the corresponding analyzability or guarantee; declining a soft constraint (S1-S5) relinquishes the corresponding preference criterion or evaluation dimension.
- Note on internal cross-references inside `references/theory.md`: map `practice-*.md` to the target project's T2 projection (Step 2) and `decisions.md` to the target project's ADR log (only if one exists or is requested).

### 2. Establish the Target Project's T2 Projection

Load `references/t2-projection-template.md` using the runtime's supported resource-reading mechanism when entering an unfamiliar codebase or when boundaries are not yet explicit. Bind T1 primitives to the target project before judging it:

1. **Module granularity**: Identify what counts as a module (package, namespace, crate, directory, file, or class) and how dependencies are extracted (compiler graph, static analyzer, bundler graph, or import scan).
2. **Layer semantic axis**: Identify the current layers and their semantic scope. Per §8, choosing or revising semantic axes is a negotiation with the user, not a purely deductive step; confirm with the user if layers are ambiguous.
3. **Contract (T1) vs. Implementation (T2) and H4 escape markers**: Identify how public entry points are declared and how intentional escapes are marked and audited. Exclude test targets from production bypass metrics unless explicitly requested.
4. **Representation-layer invariants (§8 blind spot)**: Explicitly identify the target project's representation-layer constraints (for example: determinism, zero allocation, thread safety, transaction boundaries, serialization compatibility, or definition/runtime lifecycle separation). Topology decisions never override representation invariants; handle them orthogonally.
5. **Empirical evidence (optional, for H1b and C2)**: Record co-change data source, observation window, and thresholds if available. Leave unknown fields blank; never fabricate commit history or co-change stability to fill the template.

### 3. Run Stratified Diagnosis and Adjudication

Separate verifiable facts from empirical inferences and cognitive trade-offs:

1. **Mechanical checks (where the T2 mapping provides sufficient machine-readable declarations)**:
   - Cross-layer cycles (H1a) and SCCs crossing layer boundaries (H2): mechanically checkable from the dependency graph.
   - Contract bypasses (H3/H4): mechanically checkable when the public contract mapping and escape markers are completely declared in T2.
   - Bidirectional layer vocabulary leakage: mechanically scannable for candidates when explicit layer vocabularies exist, but final semantic judgment still requires cognitive adjudication (zero keyword hits never prove absence of semantic leakage).
2. **Cycle disposition (§4)**:
   - High co-change cohesion and permanent co-evolution: **legal cycle**. Contract into a composite unit or merge; do not force artificial unidirectionality (S5).
   - Independent modules coupled via a shared concept: **illegal cycle (insufficient sinking)**. Extract the intersection as a minimal semantic unit and sink it (D1).
   - Reverse edge is pure control-flow notification: **dependency inversion**. Define callback/event contract in the lower layer, implement in the upper layer, and check representation-layer impact separately (§8).
   - Ambiguous co-change: **defer**. Mark the cycle and observe change history.
3. **Overload and layering adjudication (D1 and D2)**:
   - **Heterogeneous aggregation (D2 Cause A)**: Split into parallel sibling modules by co-change cohesion. Keep upstream/downstream layer edges unchanged. Never add a new vertical pseudo-layer just to shrink a crowded module.
   - **Oversized single unit (D2 Cause B)**: Do not split sideways. Re-decompose into minimal semantic units and re-run D1 placement.
   - **New layer creation (D1)**: A new layer is justified only when a stable minimal semantic unit is shared by multiple higher layers with lower change frequency (H1b) and passes the C2 stability threshold. D1 gives a necessary condition, not a sufficient one; weigh S3 contract maintenance cost before adding a layer.

### 4. Run the Trade-Off, Anti-Pattern, and External-Pattern Gate

Before finalizing any diagnosis or recommendation, load `references/anti-patterns.md` (using the runtime's supported resource-reading mechanism) and distinguish between `T1-derived` consequences, `Empirical heuristic` cautions, `T2-specific` opt-in constraints, and `Engineering validation` rules. Run three mandatory checks:

1. **A1 Dual-Dimension Check**: Never infer cognitive improvement from better structural metrics (such as lower fan-out or shorter files). When genuine cross-module execution order exists, verify whether a reader can still understand the unit's semantics and sequence without hopping across extra files or relying on prose comments.
2. **S5 and Speculative Abstraction Check**: Verify that newly introduced contracts, indirections, or generic frameworks have real independent evolution and real consumers. False independence costs more than honest coupling.
3. **§9 / §10 External Pattern Check**: Whenever invoking an external principle or pattern (SOLID, DDD, Clean Architecture, Hexagonal, etc.), explicitly check both §9 questions:
   - Does it act on dependency direction (structural complexity) or semantic aggregation (cognitive complexity)?
   - What primary change pattern does it implicitly assume (e.g., independent evolution vs. permanent co-evolution)?
   Then classify its relation to T1 strictly using §10's four categories: **推论** (derived via explicit mapping + negation conflict, non-synonymous), **同义改写** (synonymous restatement), **仅解释** (compatible explanation only, not entailed), or **未覆盖** (outside §8 scope). Never claim a `仅解释` or `同义改写` item is a T1 derivation.

### 5. Deliver Auditable Output and Falsifiable Verification

For standard architecture reviews and refactoring adjudications, present each major issue using this concise output protocol:

| Field | Required Content |
|---|---|
| **Finding** | Concrete architectural issue or decision point |
| **Coverage Status** | `In-scope & adjudicable` / `In-scope but not adjudicable` / `Out-of-scope` / `Framework not applicable` |
| **Evidence** | Exact code locations, dependency edges, or empirical history |
| **Grounding** | Specific `theory.md` clauses (and T2 invariants if applicable) |
| **Judgment** | `机械可判定` / `经验可判定` / `需认知裁决` |
| **Alternatives** | Candidate options, their trade-offs, and what analyzability or evaluation dimension is lost if H is relaxed or S is declined |
| **Recommendation** | Preferred decision and its applicability conditions (omit T1 verdict if `Out-of-scope` or `Framework not applicable`, and hand off to user when negotiation/cognition governs) |
| **Verification** | Concrete, falsifiable command, static check, or test (including counter-example checks for new guards) |

Coverage Status definitions:

- **`In-scope & adjudicable`**: Inside the theory's applicability domain with sufficient evidence to form a judgment.
- **`In-scope but not adjudicable`**: The theory explains the issue, but lacks sufficient empirical or cognitive evidence to adjudicate yet.
- **`Out-of-scope`**: Outside T1's applicability domain (§8, such as representation-layer choices or semantic-axis negotiation); do not issue a T1 verdict.
- **`Framework not applicable`**: A1/A2 premises are not accepted in this context; do not apply the framework.

ADR restraint:

- Do NOT generate an ADR for routine code reviews or exploratory diagnostics.
- Only draft or update an ADR (using the snapshot-free template in `references/t2-projection-template.md`) when a durable architectural decision is being formally recorded. Never hardcode expiring metric snapshots (node counts, edge counts, pass totals) inside permanent docs.

## Gotchas

- **Do not skip `references/theory.md`**: Summarizing the theory from memory causes drift back into generic "extract more interfaces" habits. Always load it verbatim first.
- **Do not force a verdict on every issue**: Use `Coverage Status` honestly. When an issue is `Out-of-scope` or `In-scope but not adjudicable`, state why instead of fabricating a T1 ruling.
- **Do not overclaim mechanical certainty**: Vocabulary grep scans only surface candidates; zero hits do not prove absence of semantic leakage.
- **Do not elevate heuristics or T2 rules to T1 laws**: Rules like owner-only writes (`Empirical heuristic`) or definition/runtime separation (`T2-specific`) are not derived from T1; never enforce them blindly across unrelated paradigms.
- **Do not conflate H and S losses**: Relaxing H forfeits analyzability or guarantees; declining S relinquishes a preference criterion or evaluation dimension.
- **Do not treat structural cleanup as automatic progress**: Splitting a composition root so each sub-module registers itself lowers fan-out while scattering cross-cutting execution order across files (A1 violation).
- **Do not build guards for hypothetical drift**: Add mechanical checks for verified boundary risks and pair every guard with a counter-example test so a broken validator cannot silently pass.
