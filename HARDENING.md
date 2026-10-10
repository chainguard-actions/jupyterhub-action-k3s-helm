<!-- markdownlint-disable -->

# Hardening Report: jupyterhub--action-k3s-helm/v4.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **jupyterhub--action-k3s-helm/v4.0.1** was hardened automatically. 17 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Multiple `run:` blocks in action.yml directly interpolate `${{ inputs.* }}` expressions inside shell command strings (sub-rule a). The GitHub Actions template engine substitutes these values before the shell ever sees them, allowing an attacker who controls the inputs to inject arbitrary shell commands.

**Validate input step** (line ~83): `if [[ -n "${{ inputs.k3s-version }}" && -n "${{ inputs.k3s-channel }}" ]]`

**Setup k3s step** (lines ~96–113): `${{ inputs.k3s-version }}`, `${{ inputs.k3s-channel }}`, `${{ inputs.metrics-enabled }}`, `${{ inputs.traefik-enabled }}`, `${{ inputs.docker-enabled }}`, and `${{ inputs.extra-setup-args }}` are all interpolated directly into shell. The `extra-setup-args` input is especially dangerous as it is passed unquoted directly to the k3s install script: `${{ inputs.extra-setup-args }}`.

**Setup Helm step** (line ~157): `DESIRED_VERSION="${{ inputs.helm-version }}" bash` — the helm version input is interpolated directly into the shell command.

**Wait for calico step** (lines ~175, ~183): `${{ inputs.metrics-enabled }}` and `${{ inputs.traefik-enabled }}` are interpolated directly into shell conditionals.

Fix: Move all `inputs.*` values into `env:` variables and reference them as quoted shell variables (e.g., `"$INPUT_K3S_VERSION"`) inside `run:` blocks.

Locations:

- `action.yml:83`
- `action.yml:96`
- `action.yml:99`
- `action.yml:101`
- `action.yml:103`
- `action.yml:107`
- `action.yml:113`
- `action.yml:157`
- `action.yml:175`
- `action.yml:183`

### unsafe-shell (severity: high)

Two `run:` blocks pipe remote content directly to a shell interpreter without first downloading to a file for inspection:

1. **Setup k3s step**: `curl -sfL https://get.k3s.io | INSTALL_K3S_VERSION="${{ inputs.k3s-version }}" INSTALL_K3S_CHANNEL="${{ inputs.k3s-channel }}" sh -s - ...` — remote script from get.k3s.io is piped directly to `sh`. If the remote URL is compromised or redirected, arbitrary code executes on the runner.

2. **Setup Helm step**: `curl -sf https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | DESIRED_VERSION="${{ inputs.helm-version }}" bash` — remote script from GitHub (on the `master` branch, a mutable ref) is piped directly to `bash`.

Fix: Download the script to a temporary file first, verify its integrity (e.g., checksum), then execute it separately.

Locations:

- `action.yml:113`
- `action.yml:157`

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

Fixed all script-injection and static-inline-injection findings by moving all ${{ inputs.* }} expressions from run: blocks into env: blocks, then referencing them as safe shell variables ($INPUT_K3S_VERSION, $INPUT_K3S_CHANNEL, $INPUT_METRICS_ENABLED, $INPUT_TRAEFIK_ENABLED, $INPUT_DOCKER_ENABLED, $INPUT_EXTRA_SETUP_ARGS, $INPUT_HELM_VERSION). Fixed both unsafe-shell findings by downloading the k3s install script (get.k3s.io) and Helm install script (raw.githubusercontent.com) to temp files via mktemp before executing them separately, eliminating the curl-pipe-to-shell pattern. The extra-setup-args input is tokenized into a bash array using the xargs-based pattern to handle quoted arguments correctly. The -- separator from the original sh -s - invocation was dropped since we now execute a file directly rather than reading from stdin.

