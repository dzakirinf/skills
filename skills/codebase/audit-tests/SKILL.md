---
name: audit-tests
description: Sweep a test suite for tests that cannot fail, or that fail without meaning anything.
disable-model-invocation: true
---

# Audit tests

A test is a **tautology** when it is true by construction: nothing a developer can plausibly break turns it red. It costs a run every CI cycle and buys a green tick.

A test earns its place when it guards a **contract** — an invariant, a state transition, error semantics, idempotency, ordering, persistence. *"Retrying with the same key never double-charges"* is a contract. *"The charge function returns an object"* is not.

## 1. Scope

Test files the repo owns. Enumerate them. Templates and examples rank first — a tautology there is copied forward as the house pattern.

## 2. Sweep

Weak matchers are the cheap tell — `toBeDefined`, `toBeTruthy`, `toBeInstanceOf`, `typeof`, a bare `not.toThrow`, a test body with no assertion at all, a snapshot of something rendered with no props.

Grep orders the queue; it does not bound it. Read every test in scope, because the expensive findings are well-formed. A test with strong matchers, a clear name, and thorough setup still pins nothing if every value it asserts came from a mock the same file wrote. Those pass review; grep never sees them.

## 3. Classify

Two questions per test, in order.

**Name the change to the code that turns this red** — the *red question*. No answer means tautology:

| Answer | What it actually pins |
|---|---|
| "Deleting the export" | That the feature exists |
| "A type error" | What the compiler already proves |
| "Editing the mock" | That the mock agrees with itself |

**Would that change be a defect?** A test that survives the red question can still be feature-shaped — tracking the shape of the code rather than the guarantee:

- It goes red on intended work — a hardcoded file list, a bare snapshot.
- A real defect leaves it green. `expect(errors.length).toBeGreaterThan(0)` passes when the validator rejects for entirely the wrong reason.

Findings land at assertion level as often as test level. A sound test carrying one weak assertion is a finding against the assertion, not the test.

Four shapes look like findings and are not:

- **Trust boundaries.** Asserting the shape of an API response, `JSON.parse` output, a DB row, or an env var. There the type is a claim, not a guarantee — the compiler was told to believe it.
- **Recorded fixtures.** A payload captured from real traffic, or the provider's own test double, driving *your* error handling. The subject is your code's reaction, not the provider's behaviour.
- **A double that ships.** When the fake is a product artifact rather than test scaffolding, it is a subject.
- **Regression tests** pinning a bug that actually happened, however trivial they read.

## 4. Report

Every test in scope has its red question answered before the report goes out.

Per finding: `file:line`, the test name, the answer to the red question, and the verdict — delete, or the contract the assertion should pin instead. When the finding is against one assertion rather than the test, name the assertion.

Close on two counts: tests audited, findings raised. Report and stop — rewriting is a separate pass.
