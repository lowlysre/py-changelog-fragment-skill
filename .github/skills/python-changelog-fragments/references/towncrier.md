# towncrier

`towncrier` builds a changelog from per-PR fragment files in a single directory, configured via `[tool.towncrier]` in `pyproject.toml` (or a standalone `towncrier.toml`).

## Installation

```bash
pip install towncrier
```

Most projects pin it as a dev/test dependency, check `pyproject.toml`'s `[project.optional-dependencies]` or a `requirements-dev.txt` before installing globally.

## Project layout

The fragment directory name is project-specific, read the `directory` key in `[tool.towncrier]` first. Common names: `changes/`, `newsfragments/`, `changelog.d/`.

```
pyproject.toml            # [tool.towncrier] config: directory, filename, types
changelog.d/                # one fragment file per unreleased PR-scoped change
  1234.bugfix.md
CHANGELOG.md / .rst          # generated, do not hand-edit
```

## Fragment format

Filename convention: `<issue-number>.<type>.md` (or `.rst`, depending on the project's `template`/`filename` config), e.g. `changelog.d/1234.bugfix.md`. For PR-only fragments with no issue number, most projects accept `+<slug>.<type>.md`, e.g. `+fix-timeout.bugfix.md`.

Content is a single paragraph or a few lines of plain prose (Markdown or RST, matching the project's format), describing the change from the user's perspective. No YAML wrapper, no frontmatter.

Default `type` values (check `[[tool.towncrier.type]]` in `pyproject.toml` for the project's actual list, these are commonly overridden):

| Type | Use for |
|------|---------|
| `feature` | New features |
| `bugfix` | Bug fixes |
| `doc` | Documentation changes |
| `removal` | Deprecations and removals |
| `misc` | Everything else worth noting |

## Common commands

```bash
# Validate that the current branch adds a fragment (or is exempt)
towncrier check

# Preview the rendered changelog section without consuming fragments
towncrier build --draft

# Cut a release: consumes fragments, updates the changelog file
towncrier build --version <X.Y.Z>
```

`towncrier check` is the command CI usually runs on every PR, it fails if no fragment was added and the PR isn't tagged as exempt (e.g. via a `no-changelog-needed` label wired into the CI config, not into towncrier itself).

## Best practices

- One fragment per PR, named after the PR or issue number so `towncrier build` links it correctly.
- Write the fragment as the changelog entry itself, it's inserted into the final changelog verbatim (after rendering), not summarized further.
- Pick the `type` that matches the project's defined list; adding an undeclared type is a config-time error, not a lint warning.
- Skip fragments for changes with no user-facing effect, unless the project's `towncrier check` config requires one for every PR.
