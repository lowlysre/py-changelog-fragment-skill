# Best practices for high-quality fragments

## Write for the changelog reader, not the reviewer

A fragment is read months later by someone deciding whether to upgrade, not by the PR reviewer who already has the diff. State the user-visible effect: what broke, what's new, what changed behavior. Skip implementation detail the diff already shows.

- Good: `lookup_plugin - fix connection timeout not being honored when set via environment variable (https://github.com/org/repo/issues/1234).`
- Bad: `Refactored _get_timeout() to check env vars before falling back to config.`

## One fragment per user-facing change

A PR that fixes a bug and adds an unrelated feature gets two fragments (or two entries in one antsibull-changelog fragment file, one per section). Don't bundle unrelated changes into a single bullet just to save a file.

## Name fragments so they're traceable

Both tools use the issue or PR number in the filename by convention (`1234-fix-timeout.yml` for antsibull-changelog, `1234.bugfix.md` for towncrier). This lets a reader trace the changelog entry back to its discussion. For PR-only fragments with no tracked issue, towncrier's `+slug.type.md` convention still keeps entries unique without a real issue number; antsibull-changelog projects generally still expect a PR number even without a tracked issue.

## Match the project's existing tone and section choice

Read a handful of recent fragments (`git log -p -- changelogs/fragments/` or the towncrier fragment directory) before writing a new one. Consistency across entries matters more than any individual fragment being clever.

## Add the fragment in the same PR as the change

Fragments reviewed separately from the change they describe drift out of sync, or get forgotten entirely. Add the fragment in the same commit/PR, and treat a CI fragment-check failure as a blocking review comment, not a follow-up.

## Don't hand-edit generated output

Never edit `CHANGELOG.rst`, `CHANGELOG.md`, or `changelogs/changelog.yaml` directly, they're regenerated from fragments on release and any manual edit is lost. Fix the fragment instead.

## Validate before pushing, not after CI fails

Run `antsibull-changelog lint` or `towncrier check` locally before opening the PR. Both commands run in under a second and catch the most common mistakes (bad section/type name, malformed filename, invalid markup) before a CI round-trip.

## Skip fragments deliberately, not by omission

If a change has no user-facing effect, use the project's documented exemption path instead of silently skipping the fragment step, a reviewer shouldn't have to guess whether a missing fragment was intentional.
