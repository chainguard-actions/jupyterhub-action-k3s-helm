<!-- markdownlint-disable -->

# Hardening Report: jupyterhub--action-k3s-helm/v4.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **jupyterhub--action-k3s-helm/v4.1.0** was hardened automatically. 16 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Multiple `${{ inputs.* }}` expressions are directly interpolated inside `run:` shell command strings (sub-rule a), allowing an attacker who controls action inputs to inject arbitrary shell commands. Affected steps: (1) 'Validate input': `${{ inputs.k3s-version }}` and `${{ inputs.k3s-channel }}` in an `if` condition. (2) 'Setup k3s': `${{ inputs.metrics-enabled }}`, `${{ inputs.traefik-enabled }}`, `${{ inputs.docker-enabled }}`, `${{ inputs.extra-setup-args }}`, `${{ inputs.k3s-version }}`, `${{ inputs.k3s-channel }}` — most critically `${{ inputs.extra-setup-args }}` is passed unquoted directly as a positional argument to `sh -s -`. (3) 'Setup Helm': `HELM_VERSION="${{ inputs.helm-version }}"`. (4) 'Wait for calico': `${{ inputs.metrics-enabled }}` and `${{ inputs.traefik-enabled }}` in shell conditionals. All inputs should be moved to `env:` variables and referenced as double-quoted `"$VAR"` in the shell.

Locations:

- `action.yml:83`
- `action.yml:100`
- `action.yml:101`
- `action.yml:104`
- `action.yml:107`
- `action.yml:117`
- `action.yml:120`
- `action.yml:127`
- `action.yml:150`
- `action.yml:188`
- `action.yml:193`

### unsafe-shell (severity: high)

Two `run:` blocks pipe remote content directly to a shell interpreter without first downloading and verifying the script. (1) 'Setup k3s' step: `curl -sfL https://get.k3s.io | INSTALL_K3S_VERSION="..." INSTALL_K3S_CHANNEL="..." sh -s - ...` — remote content from get.k3s.io is piped directly to `sh`. (2) 'Setup Helm' step: `curl -sf ${HELM_INSTALL_SCRIPT} | DESIRED_VERSION="${HELM_VERSION}" bash` — remote content from raw.githubusercontent.com is piped directly to `bash`. Scripts should be downloaded to a temporary file, their integrity verified (e.g., checksum), and then executed separately.

Locations:

- `action.yml:120`
- `action.yml:156`

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

- `action.yml:182`

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

**Fixes applied:** script-injection, static-inline-injection, unsafe-shell

**Notes:**

Fixed all script-injection and static-inline-injection findings by moving all ${{ inputs.* }} expressions from run: shell blocks into env: blocks in each affected step (Validate input, Setup k3s, Setup Helm, Wait for calico). Shell scripts now reference these as plain $VAR_NAME environment variables. The optional extra-setup-args input uses ${EXTRA_SETUP_ARGS:+"$EXTRA_SETUP_ARGS"} to avoid passing an empty positional argument. Fixed both unsafe-shell findings by downloading scripts to temp files (/tmp/k3s-install.sh and /tmp/helm-install.sh) before executing them, eliminating the curl|sh and curl|bash pipe patterns.

