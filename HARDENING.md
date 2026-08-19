<!-- markdownlint-disable -->

# Hardening Report: raven-actions--actionlint/v2.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **raven-actions--actionlint/v2.2.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The 'Install dependencies' run: block in action.yml directly interpolates a ${{ }} expression inside the shell command string: `run: npm install --prefix "${{ runner.temp }}/actionlint-action" ...`. Any ${{ ... }} expression interpolated directly into a run: shell command is a script-injection risk because the value is substituted by the YAML template engine before the shell ever sees it, bypassing shell quoting. The safe alternative is to use the $RUNNER_TEMP environment variable instead of ${{ runner.temp }}.

Locations:

- `action.yml:215`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed the script injection vulnerability in the 'Install dependencies' step of action.yml (line 215). Replaced `${{ runner.temp }}` with `$RUNNER_TEMP` in the npm install command. The `$RUNNER_TEMP` environment variable is a built-in GitHub Actions runner environment variable that is equivalent to `runner.temp` but is expanded by the shell rather than the YAML template engine, eliminating the script injection risk.

