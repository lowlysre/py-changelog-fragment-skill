# py-changelog-fragment-skill

An agentic skill for creating and validating Python changelog fragments in a PR workflow, covering `antsibull-changelog` and `towncrier`.

The skill lives at [`skills/python-changelog-fragments/SKILL.md`](skills/python-changelog-fragments/SKILL.md). It identifies which tool a project uses, then loads the matching reference doc in [`references/`](skills/python-changelog-fragments/references/) for installation, fragment format, naming, validation commands, troubleshooting, and best practices.

Copy `skills/python-changelog-fragments/` into whatever skill directory your tool expects (`.github/skills/`, `.claude/skills/`, `.agents/skills/`, etc.).

CI validates the skill's schema with [agnix](https://github.com/agent-sh/agnix) on every push and pull request (see [`.github/workflows/agnix.yml`](.github/workflows/agnix.yml)).
