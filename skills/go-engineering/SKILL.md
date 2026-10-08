---
name: go-engineering
description: Write, refactor, debug, review, and optimize Go code with repository-aware guidance on API contracts, data ownership, concurrency, failure semantics, and cost. Use for Go implementation and design decisions, including module changes; keep the depth proportional to the requested work.
---

# Go Engineering

Use these decision aids to improve the requested code while preserving engineering judgment. Select the considerations that matter to the change. Correctness contracts are requirements; design preferences are tradeoffs, and a justified alternative needs no exception ceremony.

## Establish the actual constraints

Read enough surrounding code, `go.mod`, applicable `go.work`, build configuration, and CI to identify the module's language version, selected toolchain, supported platforms, build tags, and cgo requirements. Account for workspace replacements and repository architecture. A small edit usually needs local context, not a repository-wide investigation.

The installed compiler, module language version, and selected toolchain are distinct. Toolchain selection can depend on `GOTOOLCHAIN` and module or workspace directives. Verify APIs and behavior against the versions actually used; do not silently raise the minimum version or adopt a dependency's latest API. See [Go toolchain selection](https://go.dev/doc/toolchain) when the distinction affects the task.

## Let contracts and data shape the design

- Keep packages cohesive, exports intentional, and initialization and dependencies understandable. Choose functions or methods according to behavior; package boundaries and constructors should earn their complexity.
- Define interfaces around useful consumer contracts, including existing standard interfaces. Let actual substitution, extension, or repeated algorithms justify interfaces and generics. A function value may express the needed variation directly.
- Decide what each operation copies, shares, retains, or mutates. Passing a struct or slice by value does not imply independent referents. Choose pointer or value receivers for identity, mutation, method sets, and copying cost; synchronization values have additional copying restrictions.
- Make useful zero values where practical, and validate consequential invariants across construction, decoding, mutation, and failure. Private fields alone do not protect mutable data exposed through aliases.
- Distinguish nil, empty, absent, and zero where the contract requires it. An interface containing a typed nil is not a nil interface. Consider serialization and compatibility before normalizing representations.

For substantial interface, representation, or aliasing decisions, read [design and data](references/design-and-data.md).

## Make progress and completion explicit

Errors should support recovery and diagnosis at the appropriate boundary. Preserve or deliberately translate causes, and treat exposed error identity as part of the API. Partial progress, cleanup, successful completion, and rollback are different outcomes.

Use a synchronous operation when callers can manage its concurrency. For background work, identify its owner, stop conditions, error path, and completion signal. Context cancellation requests that work stop; it does not join goroutines or undo external effects. Bound admitted work and retained data where load can otherwise grow without limit.

## Load detail where it matters

Read only the references and sections relevant to the task.

| Situation | Reference |
| --- | --- |
| Goroutines, contexts, admission limits, shared state, or pools | [Concurrency](references/concurrency.md) |
| Errors, I/O, cleanup, networking, databases, untrusted input, or secrets | [I/O and boundaries](references/io-and-boundaries.md) |
| Allocation, retention, algorithms, GC, profiling, or optimization | [Performance](references/performance.md) |
| Unsafe operations, native interfaces, pointer lifetimes, or cgo | [Unsafe and cgo](references/unsafe-and-cgo.md) |
| Dependencies, module graphs, version-sensitive behavior, or verification | [Modules and verification](references/modules-and-verification.md) |

Interfaces, pointers, generics, channels, locks, copying, pooling, dependencies, and unsafe code all have legitimate uses. Prefer evidence over universal rules. Straightforward improvements need not become benchmark projects; specialized mechanisms need benefits that justify their cost and failure modes.

## Close the feedback loop

Use repository formatting, vet and analyzer policy, and behavioral tests at a scope that exercises the change. Select race detection, fuzzing, vulnerability scanning, or deeper profiling when the affected risks justify them. Preserve supported build configurations and compatibility; avoid unrelated modernization and lint cleanup.

Report the result, consequential tradeoffs, and what was actually verified. Distinguish measured improvements from expectations, and covered executions from general guarantees. Expand the investigation when evidence warrants it rather than turning ordinary Go work into an unsolicited audit or framework redesign.
