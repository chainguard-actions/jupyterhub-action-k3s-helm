<!-- markdownlint-disable -->

# Hardening Report: jupyterhub--action-k3s-helm/v4.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **jupyterhub--action-k3s-helm/v4.0.1** was hardened automatically. 17 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Multiple `${{ inputs.* }}` expressions are interpolated directly inside `run:` shell command strings without going through env vars, allowing an attacker to inject arbitrary shell commands via action inputs. Affected lines include:
- "Validate input" step: `if [[ -n "${{ inputs.k3s-version }}" && -n "${{ inputs.k3s-channel }}" ]]` — inputs interpolated directly into the shell string.
- "Setup k3s" step: `${{ inputs.metrics-enabled }}`, `${{ inputs.traefik-enabled }}`, `${{ inputs.docker-enabled }}`, `${{ inputs.extra-setup-args }}`, `${{ inputs.k3s-version }}`, `${{ inputs.k3s-channel }}` all interpolated directly into the shell script. Notably `${{ inputs.extra-setup-args }}` is passed completely unquoted as a positional argument to `sh`.
- "Setup Helm" step: `${{ inputs.helm-version }}` interpolated directly into the shell string.
- "Wait for calico..." step: `${{ inputs.metrics-enabled }}` and `${{ inputs.traefik-enabled }}` interpolated directly into the shell string.
All of these should be moved to `env:` block variables and then referenced as double-quoted shell variables (e.g., `"$INPUT_VAR"`).

Locations:

- `action.yml:84`
- `action.yml:92`
- `action.yml:95`
- `action.yml:98`
- `action.yml:101`
- `action.yml:107`
- `action.yml:113`
- `action.yml:115`
- `action.yml:143`
- `action.yml:144`
- `action.yml:162`
- `action.yml:169`

### unsafe-shell (severity: high)

Two `run:` blocks pipe remote content directly to a shell interpreter without first saving to a file for inspection:
1. `curl -sfL https://get.k3s.io | INSTALL_K3S_VERSION="${{ inputs.k3s-version }}" INSTALL_K3S_CHANNEL="${{ inputs.k3s-channel }}" sh -s - ...` — the k3s install script is fetched and piped directly to `sh`. Additionally, `${{ inputs.extra-setup-args }}` is passed unquoted as a direct shell argument, compounding the injection risk.
2. `curl -sf https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | DESIRED_VERSION="${{ inputs.helm-version }}" bash` — the Helm install script is fetched and piped directly to `bash`.
In both cases the script should be downloaded to a temporary file, its integrity verified (e.g., checksum), and then executed separately.

Locations:

- `action.yml:113`
- `action.yml:144`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.k3s-version }}" appears directly in run: block of step "Validate input"; move to env: map

Locations:

- `action.yml:86`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.k3s-channel }}" appears directly in run: block of step "Validate input"; move to env: map

Locations:

- `action.yml:86`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.k3s-version }}" appears directly in run: block of step "Setup k3s ${{ inputs.k3s-version }}${{ inputs.k3s-channel }}"; move to env: map

Locations:

- `action.yml:104`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.k3s-channel }}" appears directly in run: block of step "Setup k3s ${{ inputs.k3s-version }}${{ inputs.k3s-channel }}"; move to env: map

Locations:

- `action.yml:104`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.metrics-enabled }}" appears directly in run: block of step "Setup k3s ${{ inputs.k3s-version }}${{ inputs.k3s-channel }}"; move to env: map

Locations:

- `action.yml:105`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.traefik-enabled }}" appears directly in run: block of step "Setup k3s ${{ inputs.k3s-version }}${{ inputs.k3s-channel }}"; move to env: map

Locations:

- `action.yml:108`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.docker-enabled }}" appears directly in run: block of step "Setup k3s ${{ inputs.k3s-version }}${{ inputs.k3s-channel }}"; move to env: map

Locations:

- `action.yml:111`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.extra-setup-args }}" appears directly in run: block of step "Setup k3s ${{ inputs.k3s-version }}${{ inputs.k3s-channel }}"; move to env: map

Locations:

- `action.yml:121`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.k3s-version }}" appears directly in run: block of step "Setup k3s ${{ inputs.k3s-version }}${{ inputs.k3s-channel }}"; move to env: map

Locations:

- `action.yml:124`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.k3s-channel }}" appears directly in run: block of step "Setup k3s ${{ inputs.k3s-version }}${{ inputs.k3s-channel }}"; move to env: map

Locations:

- `action.yml:124`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.extra-setup-args }}" appears directly in run: block of step "Setup k3s ${{ inputs.k3s-version }}${{ inputs.k3s-channel }}"; move to env: map

Locations:

- `action.yml:130`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.helm-version }}" appears directly in run: block of step "Setup Helm ${{ inputs.helm-version }}"; move to env: map

Locations:

- `action.yml:188`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.helm-version }}" appears directly in run: block of step "Setup Helm ${{ inputs.helm-version }}"; move to env: map

Locations:

- `action.yml:189`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.metrics-enabled }}" appears directly in run: block of step "Wait for calico, coredns, metrics server, traefik"; move to env: map

Locations:

- `action.yml:220`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.traefik-enabled }}" appears directly in run: block of step "Wait for calico, coredns, metrics server, traefik"; move to env: map

Locations:

- `action.yml:226`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unsafe-shell, static-inline-injection

**Notes:**

Fixed all findings in action.yml:
1. script-injection / static-inline-injection: Moved all ${{ inputs.* }} expressions from run: blocks into env: blocks (INPUT_K3S_VERSION, INPUT_K3S_CHANNEL, INPUT_METRICS_ENABLED, INPUT_TRAEFIK_ENABLED, INPUT_DOCKER_ENABLED, INPUT_EXTRA_SETUP_ARGS, INPUT_HELM_VERSION). Shell scripts now reference these as double-quoted $INPUT_VAR_NAME variables.
2. unsafe-shell: Fixed both curl-pipe-to-shell patterns: (a) k3s install script is now downloaded to a mktemp file and executed with 'sh "$K3S_INSTALL_SCRIPT" -' (dropped the '--' shell option terminator); (b) Helm install script is now downloaded to a mktemp file and executed with 'DESIRED_VERSION="$INPUT_HELM_VERSION" bash "$HELM_INSTALL_SCRIPT"'.
3. extra-setup-args list input: Tokenized using the xargs-based array pattern (with guard for empty value) to preserve quote-aware word splitting while preventing injection.

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed two script injection findings in action.yml:
1. Line 107 (Setup k3s step): Replaced `echo "::group::Setup k3s $INPUT_K3S_VERSION$INPUT_K3S_CHANNEL"` with `printf '::group::Setup k3s %s%s\n' "$INPUT_K3S_VERSION" "$INPUT_K3S_CHANNEL"`. Using printf with %s format specifiers prevents bash from evaluating command substitutions ($(…) or backticks) that may be embedded in the caller-controlled input values.
2. Line 152 (Setup Helm step): Replaced `echo "::group::Setup Helm $INPUT_HELM_VERSION"` with `printf '::group::Setup Helm %s\n' "$INPUT_HELM_VERSION"` for the same reason.

