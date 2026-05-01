---
name: refactor-for-agents
description: Clean Code rederived for agentic coding — refactor toward vertical slices, machine-checkable seams, locality, and load-bearing comments. Use when refactoring a file or asked to clean up a codebase.
---

# Clean Code rederived for agentic coding

The Clean Code orthodoxy — small functions, decorative naming, horizontal layers, comments-as-documentation — was scaffolding for human working memory. Agents do most of the reading now, and they read differently: they ingest large spans at once, follow types and contracts more easily than narrative naming, and pay a real cost for jumping between many small files.

This skill rederives the canon for that reader. The aim is fewer files, more locality, contracts at the seams, and no decorative scaffolding. Background: [The Reader Axiom](https://jensroland.com/articles/215/the-reader-axiom).

> **Iron law:** Before flagging or refactoring anything, READ the file. Verify with file:line — don't refactor or claim issues from memory.

## Principles

### 1. Vertical slices, not horizontal layers

All logic for a single entity belongs in one file: type, validator, serializer, repository, service, factory, DI wiring. The previous orthodoxy spread this across `models/`, `serializers/`, `services/`, `repositories/` so a human could navigate by role. Agents don't need that. Locality wins.

When refactoring:
- If a feature is already split horizontally, **collapse** it back into one entity-shaped file.
- If a new entity is being added, write it as one file from the start.
- Exception: shared infrastructure (logging, DB connection pool, framework wiring) lives where it logically belongs, not duplicated per slice.

### 2. One file per concept, up to ~10,000 lines

A file should swallow a whole concept. There is no penalty for size up to roughly 10K lines. Don't split a 1,200-line file because it "feels big" — agents don't get tired.

Do split when:
- The file holds two unrelated concepts (genuinely different entities, not different layers of one entity).
- It crosses ~10K lines and the concept naturally bisects.

### 3. Functions should do one thing — but no line limit

A 200-line function that does one thing, read top-to-bottom, is fine. The "smaller than you think" rule was scaffolding for human working memory.

Extract a helper when:
- The same logic actually repeats (≥3 times — two is a coincidence).
- A subroutine has an independent concept name that genuinely clarifies the parent.
- A pure piece of logic needs its own tests at the seam.

Do **not** extract a helper just to:
- Push the parent function under some line count.
- Give every step a name.
- Match a layered template.

### 4. Machine-checkable seams, not pretty ones

Spend design effort where a machine can verify it: contracts, types, assertions, property tests at the module boundary. Spend none on cosmetic naming gymnastics or decorative formatting.

Look for:
- Module boundaries that lean on naming convention rather than a contract — strengthen with explicit types and assertions.
- Shallow interfaces that pass through to the implementation — collapse them (see [improve-codebase-architecture](../improve-codebase-architecture/SKILL.md)).
- Tests that verify implementation details rather than observable behavior — rewrite at the public seam (see [tdd](../tdd/SKILL.md)).

### 5. Naming: clear enough, not decorative

Names should be clear enough that a reader (human or agent) doesn't get confused — and no more. `elapsedTimeInDays` over `d`, yes. `validateAndPersistOrderWithSideEffectsThroughTheRepositoryFacade` — no. Stop when ambiguity is gone.

Avoid:
- Type-suffixing every variable (`orderObj`, `orderList`, `orderData`).
- Wrapper words that add no information (`Manager`, `Helper`, `Util`, `Handler`).
- Renaming local variables for stylistic uniformity when the original is already unambiguous.

### 6. One canonical implementation per utility

Date formatters, slugifiers, retry wrappers, error envelopes — exactly one per codebase. Two is a bug.

When refactoring:
- Grep for likely siblings of the utility you're touching.
- Collapse to one implementation; remove the others.

### 7. Comments earn their place

Most comments go — names and types do the explaining. But the comments that *do* belong are load-bearing, not decorative, and stripping them is a regression. Keep, and add when missing:

- **Why the code is shaped this way** — a hidden constraint, an invariant the type system can't express, an ordering requirement, a non-obvious dependency.
- **Workarounds for specific bugs** — third-party library quirks, OS edge cases, race windows. Link the issue if there is one.
- **Warnings of consequences** — "this runs in a hot path", "do not call before X is initialized", "modifying this changes wire format".
- **Legal headers** where required.
- **Public-API docstrings** for anything callers depend on — describe the contract, not the implementation.
- **TODO / FIXME** with enough context to be actionable by someone other than the author.

Default to none beyond those. A comment that explains *what* the code does, restates the function name, or narrates the obvious is noise.

### 8. Error handling: fail fast at boundaries, trust internals

Validate at system boundaries (user input, external APIs, deserialization). Inside trusted internal code, let the type system carry the weight.

- **Make errors explicit.** Don't return `""`, `[]`, `0`, or `None` to signal "this didn't work" — that cloaks the problem, and the failure resurfaces five function calls later, far from the cause, much harder to debug. Raise, return a typed error, or use a result type. Let the caller decide what to do; silently substituting an empty value takes that choice away.
- Don't sprinkle `try/except Exception:` to "be safe." Catching a failure you don't know how to handle is the same cloaking, one layer up.
- Custom exceptions for genuinely distinct failure modes a caller would handle differently — not for every call site.

### 9. Tests: behavior at the public seam

Tests verify what the system does, not how. They survive refactors. Property-based and end-to-end tests buy more leverage than unit tests of internal helpers.

**Don't refactor below a minimum test coverage.** Refactoring a poorly-tested module is gambling — passing tests after the refactor only mean you didn't break the cases you happened to cover. Before any structural change:

1. Check the test harness for an existing coverage threshold (`pytest --cov-fail-under`, vitest `coverage.thresholds`, jest `coverageThreshold`, `cargo-tarpaulin` minimums, etc.). Honor it.
2. If none is set, define one that is sufficient for the refactor in scope and achievable for the codebase. Load-bearing modules aim higher; thin glue can defensibly aim lower.
3. If coverage is below the threshold, **write tests first** — at the public seam — until it clears. Then refactor.

Detail in [tdd](../tdd/SKILL.md). This skill flags test code that violates the seam — e.g. tests that mock a private collaborator, or assert on internal data shape — and rewrites them against the public interface.

## Process

1. **Read the file(s) at file:line.** Don't claim from memory.
2. **Identify violations** — pass through the principles above. Note each as: file:line, principle, what's wrong, what to do.
3. **Apply, smallest blast radius first** — naming and comments before structural moves; structural moves before file consolidation.
4. **Run the tests after each structural move.** A failing test is information; a silent regression is a bug shipping under cover of refactor.
5. **Stop when ambiguity is gone**, not when the file is small.

## Output format

For each issue found:

1. **File:line** — exact location.
2. **Principle** — which principle from above.
3. **What's wrong** — one sentence.
4. **Fix** — concrete code change.

Then apply the fixes unless told otherwise.

## Checklist

After the pass, before declaring done:

- [ ] Did I collapse horizontal layers into one entity-shaped file where it applied?
- [ ] Did I leave any helper extracted only to shrink line count? (Inline it.)
- [ ] Did I leave any module boundary leaning on naming convention where a contract, type, or assertion would make it machine-checkable?
- [ ] Did I find and remove duplicate utility implementations?
- [ ] Did I keep load-bearing comments (why, workarounds, warnings, public-API contracts) and remove only the noise?
- [ ] Did I make every silent failure-cloaking return (`""`, `[]`, `0`, `None`) explicit?
- [ ] Did I check or set a coverage threshold, and write tests at the public seam before any structural move below it?
- [ ] Do the tests still pass?
