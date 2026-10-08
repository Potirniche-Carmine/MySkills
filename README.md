# MySkills

Practical agent skills for better engineering decisions. Written for capable models and experienced developers, with focused reminders, explicit tradeoffs, and room for judgment.

## Skills

| Skill | Purpose |
| --- | --- |
| [Rust Engineering](skills/rust-engineering/SKILL.md) | Write, refactor, debug, review, and optimize Rust with attention to ownership, contracts, failure semantics, and cost. |

## Approach

Skills should improve decisions without taking over the task. Guidance here is repository-aware, proportional to risk, and specific about when a technique helps. Correctness contracts are firm; preferences remain context-dependent.

The Rust skill covers ownership and borrowing, invariant-preserving APIs, allocation and algorithm costs, resource and async lifecycles, hostile inputs, unsafe and FFI boundaries, Cargo features, and targeted verification. It avoids universal bans on cloning, allocation, dependencies, dynamic dispatch, or unsafe code. Specialized topics live in references so ordinary edits only need the short entrypoint and relevant details.

## Use with Codex

For a local installation, clone this repository and link the skill into your personal skills directory. Run the following from the repository root, with no existing `rust-engineering` installation at the destination:

```sh
mkdir -p "$HOME/.agents/skills"
ln -s "$PWD/skills/rust-engineering" "$HOME/.agents/skills/rust-engineering"
```

The symlink follows updates to this checkout. To pin a separate copy instead, copy the whole `skills/rust-engineering` directory, including its references and metadata. A project can keep the same folder under `.agents/skills/` in its own repository.

Codex supports these discovery locations and symlinks. If the skill does not appear, restart Codex. See the [official skill documentation](https://learn.chatgpt.com/docs/build-skills) for discovery and installation details.

Invoke it explicitly with a concrete task:

```text
Use $rust-engineering to fix this borrow-checker error while preserving the public API.
```

```text
Use $rust-engineering to review this Rust diff for correctness and unnecessary complexity.
```

```text
Use $rust-engineering to investigate allocation pressure in this parser using the existing benchmarks.
```

Automatic selection remains enabled when the task matches the skill's description. Other agents that support `SKILL.md` can use the instructions and references; installation and discovery depend on the host.

## Repository layout

```text
skills/
└── rust-engineering/
    ├── SKILL.md
    ├── agents/
    │   └── openai.yaml
    └── references/
        ├── ownership-and-apis.md
        ├── performance.md
        ├── failure-and-boundaries.md
        ├── unsafe-and-ffi.md
        └── cargo-and-verification.md
```

`SKILL.md` contains discovery metadata, core guidance, and links explaining when to read each reference. `agents/openai.yaml` supplies Codex display metadata. The Rust skill has no required scripts, external services, or runtime dependencies.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). Favor a small improvement tied to a real engineering decision over additional rules for hypothetical situations.
