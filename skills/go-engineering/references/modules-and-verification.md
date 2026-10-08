# Modules and Verification

Use the sections relevant to dependency changes, version-sensitive behavior, and the risks introduced by the task.

## Dependency value and the module graph

Reuse standard-library and existing-package capabilities where they fit. A focused dependency can be more reliable than custom parsing, cryptography, or concurrency machinery. Evaluate maintenance, transitive exposure, supported versions and platforms, native tooling, and relevant build, binary, or runtime costs in proportion to the dependency's role.

Minimum version selection selects the highest required version for a module path in the applicable graph, not the lowest published version or automatically the latest release. Replacements, exclusions, workspaces, and graph pruning affect interpretation. `go.sum` records content checksums; it does not select versions or establish publisher trust. A module in the graph, a compiled package, and code linked into a binary are different scopes. Consult the [module reference](https://go.dev/ref/mod) and use `go list`, `go mod graph`, or `go mod why` when the distinction matters.

`go mod tidy` can change requirements and checksums beyond the active platform or tags because it examines a broader set of packages, including tests. Run it in the intended module with the appropriate toolchain and review the diff. Avoid unrelated upgrades, workspace replacements, or version changes while fixing an isolated issue.

## Modernize against the supported version

Check the module language version and applicable per-file build constraints before changing loop capture patterns. Go 1.22 introduced per-iteration variables for loop declarations under the new language semantics; assignment to an already-declared variable still shares that variable. See [the loop-variable change](https://go.dev/blog/loopvar-preview).

Timer stop/reset and collection behavior changed in Go 1.23, with behavior also depending on the main module's version and compatibility settings. Avoid mechanically adding or removing old timer-draining patterns without checking the program's effective behavior. See [the timer transition](https://go.dev/wiki/Go123Timer).

Verify availability of library, iterator, analyzer, and testing APIs against supported toolchains. Preserve serialization behavior, nil/empty distinctions, and public error contracts during modernization unless their migration is part of the task.

## Tests that discriminate

Choose observable contracts: boundary values, properties, malformed input, partial reads and writes, failure after partial progress, cancellation, shutdown, and retry behavior. An aliasing test should mutate a retained or original view and observe the promised independence. A concurrency test should establish the relevant ordering rather than rely on an arbitrary sleep.

Use deterministic synchronization or supported facilities such as [`testing/synctest`](https://pkg.go.dev/testing/synctest) where their model fits the operation. They do not model every external system or explore every interleaving. Test goroutine termination explicitly when leak risk matters, and ensure test cleanup joins work even on failure.

## Repository-driven checks

Use `gofmt`, relevant `go vet` checks and established analyzers, and behavioral tests for affected packages. Expand to dependents or broader suites when interfaces or shared code change. Account for supported build tags, platforms, cgo settings, and module or workspace boundaries; `go test ./...` in one module is not evidence for every repository configuration.

Use race detection for exercised shared-state paths, fuzzing for parsers or state machines, and vulnerability scanning when dependencies or exposure warrant it. [Govulncheck](https://go.dev/doc/security/vuln/) helps distinguish affected dependencies from potentially reachable vulnerable code; results depend on the analysis mode and build configuration and do not settle exploitability by themselves.

Select available checks that address the actual risk rather than installing a large toolchain by default. Report commands, configurations, and meaningful limits. A race-free test does not prove deadlock freedom, fuzzing covers only explored inputs, and a successful build on a newer compiler does not establish minimum-version support.
