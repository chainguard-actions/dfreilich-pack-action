<!-- markdownlint-disable -->

# Hardening Report: dfreilich--pack-action/v2.0.12

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **dfreilich--pack-action/v2.0.12** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a) violation: The `run-pack` step directly interpolates `${{ inputs.args }}` inside a `run:` shell command (`pack ${{ inputs.args }}`). Before the shell ever sees the command, GitHub Actions substitutes the raw value of `inputs.args` into the script string. An attacker who controls this input can inject shell metacharacters (`;`, `&&`, `|`, `$(...)`, backticks, etc.) to execute arbitrary commands on the runner. The fix is to pass the value through an `env:` variable and double-quote it: set `PACK_ARGS: ${{ inputs.args }}` in an `env:` block and run `pack "$PACK_ARGS"` in the shell.

Locations:

- `action.yml:33`

### unpinned-uses (severity: high)

The composite action step `uses: buildpacks/github-actions/setup-pack@v4.1.0` references a mutable version tag (`@v4.1.0`) rather than a pinned 40-character commit SHA. If the tag is moved (intentionally or via a supply-chain compromise), the action will silently execute different code. Pin to a full SHA, e.g. `uses: buildpacks/github-actions/setup-pack@<40-char-sha> # v4.1.0`.

Locations:

- `action.yml:22`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.args }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:34`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, static-inline-injection

**Notes:**

1. Pinned `buildpacks/github-actions/setup-pack@v4.1.0` to full SHA `b3038dd2ada5d9ce26d9bdd0c4f81473297e4379` (tag preserved as comment). 2. Fixed script injection by moving `${{ inputs.args }}` to an `env:` block as `PACK_ARGS`, then using xargs-based tokenization into a bash array before passing to `pack` — this handles the list-type nature of the `args` input while preventing shell metacharacter injection.

