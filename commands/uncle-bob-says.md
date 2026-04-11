---
description: Review and refactor code using Clean Code principles (naming, functions, comments, error handling, etc.)
allowed-tools: Read, Grep, Glob, Edit, Write
---

# Clean Code Review

> **Iron Law:** Before applying any principle, READ the relevant source file first.
> Do not refactor or flag issues based on memory — verify the actual code (file:line) before making any claim.

Apply the principles of "Clean Code" by Robert C. Martin to the code I point you at. Transform "code that works" into "code that is clean."

## Core Philosophy
> "Code is clean if it can be read, and enhanced by a developer other than its original author." — Grady Booch

## Principles to Apply

### 1. Meaningful Names
- Use intention-revealing names: `elapsedTimeInDays` instead of `d`
- Avoid disinformation: don't use `accountList` if it's actually a `Map`
- Make meaningful distinctions: avoid `ProductData` vs `ProductInfo`
- Use pronounceable/searchable names
- Class names: nouns (`Customer`, `WikiPage`). Avoid `Manager`, `Data`
- Method names: verbs (`postPayment`, `deletePage`)

### 2. Functions
- **Small**: Functions should be shorter than you think
- **Do one thing**: A function should do only one thing, and do it well
- **One level of abstraction**: Don't mix high-level business logic with low-level details
- **Descriptive names**: `isPasswordValid` is better than `check`
- **Few arguments**: 0-1 is ideal, 2-4 is okay, 5+ needs a parameter object
- **No side effects**: Functions shouldn't secretly change global state

### 3. Comments
- Don't comment bad code — rewrite it
- Good comments:
  - legal
  - informative
  - explanation of intent
  - clarification (external library behavior)
  - warning of consequences
  - TODOs
  - amplifications
  - Docstrings in public APIs
- Bad comments: mumbling, redundant, misleading, mandated, noise, position markers

### 4. Formatting
- The newspaper metaphor: high-level concepts at the top, details at the bottom
- Related lines should be close to each other

### 5. Objects and Data Structures
- Hide implementation behind interfaces
- Law of Demeter: a module should not know about the innards of objects it manipulates
- Prefer `order.getCustomerCity()` over `order.getCustomer().getAddress().getCity()`

### 6. Error Handling
- Use custom exceptions instead of return codes
- Don't return null — throw or return an error state
- Don't pass null — validate at boundaries
- Fail fast: throw exceptions as soon as a problem is detected, don't silently ignore invalid inputs or states

### 7. Unit Tests
- F.I.R.S.T.: Fast, Independent, Repeatable, Self-Validating, Timely

### 8. Classes
- Small, single responsibility (SRP)
- Practice Object Oriented Design in any language that supports it: Use objects to hide data and expose behavior. Use this to enforce invariants and encapsulate complexity. Avoid exposing data structures and low level functions that allow external code to manipulate internal state directly.
- Prefer composition over multi-layered inheritance

### 9. Smells to Watch For
- Rigidity (hard to change), Fragility (breaks elsewhere), Immobility (hard to reuse), Viscosity (wrong thing is easier), Needless complexity/repetition

## Output Format

For each issue found, report:
1. **File and line** — exact location
2. **Principle violated** — which Clean Code principle
3. **What's wrong** — brief explanation
4. **Suggested fix** — concrete code improvement

Then apply the fixes unless I say otherwise.

## Checklist
- [ ] Is every function smaller than 20 lines?
- [ ] Does every function do exactly one thing?
- [ ] Is complexity encapsulated in well-named classes/functions?
- [ ] Are all names searchable and intention-revealing?
- [ ] Are there no functions with 5+ arguments that lack a parameter object?
