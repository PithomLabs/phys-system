# AGENTS.md — `phys-system` Collective Operating Manual

## 0. Purpose

This document is the **collective, documentation-only operating manual** for AI
agents and human researchers working across the Phys ecosystem:

```text
phys-artifact    canonical artifact identity + bytes
       ↑
phys-math        pure mathematical representation + bounded execution
       ↑
phys-lib         physical-framework semantics (`gr`, future `bohm`, `cst`, `cdt`, ...)

phys             frozen predecessor/reference system
phys-gr          frozen historical GR implementation/reference
phys-system      this collective documentation/control-plane layer
```

This directory contains no production physics or mathematics implementation.
It explains **what the components are, why they are separated, when each is
used, where semantics belong, and how an AI agent must operate across them**.

It does not replace the normative representation specification or package-local
contracts. It organizes their use at the collective level.

---

## 1. Human rationale

The ecosystem is intentionally split because four different concerns must not
be conflated:

```text
artifact identity / canonical bytes
        ≠
pure mathematics
        ≠
physical interpretation / framework semantics
        ≠
legacy integrated implementation
```

The reason for the split is not stylistic. It protects long-term extensibility.
A future framework such as Bohmian mechanics, causal-set theory, or CDT should
be able to reuse the same mathematical artifact without importing GR meaning
into the mathematical layer.

Likewise, a mathematical identity must remain stable even when different
physical frameworks interpret that mathematics differently.

The collective architecture therefore follows:

```text
common mathematical structure → PhysMath
physical meaning              → phys-lib/<framework>
artifact identity/encoding    → phys-artifact
legacy/reference behavior     → phys / phys-gr (frozen)
agent coordination            → phys-system
```

The existing legacy agent manual already establishes the broader epistemic
principle: formal validity is not empirical truth, and human researchers plus
empirical reality remain the final scientific authority. This document extends
that principle to the new multi-library architecture.

---

## 2. Component map

### 2.1 `phys-artifact`

**Role:** lowest-level canonical identity and representation substrate.

Use it for:

- `ArtifactRef`
- digests
- canonical primitive encoding
- framing and lengths
- canonical integer representation
- grammar-level decoding/validation

It knows nothing about:

- GR
- quantum mechanics
- physical assumptions
- theorem interpretation
- framework semantics

Dependency rule:

```text
phys-artifact → nothing local
```

It is the bottom of the new stack.

### 2.2 `phys-math`

**Role:** pure mathematical representation and bounded mathematical execution.

It owns:

- the closed expression representation
- mathematical families
- mathematical identity rules
- canonical mathematical serialization
- bounded transformation rules
- derivations, theorem structures, equations, conditions, and related pure math

It must remain independent of physical theories.

It does not know that an expression is "GR", "Bohmian", "CDT", or any other
physical framework.

### 2.3 `phys-lib`

**Role:** physical semantics and framework-specific artifacts.

It is one Go module containing focused framework packages:

```text
phys-lib/
    core/       # intentionally empty at Phase 1
    gr/         # General Relativity
    ...         # future framework packages
```

A framework package owns:

- framework identity
- physical assumptions
- physical definitions
- physical equations
- physical theorems
- physical interpretations
- theory-local algebra and validation where required

Framework packages may depend downward on PhysMath and/or PhysArtifact where
the contract permits them. They must not import one another merely to share
physical meaning.

### 2.4 `phys`

`phys` is a **frozen predecessor/reference system**.

Do not modify it as part of development of the new architecture.
Do not move functionality into it.
Do not use it as a dependency of the new modules.

Its historical contracts can be useful as reference evidence, but new code must
be judged against the current canonical architecture and normative
specification.

### 2.5 `phys-gr`

`phys-gr` is the **frozen historical GR implementation/reference**.

It is a source of semantic and protocol evidence during migration, not a library
to import.

New code must not:

- import `github.com/PithomLabs/phys-gr`
- modify files in `phys-gr`
- add compatibility shims there
- treat legacy bytes as canonical bytes for the new system

Legacy behavior may be re-expressed as new test vectors when explicitly scoped.

### 2.6 `phys-system`

This folder is the collective documentation/control-plane layer.

It explains relationships and operating rules but introduces no production API.

---

## 3. Architectural principles

### 3.1 Separate coupling, then restore necessary cohesion

When concerns are conflated, separate them.

When separation breaks a relationship that is semantically necessary, identify
the smallest stable interface that restores that cohesion.

Do not solve coupling by creating a generic dumping-ground package.

### 3.2 Keep the true common denominator in the core

A structure belongs in `phys-math` only if it is genuinely mathematical and
framework-neutral.

A structure belongs in `phys-lib/core` only if its semantics are genuinely
shared by physical frameworks and stable outside any one theory.

When uncertain, do not promote for convenience. Keep the abstraction local until
there is evidence for promotion.

### 3.3 Mathematics and physical semantics are different layers

The same mathematical artifact may have different framework interpretations.

For example, a generic commutation/equivariance-shaped mathematical relation
may occur in GR and Bohmian mechanics without implying that the two physical
theorems are identical.

Therefore:

```text
same mathematics
    ≠
same physical theorem
```

and:

```text
shared mathematical representation
    ≠
shared physical ontology
```

### 3.4 Canonical identity is not semantic truth

A canonical digest proves stable identity of a representation.
It does not prove that the represented claim is physically true.

Likewise:

```text
byte-equal      ≠ empirically true
canonical       ≠ physically correct
replayable      ≠ mathematically justified by itself
```

---

## 4. Formal/physical boundary

The canonical direction is:

```text
physical framework
        ↓
reference to pure mathematics
        ↓
canonical mathematical artifact
        ↓
canonical artifact identity
```

Not:

```text
physical concept → special Math AST node
```

Do not add a PhysMath node merely because a physical theory needs a concept.
First ask whether the concept is actually theory-neutral mathematics.

Examples of things that should remain framework-local unless later evidence
shows otherwise:

- GR coordinate semantics
- metric conventions
- curvature conventions
- connection semantics
- GR-specific tensor contractions
- GR trigonometric behavior
- Bohmian physical interpretation
- theory-specific regularity assumptions

---

## 5. Mathematical claim protocol

Every nontrivial theorem, proof fixture, derivation, or protocol must explicitly
separate four layers:

```text
1. SEMANTIC CLAIM
2. FORMAL REPRESENTATION
3. EXECUTION / DERIVATION TRACE
4. FRAMEWORK INTERPRETATION
```

Do not let metadata silently substitute for formal mathematics.

### 5.1 Claim-to-conclusion trace

For every theorem-like artifact, create a trace:

```text
claimed proposition
      ↓
formal LHS / RHS or equivalent proposition structure
      ↓
initial artifacts
      ↓
derivation steps
      ↓
designated final artifacts
      ↓
formal conclusion relation
      ↓
theorem artifact
```

Every artifact that defines the claimed proposition must be **semantically live**
in this chain.

A fixture is incorrect when it constructs the intended theorem sides but the
persisted theorem only asserts equality of independently computed final normal
forms.

### 5.2 Proposition preservation

These are different obligations:

```text
Computation:
    normalize(LHS) == normalize(RHS)

Theorem preservation:
    the formal theorem conclusion remains the claim
    represented by the original LHS/RHS.
```

Never treat the first as automatic proof of the second.

### 5.3 Metadata firewall

Manifest prose, names, descriptions, and explanatory text are metadata unless
the normative schema explicitly makes them identity-bearing.

Never use prose as an unexplained mathematical premise.

---

## 6. Durable artifact lifecycle

For every durable artifact family, the expected lifecycle is:

```text
construct
   ↓
validate
   ↓
canonical encode
   ↓
store / mint
```

The invariant is:

```text
invalid durable object → MUST NOT enter the content-addressed store
```

Validation and encoding must not disagree about what is legal.
If encoding rejects an object, structural validation should normally reject it
earlier as well.

For every durable family, agents should be able to answer:

```text
Where is the type defined?
Where is it validated?
Where is it encoded?
Where is it minted?
Where is it decoded?
Where is it replay-checked?
```

---

## 7. Identity and canonicalization discipline

Canonical representation is an architectural invariant, not merely a codec
detail.

When changing identity-bearing structures, verify all of:

- canonical bytes
- digest/reference derivation
- field ordering
- framing
- unknown-tag handling
- duplicate handling for SET-valued fields
- sequence preservation for SEQUENCE-valued fields
- round-trip stability
- malformed-input rejection

For identity-bearing mathematical symbols and parameters, preserve the exact
identity rules from the normative representation specification.

Do not replace exact canonical identity with intuitive semantic equality.

---

## 8. Condition lifecycle

Mathematical conditions are structured mathematical artifacts.

When a closed rule contract requires a condition:

```text
framework/fixture materializes condition
          ↓
OperationInvocation supplies ConditionRefs
          ↓
rule checks exact supplied condition
          ↓
success OR fail closed
          ↓
TransformResult threads normalized supplied conditions
```

Phase-1 rules do not silently manufacture new durable mathematical conditions.

Framework assumptions may reference an existing mathematical condition when the
assumption is genuinely expressible as mathematics. Non-expressible physical
assumptions remain framework-level semantics.

Never infer a missing mathematical condition from prose or silently discharge
one.

---

## 9. Framework organization

Each `phys-lib/<framework>` package is responsible for its own physical meaning.

The framework package may own additional local representations when PhysMath
cannot express the necessary mathematics, provided the projection boundary is
explicit and fallible.

A framework may expose:

```text
framework artifact
assumptions
physical definitions
equations
theorems
interpretations
local algebra
projection into PhysMath where representable
```

It must not force nonrepresentable theory-specific structures into PhysMath by
inventing synthetic mathematical identifiers.

### 9.1 Framework closure

Framework inheritance/base relationships are artifact/data dependencies, not Go
imports.

When a contract says a framework may reference the transitive base-framework
closure, implementation and tests must cover:

```text
depth 0
 depth 1
 depth 2+
dangling base
wrong family
wrong namespace
self-cycle
multi-node cycle
```

Do not implement a transitive contract with only direct-edge checks.

---

## 10. Core promotion protocol

`phys-lib/core` begins empty by design.

Do not create a generic type there merely because several packages currently need
something similar.

A promotion requires evidence that the abstraction is:

- framework-independent in physical meaning
- semantically stable across frameworks
- free of theory-specific assumptions
- a coherent reusable abstraction
- supported by evidence of multi-framework use
- explainable without naming one physical theory

When evidence is ambiguous, keep the abstraction in the specific framework.

Classification records are governance artifacts, not a substitute for good API
design.

---

## 11. Legacy/reference protocol

Treat the legacy systems as evidence, never as silent dependencies.

```text
phys / phys-gr
    ↓ reference evidence
new canonical representation
```

Legacy bytes are not automatically the new canonical bytes.
A ported vector means that the semantic content has been intentionally
re-expressed under the new representation contract.

Ported vectors should identify their category, for example:

```text
A — PhysMath-expressible content re-encoded canonically
B — framework-local behavior vector
C — projection success/failure vector
```

---

## 12. Agent workflow: before changing code

For any nontrivial task:

```text
[ ] 1. Identify the user goal precisely.
[ ] 2. Identify which layer owns the requested behavior.
[ ] 3. Read the relevant normative specification.
[ ] 4. Read the relevant package-local AGENTS.md / README / manifest.
[ ] 5. Inspect the existing implementation, not only the plan.
[ ] 6. Identify current framework assumptions and scope boundaries.
[ ] 7. State the semantic claim or invariant being preserved.
[ ] 8. Map claim → representation → derivation → conclusion where applicable.
[ ] 9. Identify what existing tests actually prove.
[ ] 10. Identify at least one way the implementation could be wrong while
       existing tests remain green.
```

Only then implement.

---

## 13. Agent workflow: while implementing

Prefer the smallest change that satisfies the existing contract.

For ordinary implementation details, choose idiomatic Go without escalation.

Do not ask for permission for:

- helper names
- constructor names
- file organization within an agreed package
- internal data structures
- test helper organization
- allocation strategy
- caching
- ordinary CI mechanics

Do ask when the decision changes:

- ownership
- dependency direction
- canonical identity
- serialization
- durable schema/family
- mathematical vocabulary
- physical semantics
- rule semantics
- protocol semantics
- framework boundaries
- Phase scope
- preservation boundaries
- core/specific classification

Also stop the affected behavior when there is a **semantic contradiction**, even
if the architecture and tests otherwise appear green.

---

## 14. Agent workflow: completion test

A phase is not complete merely because `go test` is green.

Report five independent dimensions:

```text
STRUCTURAL
    required files, APIs, schemas, modules exist

BEHAVIORAL
    positive and negative tests pass

SEMANTIC
    intended mathematical/physical claim is preserved

INDEPENDENCE
    important goldens/witnesses are not circularly generated
    by the same implementation under test

BOUNDARY
    ownership, dependency, namespace, and preservation rules hold
```

### 14.1 Semantic liveness audit

For each theorem/fixture:

```text
[ ] Is the claimed proposition explicitly stated?
[ ] Are its formal sides represented?
[ ] Are those sides connected to the derivation?
[ ] Are designated final artifacts connected to those sides?
[ ] Does the conclusion relation refer to the intended proposition?
[ ] Can a mutation disconnect the original proposition while replay still passes?
```

The last question is mandatory.

### 14.2 Golden independence audit

For every important golden value ask:

```text
Was it derived independently from the implementation under test?
```

Classify evidence as needed:

```text
SPEC-DERIVED
HAND-DERIVED
LEGACY-PORTED
INDEPENDENTLY-COMPUTED
IMPLEMENTATION-DERIVED
```

Implementation-derived values are useful for regression detection but weak as
independent correctness evidence.

---

## 15. Adversarial review protocol

For every major implementation, ask:

> **What incorrect implementation could still pass the current tests?**

Attack the system at several levels:

### Artifact layer

- alternate encodings
- malformed frames
- ambiguous integers
- trailing bytes
- unknown tags
- identity drift

### Math layer

- physical leakage
- schema expansion
- invalid family references
- rule loopholes
- condition laundering
- capture/identity errors

### Framework layer

- GR assumptions leaking into core/math
- non-transitive closure
- cycles
- cross-framework imports
- synthetic identities

### Proof/protocol layer

- dead theorem inputs
- conclusion weakening
- replay that checks self-consistency but not semantic intent
- skipped or reordered steps
- metadata replacing formal content
- circular goldens

### Preservation layer

- legacy modification
- hidden imports
- compatibility shims
- workspace tricks masking module dependencies

---

## 16. Tests must prove the right thing

For important behavior, prefer a layered test set:

```text
positive construction
      +
negative construction
      +
mutation
      +
semantic witness
      +
independently derived golden where practical
```

A passing replay test means the recorded derivation is internally consistent.
It does not automatically mean the derivation proves the intended proposition.

A passing canonicalization test means the encoding is stable.
It does not establish physical or mathematical truth.

---

## 17. Protocol and metadata discipline

When a protocol package uses a manifest:

```text
manifest = machine-readable metadata about the fixture
formal artifacts = the actual mathematical/physical content
execution trace = proof/protocol behavior
```

Do not make the manifest a hidden premise merely because it describes the
premises.

If a protocol contract cannot be represented by the current schema, report a
**PROTOCOL GAP** rather than inventing fields or silently changing semantics.

---

## 18. Versioning and migration discipline

During local Phase-1 development:

- workspace wiring may use `go.work`
- do not invent publication/version ontology unless specified
- do not add module `replace` directives merely for convenience when they conflict
  with the current module strategy
- do not use version labels as semantic identifiers unless the contract says so

A migration is complete only when both of these are true:

```text
new architecture works
AND
legacy preservation boundary remains intact
```

---

## 19. What an AI agent must never infer

Never infer from:

```text
same bytes
same digest
same replay result
green tests
successful decoding
successful serialization
```

that a physical theorem is true.

Never infer from:

```text
similar API
same field names
same behavior in one framework
```

that two concepts should be moved into `phys-lib/core`.

Never infer from:

```text
manifest prose
README prose
commentary
```

that a statement is formally encoded unless the actual canonical artifact says
so.

---

## 20. Forbidden shortcuts

```text
[ ] Do not modify frozen `phys` or historical `phys-gr`.
[ ] Do not import `phys-gr` from new code.
[ ] Do not introduce reverse dependencies.
[ ] Do not put physical meaning into `phys-math` for convenience.
[ ] Do not create a generic dumping-ground core package.
[ ] Do not invent PhysMath nodes/families to fit one theory.
[ ] Do not create synthetic theory-specific mathematical identifiers.
[ ] Do not mint invalid durable artifacts merely because `Encode()` succeeds.
[ ] Do not generate missing mathematical conditions silently.
[ ] Do not treat a relation artifact as proof merely because it exists.
[ ] Do not let a theorem fixture prove only a weaker normalized statement.
[ ] Do not rely on implementation-derived goldens as sole correctness evidence.
[ ] Do not start another planning revision for ordinary implementation detail.
```

---

## 21. Decision hierarchy

When sources appear to conflict, use this hierarchy:

```text
1. explicit current user/architect directive
2. normative representation specification
3. package-local normative contract / AGENTS.md
4. this collective `phys-system/AGENTS.md` workflow contract
5. current implementation and tests as evidence of actual behavior
6. historical plans/reviews as rationale and migration evidence
7. agent preference
```

A lower item cannot silently override a higher one.

If a genuine conflict remains at a semantic or architectural level, stop the
affected behavior, present the competing interpretations and consequences, and
ask a focused question.

---

## 22. The collective mental model

Humans and AI agents should reason about the ecosystem as a layered compiler and
representation system:

```text
                HUMAN / AI REASONING
                        │
             scientific interpretation
                        │
        ┌───────────────┴────────────────┐
        │                                │
   physical theory                  empirical reality
        │
   phys-lib/<framework>
        │
   physical semantics
        │
   ───────── framework boundary ─────────
        │
      phys-math
        │
   pure mathematical structure
        │
   ───────── identity boundary ──────────
        │
   phys-artifact
        │
   canonical bytes + identity

Historical/reference systems:
   phys, phys-gr
        ↑
   preserved evidence only

Agent coordination:
   phys-system
```

The architecture is successful when the layers can evolve independently while
still composing through small, explicit, deterministic interfaces.

---

## 23. Final operating rule

The agent's job is not merely to make code pass.

The agent must preserve the chain:

```text
INTENT
  → SEMANTIC CLAIM
  → FORMAL REPRESENTATION
  → CANONICAL IDENTITY
  → EXECUTION / DERIVATION
  → CONCLUSION
  → FRAMEWORK INTERPRETATION
```

Whenever those links are preserved, the system can grow across physical theories
without turning the mathematical core into a theory-specific ontology.

Whenever one of those links is broken, the agent must surface the break rather
than hide it behind a green test suite.

**This file is documentation-only. It authorizes no code or semantic change by
itself.**
