# T2 Projection Template (Target Codebase Binding)

Use this template to bind the domain-agnostic primitives in `references/theory.md` (T1) to a specific software project (T2). Fill in only what is verified from the codebase or confirmed by the user; leave unknown fields blank rather than fabricating data.

## 1. Structural Mapping (T1 -> Target Codebase)

| T1 Primitive (`theory.md`) | Target Project Binding | Verification / Extraction Method |
|---|---|---|
| Module (`模块`) | e.g., package / crate / workspace / namespace / directory | How modules are enumerated |
| Dependency Graph `G = (V, E)` | e.g., compiler graph, import graph, bundler graph | Command or static analysis tool |
| Layer Order (`层`, §0 / H1b) | e.g., `core -> domain -> app -> host/ui` | Negotiated semantic axis (confirm with user if unclear) |
| Public Contract (`契约`, T1) | e.g., barrel exports, `public` entry manifest, OpenAPI/proto | Where public entries are declared |
| Implementation (`实现形态`, T2) | e.g., `internal/`, private modules, unexported files | What external callers must not touch directly |
| Escape Marker (`逃逸点`, H4) | e.g., lint allowlist, `@internal-escape` tag, explicit port | How intentional escapes are audited |

**Metric scope**: State whether test targets (e.g., `*.test.*`, `tests/`, `*.Tests`) are excluded from production H3 bypass metrics (recommended default: exclude tests from production bypass rate).

## 2. Representation-Layer & Lifecycle Invariants (§8 Blind Spot)

`theory.md` is structurally blind to how references, memory, and lifecycles are represented at runtime. Record the target project's non-negotiable T2 constraints here so topology refactors never violate them:

- **Memory / Performance invariants**: e.g., zero-allocation hot path, immutable state, copyable/forkable runtime, bundle size budget.
- **Concurrency / Consistency invariants**: e.g., determinism, thread safety, transaction atomicity, stateless horizontal scaling.
- **Lifecycle / Isolation invariants (optional)**: e.g., strict separation between shared immutable definitions and per-instance runtime bindings/progress (enable only if required by multi-instance, replay, or sandbox isolation).
- **Compatibility invariants**: e.g., wire format, DB schema migration, public semver guarantees.

## 3. Empirical Evidence (Optional - For H1b & C2)

H1b (stable dependency order) and C2 (decision stability) depend on empirical change history. Record what is actually observable; leave blank or mark `Unknown` if unavailable so occasional co-edits are not mistaken for stable co-evolution:

| Item | Recorded Value / Status |
|---|---|
| **Co-change Source** | e.g., Git history, issue tracker, release logs, or `Unknown` |
| **Observation Window** | e.g., last 6 months / last 300 commits / last 3 releases |
| **Frequency Threshold** | Thresholds (`δ`, `N`) used for H1b / C2 stability decisions |
| **Cognitive Evidence** | Basis for identifying minimal semantic units (domain docs, team interviews, review feedback) |
| **Known Blind Spots** | Constraints or dependency edges that current tools/data cannot verify |

## 4. Mechanical Gates and Falsifiable Commands

Record runnable commands instead of static metric snapshots:

- **Cycle / Layer direction check (H1a, H2)**: `<command or script>`
- **Contract bypass check (H3, H4)**: `<command or script>`
- **Layer vocabulary check (bidirectional)**: `<grep / lint / script>`
- **Behavioral / regression check**: `<test command>`

## 5. Minimal ADR Template (Snapshot-Free)

Use only when a durable architectural decision is formally committed (not for routine reviews):

```markdown
## D-XXX <Decision Title>

- **Status**: proposed | accepted | implemented | retired
- **Context**: Concrete problem and state at decision time (the only place history belongs).
- **Decision**: Exact structural or contract change.
- **Grounding**: Which `theory.md` primitives (e.g., A1, H3, §4, D2 Cause A, S5, §8) and T2 invariants justify this.
- **Consequences & Verification**: Trade-offs knowingly accepted (including any forfeited analyzability or declined evaluation dimensions) + runnable command(s) that falsify regressions.
```
