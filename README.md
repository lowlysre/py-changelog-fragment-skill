# py-changelog-fragment-skill

An agentic skill for creating and validating Python changelog fragments in a PR workflow, covering `antsibull-changelog` and `towncrier`.

The skill lives at [`.github/skills/python-changelog-fragments/SKILL.md`](.github/skills/python-changelog-fragments/SKILL.md). It identifies which tool a project uses, then loads the matching reference doc for installation, fragment format, naming, validation commands, troubleshooting, and best practices.

CI validates the skill's schema with [agnix](https://github.com/agent-sh/agnix) on every push and pull request (see [`.github/workflows/agnix.yml`](.github/workflows/agnix.yml)).
