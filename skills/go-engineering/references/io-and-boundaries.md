# I/O and Boundaries

Read the sections matching the failure, resource, or external-input boundary being changed.

## Error identity and progress

Return errors that support the caller's recovery decisions and add context where the operation is understood. Use `errors.Is` and `errors.As` for supported identities and types instead of matching message text. Wrapping with `%w` exposes a cause to callers and can create a compatibility commitment; translate errors deliberately when implementation details should remain private. See [Go's error-wrapping guidance](https://go.dev/blog/go1.13-errors).

Avoid logging the same error at every layer. Preserve useful progress information when an operation can partially succeed, and do not imply rollback merely because an error is returned. Panic and recovery policy should match the repository's failure boundaries. Recovery in a caller cannot catch a panic in another goroutine; recovering without restoring state may hide corruption.

## Completion is more than cleanup

`defer` runs at function exit, not the end of a loop iteration or lexical block. A small helper or explicit close can keep resource lifetimes bounded inside loops. Install cleanup after acquisition succeeds and consider when deferred arguments are evaluated.

Determine which `Close`, `Flush`, or `Commit` errors affect the result. Closing an underlying file does not flush a separate buffered writer, and closing a resource does not imply a transaction committed or data became durable. Preserve the primary failure while reporting material cleanup failures in the repository's established form. Garbage collection and finalizers are not timely resource-completion mechanisms.

## Respect I/O contracts

An `io.Reader` can return bytes and an error together. Process the bytes before handling the error, and interpret EOF according to framing: normal stream completion differs from a truncated fixed-size record. A zero-byte read with nil error is not EOF. Writers can report partial progress; inspect counts and errors or use a suitable standard helper, without replaying already-written bytes. See the [`io` contracts](https://pkg.go.dev/io).

Use `io.ReadFull`, copying helpers, or buffered APIs where their exact semantics fit. Check `Scanner.Err`, choose a bounded scanner token limit appropriate to the format, and copy transient scanner or buffer views when retaining them. Avoid unlimited reads or drains of untrusted streams; enforce size limits and detect truncation rather than silently treating a capped prefix as the whole input.

## Network and database lifecycles

Reuse concurrency-safe clients, transports, and database pools as intended. Configure operation budgets and relevant connection or server deadlines for the actual protocol. Bound active requests and pool use together so work does not deadlock while holding scarce connections.

Close response bodies on every acquired-response path. Where connection reuse depends on consuming the body, respect a bounded work budget rather than draining arbitrary data indefinitely. Observe context cancellation and the API's distinction between header, body, and total-request deadlines.

Close database rows and check iteration errors. Keep operations intended for a transaction on that transaction, handle commit failure, and use rollback as appropriate cleanup. Context cancellation does not prove that a remote side effect failed; retry according to idempotency and transaction guarantees. Plan graceful shutdown for in-flight operations and separately owned long-lived connections.

## Untrusted data and diagnostics

Bound input bytes, nesting depth, decompression, work, and concurrent processing at the relevant boundary. Check size arithmetic for overflow and conversions for representability before allocating or slicing. Use cryptographic randomness for security-sensitive tokens and parameters for SQL values; dynamic identifiers need a separate constrained design.

Path cleaning or a check followed by an ordinary open may not prevent traversal through symlinks or filesystem races. Use a suitable traversal-resistant API such as `os.Root` when supported, and understand its guarantees and platform limits. See [Go's traversal-resistant file APIs](https://go.dev/blog/osroot).

Keep structured logs and errors useful without exposing credentials, query parameters, payloads, or sensitive fields through formatting, serialization, or debug endpoints. Expensive log arguments are evaluated before a logging call can discard them; guard costly work where it matters. Redact at meaningful boundaries and avoid unnecessary copies or extended retention of secrets.
