# Unsafe and cgo

Use when implementing or changing unsafe operations or native boundaries, not just because a dependency contains them.

## Explain the lifetime and representation

Prefer a suitable safe API when it expresses the operation. Justify unsafe code through a concrete interoperability need or measured benefit, and keep the assumptions near the operation. Establish bounds, alignment, layout, aliasing, initialization, and lifetime for every affected access.

`uintptr` is an integer, not a GC-tracked reference that keeps an object alive. Converting a pointer to an integer and converting it back later is not a general safe storage technique. Follow the specific supported conversion patterns; `runtime.KeepAlive` does not make arbitrary pointer arithmetic or conversions valid. Zero-copy string or slice views also require that backing storage outlive every use and obey mutation restrictions. See the [`unsafe.Pointer` contracts](https://pkg.go.dev/unsafe#Pointer).

Avoid assuming Go layout is a wire format or native ABI. Check architecture, widths, padding, and endianness as relevant. A memory operation working on one platform or compiler is limited evidence for its validity.

## Make native ownership explicit

Specify who allocates, retains, mutates, and frees each native object or buffer, including error paths and callbacks. Match allocators and deallocators, and define callback lifetime, thread affinity, and shutdown ordering. Native memory and blocking calls have costs that may not appear in Go heap profiles or cancellation behavior.

Apply the [cgo pointer-passing rules](https://pkg.go.dev/cmd/cgo#hdr-Passing_pointers) to the exact memory involved. Go memory passed to C must meet pinning and reachable-pointer requirements. Implicit pinning during a call does not authorize indefinite retention. `runtime.Pinner` can support eligible retained memory, but is not blanket permission to store Go slice or string descriptors in C. A `runtime/cgo.Handle` can represent a Go value when an opaque handle is appropriate; arrange its deletion after all native uses end.

Account for native headers, libraries, compiler versions, build tags, target platforms, and cross-compilation constraints. Test supported boundaries and partial failures. Applicable pointer checks, race detection, and native sanitizers add evidence for exercised paths; none replaces a sound lifetime and ownership argument.
