# Best practices for high-quality fragments

## Write for the changelog reader, not the reviewer

A fragment is read months later by someone deciding whether to upgrade, not by the PR reviewer who already has the diff. State the user-visible effect: what broke, what's new, what changed behavior. Skip implementation detail the diff already shows.

- Good: `lookup_plugin - fix connection timeout not being honored when set via environment variable (https://github.com/org/repo/issues/1234).`
- Bad: `Refactored _get_timeout() to check env vars before falling back to config.`

## Say the specific thing, not the vague adjective

Words like "robust", "seamless", "powerful", "efficient", or "enhanced" describe a feeling, not a fact, so they're the first thing to cut. Replace each with the concrete detail it's standing in for:

- Instead of "improved performance", say what changed: "cuts cold-start from 8s to 1.2s".
- Instead of "more robust error handling", say what it now handles: "retries on connection reset instead of failing immediately".
- Instead of "enhanced logging", say what's new: "adds the request ID to every log line".

This isn't about tone policing, it's that a vague adjective forces the reader to guess at the actual change, and a changelog exists so they don't have to.

## State it plainly, don't hedge it

"Aims to fix" or "should resolve" reads as uncertainty the maintainer didn't intend. If the fix is confirmed, say what it does: "fixes X". If there's a genuine known gap (works around the common case but not an edge case, say), state that plainly instead of a vague qualifier or silence, a reader deciding whether to upgrade needs the real limitation, not a hedge.

## One fragment per user-facing change

A PR that fixes a bug and adds an unrelated feature gets two fragments (or two entries in one antsibull-changelog fragment file, one per section). Don't bundle unrelated changes into a single bullet just to save a file.

## Name fragments so they're traceable

antsibull-changelog and towncrier use the issue or PR number in the filename by convention (`1234-fix-timeout.yml` for antsibull-changelog, `1234.bugfix.md` for towncrier). This lets a reader trace the changelog entry back to its discussion. For PR-only fragments with no tracked issue, towncrier's `+slug.type.md` convention still keeps entries unique without a real issue number; antsibull-changelog projects generally still expect a PR number even without a tracked issue. scriv names fragments for you via `scriv create` (date, username, branch), so there's nothing to choose, just don't rename the generated file.

## Match the project's existing tone and section choice

Read a handful of recent fragments (`git log -p -- changelogs/fragments/`, the towncrier fragment directory, or `changelog.d/` for scriv) before writing a new one. Consistency across entries matters more than any individual fragment being clever.

## Add the fragment in the same PR as the change

Fragments reviewed separately from the change they describe drift out of sync, or get forgotten entirely. Add the fragment in the same commit/PR, and treat a CI fragment-check failure as a blocking review comment, not a follow-up.

## Don't hand-edit generated output

Never edit `CHANGELOG.rst`, `CHANGELOG.md`, or `changelogs/changelog.yaml` directly, they're regenerated from fragments on release and any manual edit is lost. Fix the fragment instead.

## Validate before pushing, not after CI fails

Run `antsibull-changelog lint`, `towncrier check`, or (for scriv) a self-review against the fragment's diff before opening the PR, scriv has no built-in lint command, so replacing placeholder text is on you. These checks run in under a second and catch the most common mistakes (bad section/type name, malformed filename, invalid markup, leftover placeholder text) before a CI round-trip.

## Skip fragments deliberately, not by omission

If a change has no user-facing effect, use the project's documented exemption path instead of silently skipping the fragment step, a reviewer shouldn't have to guess whether a missing fragment was intentional.
