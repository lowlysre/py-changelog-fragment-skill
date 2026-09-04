---
name: python-changelog-fragments
description: "Use when a PR needs a changelog fragment for a Python project using antsibull-changelog or towncrier: installing the tool, writing the fragment file, naming it, and validating it before commit. Also use to troubleshoot fragment lint failures or `changelog build`/`towncrier build` errors."
allowed-tools: Bash(towncrier:*), Bash(antsibull-changelog:*), Bash(pip:*), Bash(pipx:*), Read, Glob, Grep, Write
---

# Python changelog fragments

Create and validate changelog fragments for Python projects that use `antsibull-changelog` (Ansible collections) or `towncrier` (most other Python packages). Both tools solve the same problem, a PR-scoped snippet that gets collected into the real changelog at release time, but they use different file formats and directory conventions.

## 1. Identify the tool in use

Check for these markers before doing anything else, they determine which reference file and commands apply:

| Marker | Tool |
|--------|------|
| `changelogs/config.yaml` or `changelogs/fragments/` | antsibull-changelog |
| `pyproject.toml` has a `[tool.towncrier]` section, or a `newsfragments/`/`changes/`/`changelog.d/` directory exists | towncrier |

If neither marker exists, ask which tool the project uses rather than guessing, the fragment format and directory are not interchangeable.

## 2. Load the tool-specific reference

- antsibull-changelog → read `references/antsibull-changelog.md` for installation, fragment YAML sections, naming, and `changelog build`/`lint` usage.
- towncrier → read `references/towncrier.md` for installation, fragment naming (`<issue>.<type>.md`), `pyproject.toml` config, and `towncrier build`/`check` usage.

## 3. Write the fragment

- Match the existing fragments in the project's fragment directory for tone and section/type choice, don't invent a new type.
- One fragment per user-facing change. A PR touching multiple unrelated behaviors gets multiple fragments.
- Write it as a changelog entry for the end user, not a commit message: describe the effect of the change, not the diff.
- Skip fragments for changes with no user-facing effect (internal refactors, test-only changes, CI config) unless the project's contributing guide says otherwise.

## 4. Validate before committing

Run the tool's lint/check command (see the reference file) and fix any reported errors: bad section name, malformed filename, or invalid YAML/RST/Markdown. Never commit a fragment that fails validation.

## Troubleshooting and best practices

For fragment lint failures, `changelog build` errors, missing sections, or PR-workflow conventions (squash-merge fragment loss, fragment-per-PR review gates), read `references/troubleshooting.md` and `references/best-practices.md`.
