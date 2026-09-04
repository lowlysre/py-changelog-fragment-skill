# antsibull-changelog

`antsibull-changelog` generates changelogs for Ansible collections and Ansible core from per-PR YAML fragments. It reads `changelogs/config.yaml` and every fragment under `changelogs/fragments/`.

## Installation

```bash
pip install antsibull-changelog
```

For a one-off run without polluting the environment:

```bash
pipx run antsibull-changelog --help
```

Ansible collections typically pin it as a dev dependency in `test-requirements.txt` or `requirements-dev.txt`, check there first before installing globally.

## Project layout

```
changelogs/
  config.yaml           # sections, changelog filename, keep_fragments, etc.
  fragments/             # one YAML file per unreleased PR-scoped change
  changelog.yaml          # generated, do not hand-edit
CHANGELOG.rst              # generated, do not hand-edit
```

## Fragment format

A fragment is a YAML file, filename usually `<issue-or-pr-number>-<short-description>.yml`, e.g. `changelogs/fragments/1234-fix-connection-timeout.yml`. Content is a mapping of section name to a list of RST-formatted bullet strings:

```yaml
bugfixes:
  - lookup_plugin - fix connection timeout not being honored when set via environment variable (https://github.com/org/repo/issues/1234).
```

Common section keys (defined in `changelogs/config.yaml` under `sections` and `prelude_name`, check the project's config for the exact list):

| Section | Use for |
|---------|---------|
| `major_changes` | Breaking or headline changes |
| `minor_changes` | New features |
| `deprecated_features` | Features scheduled for removal |
| `removed_features` | Features removed in this release |
| `bugfixes` | Bug fixes |
| `known_issues` | Known issues without a fix yet |

A single fragment file can contain multiple sections and multiple bullets per section.

## Common commands

```bash
# Validate all fragments (run before every PR)
antsibull-changelog lint

# Preview the changelog that would be generated, without releasing
antsibull-changelog generate --reload-plugins

# Cut a release: consumes fragments, updates changelog.yaml and CHANGELOG.rst
antsibull-changelog release
```

`antsibull-changelog lint` is the command to run in CI and locally before committing a fragment, it catches invalid YAML, unknown section names, and RST formatting errors without touching any generated files.

## RST formatting notes

Fragment bullets are RST, not Markdown:

- Inline code: double backticks, `` ``module_name`` ``, not single backticks.
- Links: `` `text <https://url>`__ ``.
- Escape literal asterisks and backticks that aren't meant as RST markup.

## Best practices

- One fragment per PR is the norm; split it only if the PR bundles genuinely unrelated changes.
- Name the fragment after the issue or PR number so it's traceable, `changelogs/fragments/<number>-<slug>.yml`.
- Reference the issue/PR URL in the bullet text so users can find more context.
- Don't add a fragment for changes with no user-visible effect (CI, tests, internal refactors) unless the collection's contributing guide asks for one.
