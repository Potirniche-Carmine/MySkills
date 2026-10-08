# MySkills

Practical agent skills for better engineering decisions. Written for capable models and experienced developers, with focused reminders, explicit tradeoffs, and room for judgment.

## Philosophy

Skills should add useful context while preserving the engineer's ability to choose the right approach. Guidance here is grounded in the repository, proportional to the task, and specific about when a technique helps.

Correctness contracts are firm; design preferences remain context-dependent. Cloning, allocation, dependencies, dynamic dispatch, unsafe code, and abstractions all have legitimate uses. Their value depends on the problem, constraints, and evidence.

Each skill keeps its core guidance concise and places specialized detail in focused references. Agents can read what the task needs without turning a small change into an exhaustive audit. The goal is clearer reasoning, stronger contracts, and better code with an appropriate amount of complexity.

## Skills

| Skill | Description |
| --- | --- |
| [Rust Engineering](skills/rust-engineering/SKILL.md) | Repository-aware guidance for writing, refactoring, debugging, reviewing, and optimizing Rust. |

The Rust skill covers ownership and borrowing, API design and invariants, allocation and algorithm costs, errors and resource lifecycles, concurrency and async correctness, input and representation boundaries, unsafe and FFI contracts, Cargo features, and risk-directed verification.

## Install

Install with the [skills CLI](https://github.com/vercel-labs/skills):

```sh
npx skills add Potirniche-Carmine/MySkills
```
