# Troubleshooting

## antsibull-changelog

**`antsibull-changelog lint` fails with "Invalid section"**
The fragment uses a section key not defined in `changelogs/config.yaml`'s `sections` list. Open the config and use one of the declared keys (typically `bugfixes`, `minor_changes`, `major_changes`, `deprecated_features`, `removed_features`, `known_issues`).

**`antsibull-changelog lint` fails with a YAML parse error**
Usually an unquoted string with a colon, an unescaped `*` at the start of a line (RST emphasis marker colliding with YAML), or mismatched indentation between the section key and its list items. Fragments are YAML, quote any bullet text containing `:` and check two-space list indentation.

**Fragment doesn't show up in `antsibull-changelog generate`**
`generate --reload-plugins` only picks up fragments in `changelogs/fragments/`, not `changelogs/fragments/.git*` or subdirectories. Confirm the file sits directly in that directory and has a `.yml`/`.yaml` extension.

**Fragment file disappeared after merge**
`antsibull-changelog release` deletes consumed fragments and folds them into `changelogs/changelog.yaml`. If a fragment vanished without a release happening, check whether a squash-merge or rebase workflow dropped the commit that added it, see "Squash-merge fragment loss" below.

## towncrier

**`towncrier check` fails with "Nothing to check"** or the reverse, "missing a newsfragment"
The PR's changed files don't include anything under the configured fragment `directory`, or the fragment's issue number doesn't match anything `towncrier check` expects (it diffs against the base branch to find added files). Confirm the fragment file is new relative to the PR's base, not just newly renamed or moved.

**`towncrier build` errors with "Invalid type"**
The fragment's `.type` suffix isn't declared under `[[tool.towncrier.type]]` in `pyproject.toml`. List the declared types and rename the fragment to match one.

**Duplicate or out-of-order entries in the built changelog**
towncrier sorts by type, then by fragment filename. If ordering looks wrong, check the `type` order declared in the config, it controls section order, not addition order.

## PR-workflow issues (all tools)

**Squash-merge fragment loss**
If the repo squash-merges PRs, a fragment added in an early commit and later renamed/deleted within the same PR still lands correctly, since squash collapses to the final tree. The actual failure mode is the opposite: someone adds a fragment, force-pushes to fix an unrelated review comment, and the fragment file is fine, but a *rebase onto a stale base* before merge can silently drop it if the rebase had a conflict resolved incorrectly. Diff the final PR against its base to confirm the fragment file is still present before merge.

**CI passes locally but fails in CI (or vice versa)**
Confirm the CI job runs the same tool version as installed locally, `antsibull-changelog --version` / `towncrier --version` / `scriv --version`. Section/type lists and lint rules have changed across major versions.

**Fragment review gate blocks an internal-only PR**
If a PR genuinely has no user-facing effect, use the project's documented exemption (a `no-changelog-needed`-style CI label or a project-specific towncrier `check` exemption) rather than adding a placeholder fragment. Adding a "no changes" fragment pollutes the real changelog.

## scriv

**`scriv collect` picks up a fragment that's still a placeholder**
`scriv create` pre-fills every category as a commented-out template; if a section gets uncommented without replacing `A bullet item for this fragment. EDIT ME!`, `collect` folds the placeholder text straight into the changelog. There's no built-in lint step to catch this, review the fragment's diff for leftover placeholder text before merging.

**scriv reads the wrong settings**
Settings cascade across `setup.cfg`, `tox.ini`, `pyproject.toml`, and `changelog.d/scriv.ini`, with later files overriding earlier ones. If a setting doesn't seem to apply, check whether a `changelog.d/scriv.ini` is silently overriding the project-level config.

**`pyproject.toml`-based config isn't picked up**
Reading `[tool.scriv]` from `pyproject.toml` requires `tomli` (or Python 3.11+'s built-in `tomllib`). Install with `pip install scriv[toml]` (or `uv add --dev "scriv[toml]"`) on older Python versions.
