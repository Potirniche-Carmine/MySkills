# Performance

Use when runtime, build, or memory cost matters to the requested change. Match the depth of measurement to the claim.

## Allocation and retention are different

A pointer does not guarantee heap allocation, and a value does not guarantee stack allocation. Escape behavior depends on context and the compiler; use escape diagnostics to explain a measured cost rather than coding around remembered rules.

Small slices or substrings can retain large backing storage. Capacity clipping does not release that storage; an intentional copy can reduce live memory even though it adds an allocation. Inspect aliases and pointer-containing elements beyond a shortened slice's length when retention persists. Clearing unused slots can release referents but must respect other views of the same array.

Preallocate from credible bounded estimates. Reuse buffers when the savings matter, but cap retention of unusually large allocations. A zero-allocation operation that holds much more memory or contends on a shared pool can be a regression. Read [pool ownership](concurrency.md#pools-are-temporary-reuse) if introducing pooling.

## Algorithms and layout

Choose collections for size, ordering, locality, and update patterns. Contiguous storage and fewer pointers can reduce indirection and scanning costs, but large value copies and mutation semantics still matter. Prefer readable loops or supported iterator APIs according to control flow and the repository's version.

Follow how often work happens. Repeated membership scans, sorting inside loops, repeated conversions, concatenation, or accidental reconstruction of lookup tables may dominate individual allocations. An intermediate collection can be wasteful or can provide useful ownership and reuse; inspect its purpose.

## Measure representative workloads

Use benchmarks with realistic input sizes, distributions, concurrency, and lifetimes, and prevent benchmark setup or eliminated work from obscuring the operation. Use supported testing APIs and report allocations where relevant. Compare repeated samples under comparable conditions, using statistical tools such as `benchstat` when useful; a single number is weak evidence.

Choose CPU, allocation, live-heap, mutex, or block profiles according to the symptom. Allocation profiles and live-heap profiles answer different questions. Execution traces help explain scheduling, blocking, and latency interactions. Consider build time and binary size when generated code, generics, dependencies, or native tooling changes materially.

Profile-guided optimization can help with a representative CPU profile and a repeatable build process. Verify improvements across relevant workloads and refresh profiles as code or traffic changes; follow [Go's PGO guidance](https://go.dev/doc/pgo) for the supported toolchain.

## Cooperate with the collector

Consider allocation rate, live memory, pointer density, and scanning work together. Reduce unnecessary object lifetimes and bound caches or reuse before tuning runtime controls. GC tuning trades CPU and memory; measure the service's actual latency and throughput needs.

`GOMEMLIMIT` and `debug.SetMemoryLimit` provide a soft Go runtime memory limit, not a hard process or container limit. Leave headroom for memory outside runtime accounting, including native allocations and mappings, and for workload variation. A limit below the live working set can cause heavy collection without making the data disappear. See the [Go GC guide](https://go.dev/doc/gc-guide).
