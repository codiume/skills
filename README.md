# Skills

Reusable skills for AI agents, installable via the [skills.sh](https://www.skills.sh) CLI.

## Available skills

- **[preflight](skills/preflight)** — Pre-production code review against a user-chosen healthy point. Use it to review a branch before shipping or identify blockers before a PR lands.
- **[lockin](skills/lockin)** — Lock in on a checkable goal and work autonomously until it's provably met or a cap is hit. Use it for tasks with a clear finish line, like getting tests passing or a build green.

## Installation

Install a skill with the `skills` CLI:

```sh
npx skills add codiume/skills --skill preflight
npx skills add codiume/skills --skill lockin
```

## Usage

Once installed, your AI agent picks up each skill based on its `description` in `SKILL.md`. You can also invoke them directly by asking for the skill by name (e.g. "run /preflight on this branch").

## Resources

- [skills.sh documentation](https://www.skills.sh/docs)

[![skills.sh](https://skills.sh/b/codiume/skills)](https://skills.sh/codiume/skills)
