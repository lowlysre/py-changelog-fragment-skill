---
name: python-changelog-fragments
description: "Use when a PR needs a changelog fragment for a Python project using antsibull-changelog, towncrier, or scriv: installing the tool, writing the fragment file, naming it, and validating it before commit. Also use to troubleshoot fragment lint failures or `changelog build`/`towncrier build`/`scriv collect` errors."
allowed-tools: Bash(towncrier:*), Bash(antsibull-changelog:*), Bash(scriv:*), Bash(pip:*), Bash(pipx:*), Bash(uv:*), Bash(uvx:*), Read, Glob, Grep, Write
---

# Python changelog fragments

Create and validate changelog fragments for Python projects that use `antsibull-changelog` (Ansible collections), `towncrier`, or `scriv` (most other Python packages). All three solve the same problem, a PR-scoped snippet that gets collected into the real changelog at release time, but they use different file formats, config sections, and directory conventions.

## 1. Identify the tool in use

Check for these markers before doing anything else, they determine which reference file and commands apply:

| Marker | Tool |
|--------|------|
| `changelogs/config.yaml` or `changelogs/fragments/` | antsibull-changelog |
| `pyproject.toml` has a `[tool.towncrier]` section | towncrier |
| `pyproject.toml` has a `[tool.scriv]` section, or `setup.cfg`/`tox.ini` has a `[scriv]` section | scriv |

`towncrier` and `scriv` both commonly use a directory literally named `changelog.d/`, so disambiguate by the config section name, not the directory. If neither marker exists, ask which tool the project uses rather than guessing, the fragment format and directory are not interchangeable.

## 2. Load the tool-specific reference

- antsibull-changelog → read `references/antsibull-changelog.md` for installation, fragment YAML sections, naming, and `changelog build`/`lint` usage.
- towncrier → read `references/towncrier.md` for installation, fragment naming (`<issue>.<type>.md`), `pyproject.toml` config, and `towncrier build`/`check` usage.
- scriv → read `references/scriv.md` for installation, the `scriv create` workflow, category sections, and `scriv collect` usage.

## 3. Write the fragment

- Match the existing fragments in the project's fragment directory for tone and section/type choice, don't invent a new type.
- One fragment per user-facing change. A PR touching multiple unrelated behaviors gets multiple fragments.
- Write it as a changelog entry for the end user, not a commit message: describe the effect of the change, not the diff.
- State the concrete effect, not a vague quality claim: say what broke and under what condition, or what's now possible, instead of reaching for words like "robust", "seamless", "improved", or "enhanced" that carry no information on their own.
- Don't hedge ("aims to fix", "should resolve"). State what the change does. If there's a genuine known limitation, say so plainly rather than leaving it out.
- Skip connective filler ("Additionally,", "It's worth noting that", "Note that") and AI-flavored framing ("This release introduces..."). Start the sentence at the subject.
- Skip fragments for changes with no user-facing effect (internal refactors, test-only changes, CI config) unless the project's contributing guide says otherwise.
- End the fragment file with a trailing newline (a blank line after the last line of content), as is conventional for text files.

## 4. Validate before committing

Run the tool's lint/check command (see the reference file) and fix any reported errors: bad section name, malformed filename, or invalid YAML/RST/Markdown. Never commit a fragment that fails validation.

## Troubleshooting and best practices

For fragment lint failures, `changelog build` errors, missing sections, or PR-workflow conventions (squash-merge fragment loss, fragment-per-PR review gates), read `references/troubleshooting.md` and `references/best-practices.md`.
