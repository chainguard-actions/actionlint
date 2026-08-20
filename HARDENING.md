<!-- markdownlint-disable -->

# Hardening Report: raven-actions--actionlint/v2.1.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **raven-actions--actionlint/v2.1.2** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Two `run:` blocks in ci.yml directly interpolate `${{ steps.actionlint.outputs.* }}` expressions inside shell commands. Per the check rules, `steps.*.outputs.*` is a workflow-controllable context and any `${{ ... }}` directly inside a `run:` script is a script-injection finding (sub-rule a). In the first 'actionlint Outputs' step (dog-food job), lines like `echo "Used actionlint version ${{ steps.actionlint.outputs.version-semver }}"` inject step output values directly into the shell. In the second 'actionlint Outputs' step (dog-food-matrix job), the same pattern occurs plus `exit ${{ steps.actionlint.outputs.exit-code }}` which injects an exit code directly into the shell command. These should be passed via `env:` variables and referenced as `$ENV_VAR` in the script.

Locations:

- `.github/workflows/ci.yml:67`
- `.github/workflows/ci.yml:101`

### missing-permissions (severity: medium)

ci.yml has no top-level `permissions:` key and neither the `dog-food` job nor the `dog-food-matrix` job defines a job-level `permissions:` block. This means the workflow runs with the default (potentially broad) permissions granted by the repository settings.

Locations:

- `.github/workflows/ci.yml:1`

### unpinned-uses (severity: high)

Three workflow files reference reusable workflows using a mutable `@main` branch ref instead of a pinned 40-character commit SHA. This exposes the workflows to supply-chain attacks if the referenced repository is compromised or the branch is force-pushed. Failing references: linter.yml uses `raven-actions/.workflows/.github/workflows/__linter.yml@main`; release-draft.yml uses `raven-actions/.workflows/.github/workflows/__release-draft.yml@main`; release-publish.yml uses `raven-actions/.workflows/.github/workflows/__release-publish.yml@main`.

Locations:

- `.github/workflows/linter.yml:18`
- `.github/workflows/release-draft.yml:11`
- `.github/workflows/release-publish.yml:11`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, missing-permissions, unpinned-uses

**Notes:**

1. script-injection (ci.yml lines 67, 101): Moved all ${{ steps.actionlint.outputs.* }} expressions from both 'actionlint Outputs' run: blocks into step-level env: blocks. Shell scripts now reference plain $ACTIONLINT_* environment variables. The `exit ${{ steps.actionlint.outputs.exit-code }}` in the matrix job is now `exit "$ACTIONLINT_EXIT_CODE"`. 2. missing-permissions (ci.yml): Added `permissions: {}` at the top-level workflow scope since the workflow only uses local actions and needs no GitHub API permissions. 3. unpinned-uses (linter.yml line 18, release-draft.yml line 11, release-publish.yml line 11): Pinned all three `raven-actions/.workflows` reusable workflow references from `@main` to the resolved commit SHA `@1f992c9cb996a0672a65cf952ff539a870389b56 # main`.

