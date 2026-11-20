# Cross-Project Failure Modes, Heuristics, and Trade-Offs

## Framing: Trade-Offs vs. Anti-Patterns

The Dual-Boundary Principle is an **analytical and explanatory framework** (`theory.md` §2 and §3), not a coercive dogma:

- **Exiting the framework (A1/A2)**: Abandoning A1 or A2 removes the vocabulary and direction needed to use the theory at all.
- **Relaxing H (H1-H4)**: Forfeits the corresponding analyzability or guarantee (for example, relaxing H3 forfeits P1's reorganization isolation; relaxing H4 makes intentional escapes indistinguishable from accidental coupling).
- **Declining S (S1-S5)**: S1-S5 are preference-order components (`theory.md` §3) that do not define legality; declining an S criterion relinquishes the corresponding preference criterion or evaluation dimension (for example, declining S5 means no longer using independent-evolution payoff as the evaluation basis for unidirectionality).

To prevent confusing empirical habits with T1 theorems, every entry below is tagged with its exact epistemological tier:

- **`T1-derived`**: Derivable from `theory.md`; violating or declining it forfeits the corresponding T1 guarantee or evaluation dimension.
- **`Empirical heuristic`**: Valuable cross-project engineering experience, NOT a theorem of T1. Always verify fit against the target architecture's paradigm.
- **`T2-specific`**: Representation-layer or lifecycle constraint (§8 blind spot). Disabled by default; enable only when explicitly declared in the target project's T2 projection.
- **`Engineering validation`**: General verification discipline for keeping mechanical checks honest.

---

## Tier 1: T1-Derived Failure Modes

### 1. Fake Decoupling That Scatters Cross-Cutting Order
- **Tier**: `T1-derived` (A1 + D1) + engineering experience
- **Scope**: Applies when genuine cross-module execution order exists.
- **Symptom**: A composition root or orchestrator has high fan-out. Splitting it by letting each sub-module perform its own registration or lifecycle side effects improves structural metrics (lower fan-out, shorter entry function).
- **Trade-off / Why it fails**: Execution order is a cross-cluster relation, not a local concern of any single cluster (D1). Scattering registration across N files forces readers to inspect N files plus prose comments to reconstruct sequence. Structural metrics improve while cognitive complexity regresses (A1: never infer one dimension from the other).
- **Guidance**: When correctness relies on prose comments to explain hidden order, structure has degraded. Let sub-modules construct and return values; keep cross-cutting registration and ordering visible top-to-bottom in the parent/composition root.

### 2. Adding a Vertical Pseudo-Layer to Fix Horizontal Overload
- **Tier**: `T1-derived` (D2 Cause A + A2 + S3)
- **Symptom**: A module grows large because multiple unrelated features live in it. The refactor splits it into "upper wrapper" and "lower helper" layers with a new inter-layer edge.
- **Trade-off / Why it fails**: Vertical splitting does not reduce the complexity of heterogeneous features; it lengthens propagation distance (A2) and creates an unstable contract surface (S3).
- **Guidance**: Diagnose overload cause first. Heterogeneous aggregation (D2 Cause A) calls for parallel functional splitting with zero new inter-layer edges. Only a stable, widely shared minimal semantic unit justifies a new layer (D1).

### 3. Bidirectional Layer Vocabulary Leakage
- **Tier**: `T1-derived` (P1 stability lemma projected onto naming/docs)
- **Symptom**:
  - **Upward leak**: Upper-layer code or comments explain lower-layer implementation details (internal buffers, memory layouts, concurrency workarounds), forcing readers to hold two mental models at once.
  - **Downward leak**: Generic lower-layer modules hardcode upper-layer domain terms, enum names, or default asset keys.
- **Trade-off / Why it fails**: Violates P1's contract stability precondition (contracts must be expressed in consumer semantics without leaking provider internals) and forfeits layer reusability.
- **Caveat**: Removing leaked vocabulary alone does not guarantee readability. Neutral names must be paired with clear semantics, units, and grouping, otherwise one form of cognitive load is merely swapped for another.

### 4. Speculative Generic Abstractions Without Real Consumers
- **Tier**: `T1-derived` (S5)
- **Symptom**: Introducing a universal graph, generic plugin layer, or extra interface "for theoretical completeness" or "future extensibility" when existing primitives already cover all current use cases, leaving parallel idioms in the same codebase.
- **Trade-off / Why it fails**: Under S5, false independence is more expensive than honest coupling. Coexisting representations for the same minimal semantic unit double cognitive chunks without reducing real change coupling; declining S5 relinquishes independent-evolution payoff as an evaluation criterion.
- **Guidance**: Differences must be real and backed by concrete consumers. Prefer retiring or deferring abstractions that add no required contract over existing mechanisms; do not build guards for hypothetical future drift.

---

## Tier 2: Empirical Heuristics (Non-Theorem Cautions)

### 5. Cross-Object State Mutation vs. Owner-Only Writes
- **Tier**: `Empirical heuristic` (NOT derived from T1)
- **Symptom**: Module A reaches into Module B's internal state slot or index to mutate B's behavior at runtime, creating an implicit ordering dependency on who runs first.
- **When it hurts**: Couples execution order to runtime addressing, hides invariants across files, and complicates isolated testing.
- **Applicability boundary**: Owner-only writes are a strong heuristic in simulation loops and localized state machines, but event-driven systems, transactional databases, shared-state stores, and Actor/CQRS architectures may define state ownership differently. Evaluate as a trade-off rather than asserting it as a universal T1 law.

---

## Tier 3: T2-Specific Constraints (Opt-In via Project Projection Only)

### 6. Definition vs. Per-Instance Runtime Binding Separation
- **Tier**: `T2-specific` (§8 representation-layer & lifecycle concern)
- **Note**: `theory.md` §8 is structurally blind to representation and instance lifecycle. This constraint applies **only** when the target project's T2 projection explicitly requires multi-instance isolation, deterministic replay, forkable runtimes, or shared immutable definitions (see `references/t2-projection-template.md` §2). Do not enforce it by default on single-instance apps or stacks with different lifecycle invariants.

---

## Tier 4: Engineering Validation Discipline

### 7. Unverified Mechanical Guards
- **Tier**: `Engineering validation`
- **Symptom**: Adding a static check, linter rule, or architectural test that passes on the current codebase without verifying that it fails when the rule is violated.
- **Why it fails**: A passing validator without a counter-example is indistinguishable from a broken or no-op check, forfeiting the evaluability of the gate.
- **Guidance**: Pair every mechanical architectural guard with explicit negative counter-examples (injected forbidden edges, illegal cycles, or intentional violations) that must fail the check.
