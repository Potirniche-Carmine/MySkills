# Contributing

Keep these skills useful to an expert working on a concrete task.

## Writing guidance

- Put each skill in `skills/<skill-name>/`, with matching lowercase, hyphenated `name` metadata in `SKILL.md`.
- Describe the actual task that should trigger the skill. Keep broad instructional detail out of the discovery description.
- Explain the decision a recommendation improves, the conditions where it helps, and consequential tradeoffs. Separate correctness requirements from defaults and preferences.
- Preserve the user's scope and repository conventions. Avoid mandatory audits, blanket bans, repeated generic advice, and instructions that silently expand permissions.
- Put conditional detail in linked references. Add scripts or examples only when they materially improve reliability or understanding.
- Prefer primary sources for subtle technical contracts. Check APIs against the relevant version; current documentation alone does not establish availability on an older MSRV.

## Checking a change

Read the entrypoint as an agent would and follow its relevant references. Check frontmatter, relative links, metadata consistency, and unfinished placeholders. When Codex's `skill-creator` is available, run its bundled `scripts/quick_validate.py` against the changed skill directory.

For a substantive behavior change, try a realistic, narrowly scoped task and inspect the result. Check whether the guidance improves correctness or clarity, preserves useful existing design, and lets the agent finish without unnecessary ceremony. More complex skills can benefit from an independent evaluation in a temporary workspace. Document the scenario and outcome in the change description; structural validation alone does not establish quality.

Keep fixes supported by observed failures or concrete needs. Avoid growing a general prohibition from one unusual example.
