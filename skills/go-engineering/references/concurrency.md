# Concurrency

Use this reference when changing shared access or the lifecycle and coordination of concurrent work.

## Own the lifetime

Identify who starts each goroutine, observes errors, requests shutdown, and waits for completion. A synchronous function often leaves these choices cleanly with its caller. Detached work needs an explicit longer-lived owner. A buffered channel can defer a blocked send without fixing a goroutine leak.

Propagate the caller's context through the operation and derive budgets where the operation understands its deadline. Release cancellation resources promptly. Context values are for request-scoped information crossing API boundaries, not hidden required dependencies or optional function parameters. Preserve a request's cancellation unless a deliberate handoff gives the work a different owner and budget.

Cancellation is cooperative. Ensure blocking operations have a cancellation or interruption path supported by their API; abandoning a goroutine waiting on an uncancellable call does not stop it. Distinguish cancellation from completion and from rollback of external effects.

Define shutdown ordering: stop admission, signal or drain work according to policy, wait, and release resources once users have finished. Channel closure belongs to the component that can establish that sends are finished; multiple producers need coordination.

## Bound admission and backlog

Limit active work, queued work, and retained bytes where each can grow independently. Starting one goroutine per item and acquiring a semaphore inside it may still create an unbounded queue of goroutines. Choose blocking backpressure, rejection, shedding, or another explicit overload policy. Make admission waits cancellation-aware when callers can abandon them.

When using `errgroup`, verify the repository's version and [its contract](https://pkg.go.dev/golang.org/x/sync/errgroup). A `WithContext` context is canceled on the first task error or when `Wait` returns, including successful completion; it is unsuitable for follow-up work after `Wait`. A zero group has no error-triggered cancellation. `SetLimit` bounds active group functions, and `Go` can block waiting for a slot without a context-aware admission API. Do not change the limit while functions are active. Nested submissions can deadlock a saturated group; use a coordination structure that fits the workload. `Wait` still requires all started functions to return.

## Synchronize the invariant

Protect the complete state transition, not just individual field accesses. A sequence of race-free reads and writes can still violate an invariant. Establish publication and ordering through the [Go memory model's synchronization guarantees](https://go.dev/ref/mem), rather than assumed scheduler timing.

Choose mutexes, channels, or atomics for the coordination they express. Keep lock scopes proportionate and examine callbacks, reentrancy, lock ordering, and blocking work inside them. Publishing an atomic pointer does not make reachable mutable data immutable or synchronize later mutations. An immutable snapshot needs a genuinely stable object graph.

`sync.Map` fits specialized access patterns; an ordinary map with a lock often makes related invariants easier to express. Use its documented operations rather than assuming a sequence of calls is atomic. For primitives and copying restrictions, consult the applicable [`sync` contracts](https://pkg.go.dev/sync).

## Pools are temporary reuse

`sync.Pool` may discard entries at any time, so it cannot enforce a resource inventory or replace explicit cleanup. After `Put`, relinquish use of the object and any aliases that would conflict with reuse. Reset necessary state before exposure to another consumer, consider secret retention, and bound unusually large buffers. Measure allocation savings against retained memory and reset costs before adding a pool.
