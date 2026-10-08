# Design and Data

Use the relevant sections when changing package boundaries, APIs, invariants, or shared representations.

## Cohesion before abstraction

Group behavior and state by responsibility. Keep exports as narrow as the callers need and make initialization and dependencies visible enough to test and operate. `init` and global registration can fit established plugin conventions; avoid hiding fallible startup or lifecycle ownership there without a reason.

Small consumer-defined interfaces often clarify dependencies. Reuse contracts such as `io.Reader` when their semantics fit, rather than inventing equivalent interfaces for every concrete type. Returning a concrete type can preserve useful capabilities; returning an interface can be the intended abstraction. Decide from the caller's contract and compatibility needs. Speculative mocking alone is a weak reason to introduce a layer.

Use generics for genuinely repeated type-independent behavior and function values for simple variation. Consider constraints, inference, diagnostics, and exposed API commitments. Embedding promotes methods and changes method sets; it does not provide virtual dispatch from the embedded value into the outer type. Prefer explicit forwarding when promotion exposes unwanted behavior or makes invariants hard to follow.

## Values, identity, and invariants

Choose receivers according to mutation and identity, size and copying cost, and the method sets needed by callers. A pointer receiver does not automatically imply heap allocation. A value receiver copies its receiver, but reference-bearing fields can still share data. Avoid copying used mutexes, wait groups, atomic wrappers, or other types whose contracts forbid it, including copies through assignment, return values, containers, or value receivers.

A useful zero value reduces setup, but an invalid zero state can be appropriate when required resources or configuration cannot be inferred. Make the valid states clear and ensure constructors, decoders, setters, and returned mutable views preserve them. Prepare fallible work before committing mutations when the contract promises an unchanged value on failure; otherwise describe meaningful partial progress.

## Slices, maps, and retained views

A slice copy shares its backing array. `append` may reuse that array or allocate, so spare capacity can change whether another view observes writes. Full slice expressions and `slices.Clip` restrict capacity; they neither isolate existing elements nor release the retained backing array. Copy when independent lifetime or mutation is required.

Slice and map cloning is shallow: contained pointers, slices, maps, and other referents may remain shared. A map assignment shares the map; cloning its entries does not necessarily make an immutable snapshot. Views returned by scanners, buffers, and parsers may expire at the next operation. Keep them only for their promised lifetime or copy at the retention boundary.

Passing a slice or pointer through a channel does not itself transfer exclusive access. Establish a handoff convention, publish immutable data, copy, or synchronize subsequent access. Choose the mechanism according to actual sharing, rather than adding a copy everywhere.

## Representation is part of the API

A typed nil inside an interface can produce a non-nil `error` or a seemingly present dependency. Return a nil interface for the absent case, and make receiver-specific nil behavior explicit where supported.

Keep absent, null, zero, and empty distinct when decoding, patch semantics, or a wire contract depends on them. A pointer alone may not preserve every distinction; use presence tracking when needed. Go strings hold bytes and need not contain valid UTF-8; byte length, rune count, and grapheme count differ. Check integer conversions and avoid losing large numeric values through an intermediate floating-point representation.

Map iteration order is unspecified. Introduce explicit ordering or canonical encoding where reproducibility, signatures, or stable output requires it, while respecting the chosen serializer's actual guarantees.
