<!-- markdownlint-disable -->

# Hardening Report: dfreilich--pack-action/v2.1.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **dfreilich--pack-action/v2.1.1** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The `run-pack` step directly interpolates the user-controlled input `${{ inputs.args }}` into a shell `run:` block: `pack ${{ inputs.args }}`. An attacker who controls the `args` input can inject arbitrary shell metacharacters (`;`, `|`, `$(...)`, etc.) to execute arbitrary commands on the runner. The value must be passed via an `env:` variable and double-quoted in the shell instead: set `env: PACK_ARGS: ${{ inputs.args }}` and use `pack "$PACK_ARGS"` in the run block.

Locations:

- `action.yml:34`

### unpinned-uses (severity: high)

The `install` step references `buildpacks/github-actions/setup-pack@v4.6.0`, which uses a mutable version tag (`@v4.6.0`) rather than a pinned 40-character commit SHA. A tag can be moved to point to a different (potentially malicious) commit, enabling supply-chain attacks. Pin to a full SHA, e.g. `buildpacks/github-actions/setup-pack@<40-char-sha> # v4.6.0`.

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

1. Pinned `buildpacks/github-actions/setup-pack@v4.6.0` to full SHA `918407dc3eb8c209c5b69902b5024ebcb63fe3b5` with tag preserved as comment.
2. Fixed script injection in the `run-pack` step: moved `${{ inputs.args }}` to an `env:` variable `PACK_ARGS`, then used xargs-based tokenization into a bash array (`args=()`) to properly split the argument list while preserving quoting semantics. This prevents shell metacharacter injection while correctly handling multi-word pack arguments like `build myapp --builder heroku/buildpacks:20`.

