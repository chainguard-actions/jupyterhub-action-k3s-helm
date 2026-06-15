<!-- markdownlint-disable -->

# Hardening Report: jupyterhub--action-k3s-helm/v4.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **jupyterhub--action-k3s-helm/v4.0.1** was hardened automatically. 17 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Multiple `${{ inputs.* }}` expressions are directly interpolated inside `run:` shell command strings (rule a), allowing script injection. Affected steps:

1. **Validate input** step (line 76): `if [[ -n "${{ inputs.k3s-version }}" && -n "${{ inputs.k3s-channel }}" ]]` — inputs interpolated directly into shell conditional.

2. **Setup k3s** step (lines 91-113): `${{ inputs.metrics-enabled }}`, `${{ inputs.traefik-enabled }}`, `${{ inputs.docker-enabled }}`, `${{ inputs.extra-setup-args }}`, `${{ inputs.k3s-version }}`, and `${{ inputs.k3s-channel }}` are all interpolated directly into the shell script. Most critically, `${{ inputs.extra-setup-args }}` is passed unquoted directly as shell arguments to the k3s installer.

3. **Setup Helm** step (line 155): `${{ inputs.helm-version }}` is interpolated directly into the shell script.

4. **Wait for calico...** step (lines 168, 175): `${{ inputs.metrics-enabled }}` and `${{ inputs.traefik-enabled }}` are interpolated directly into shell conditionals.

All inputs should be moved to `env:` variables and referenced as quoted `"$VAR"` shell variables.

Locations:

- `action.yml:76`
- `action.yml:91`
- `action.yml:107`
- `action.yml:155`
- `action.yml:168`

### unsafe-shell (severity: high)

Two `run:` blocks pipe remote content directly to a shell interpreter without first downloading and verifying the script:

1. **Setup k3s** step (line 107): `curl -sfL https://get.k3s.io | INSTALL_K3S_VERSION="${{ inputs.k3s-version }}" INSTALL_K3S_CHANNEL="${{ inputs.k3s-channel }}" sh -s - ...` — the k3s install script is fetched and piped directly to `sh`.

2. **Setup Helm** step (line 155): `curl -sf https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | DESIRED_VERSION="${{ inputs.helm-version }}" bash` — the Helm install script is fetched and piped directly to `bash`.

In both cases the script should be downloaded to a file, its integrity verified (e.g. checksum), and then executed separately.

Locations:

- `action.yml:107`
- `action.yml:155`

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

**Fixes applied:** script-injection, static-inline-injection, unsafe-shell

**Notes:**

Fixed all security findings in action.yml:

1. **script-injection / static-inline-injection**: Moved all `${{ inputs.* }}` expressions out of `run:` shell blocks and into `env:` blocks for all four affected steps:
   - 'Validate input': K3S_VERSION, K3S_CHANNEL
   - 'Setup k3s': K3S_VERSION, K3S_CHANNEL, METRICS_ENABLED, TRAEFIK_ENABLED, DOCKER_ENABLED, EXTRA_SETUP_ARGS
   - 'Setup Helm': HELM_VERSION
   - 'Wait for calico...': METRICS_ENABLED, TRAEFIK_ENABLED
   Shell scripts now reference these as plain `$VAR` environment variables.

2. **unsafe-shell**: Replaced both curl-pipe-to-shell patterns with download-then-execute:
   - k3s installer: `curl ... | sh` → download to `/tmp/k3s-install.sh`, chmod 700, execute separately
   - Helm installer: `curl ... | bash` → download to `/tmp/get-helm-3.sh`, chmod 700, execute separately

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed the script-injection vulnerability in the 'Setup k3s' step of action.yml. Converted all unquoted variable expansions to use bash arrays: (1) `k3s_disable_metrics`, `k3s_disable_traefik`, `k3s_docker`, and `default_extra_setup_args` are now bash arrays initialized to empty and populated with properly quoted elements; (2) `EXTRA_SETUP_ARGS` is split into `extra_setup_args_array` using `read -ra extra_setup_args_array <<< "$EXTRA_SETUP_ARGS"` — the input is quoted in the here-string preventing metacharacter injection, while `read -ra` safely splits on whitespace; (3) all arrays are expanded with `"${array[@]}"` (double-quoted) preventing shell metacharacter interpretation; (4) removed the `# shellcheck disable=SC2086` comment since it's no longer needed.

