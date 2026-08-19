<!-- markdownlint-disable -->

# Hardening Report: jupyterhub--action-k3s-helm/v4.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **jupyterhub--action-k3s-helm/v4.1.0** was hardened automatically. 20 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Multiple `run:` blocks in action.yml directly interpolate `${{ inputs.* }}` expressions inside shell commands, violating rule (a). This allows an attacker who controls the inputs to inject arbitrary shell commands.

1. 'Validate input' step: `if [[ -n "${{ inputs.k3s-version }}" && -n "${{ inputs.k3s-channel }}" ]]` — inputs interpolated directly in shell.
2. 'Setup k3s' step: `echo "::group::Setup k3s ${{ inputs.k3s-version }}${{ inputs.k3s-channel }}"`, `if [[ "${{ inputs.metrics-enabled }}" != true ]]`, `if [[ "${{ inputs.traefik-enabled }}" != true ]]`, `if [[ "${{ inputs.docker-enabled }}" == true ]]`, `if [[ "${{ inputs.extra-setup-args }}" != *--egress-selector-mode* ]]`, and critically `${{ inputs.extra-setup-args }}` passed as an unquoted positional argument to `sh -s -`.
3. 'Setup Helm' step: `HELM_VERSION="${{ inputs.helm-version }}"`.
4. 'Wait for calico' step: `if [[ "${{ inputs.metrics-enabled }}" == true ]]` and `if [[ "${{ inputs.traefik-enabled }}" == true ]]`.

Locations:

- `action.yml:75`
- `action.yml:91`
- `action.yml:93`
- `action.yml:96`
- `action.yml:99`
- `action.yml:107`
- `action.yml:115`
- `action.yml:160`
- `action.yml:163`
- `action.yml:168`

### script-injection (severity: high)

Multiple `run:` blocks in .github/workflows/test_k3s.yml directly interpolate `${{ steps.k3s.outputs.* }}` and `${{ matrix.* }}` expressions inside shell commands, violating rule (a). These values flow through YAML template substitution before the shell processes them, enabling injection of shell metacharacters.

Examples:
- `echo "kubeconfig=${{ steps.k3s.outputs.kubeconfig }}"`
- `if [[ -z "${{ steps.k3s.outputs.kubeconfig }}" ]]`
- `if [[ "${{ steps.k3s.outputs.k3s-version }}" != v* ]]`
- `if [[ "$enabled" != "${{ matrix.metrics-enabled }}" ]]`
- `if [[ "$enabled" != "${{ matrix.traefik-enabled }}" ]]`

Locations:

- `.github/workflows/test_k3s.yml:68`
- `.github/workflows/test_k3s.yml:69`
- `.github/workflows/test_k3s.yml:70`
- `.github/workflows/test_k3s.yml:71`
- `.github/workflows/test_k3s.yml:72`
- `.github/workflows/test_k3s.yml:78`
- `.github/workflows/test_k3s.yml:83`
- `.github/workflows/test_k3s.yml:88`
- `.github/workflows/test_k3s.yml:93`
- `.github/workflows/test_k3s.yml:98`
- `.github/workflows/test_k3s.yml:115`
- `.github/workflows/test_k3s.yml:122`

### unsafe-shell (severity: high)

Two `run:` blocks in action.yml pipe remote content directly to a shell interpreter without first downloading to a file:

1. K3s installation: `curl -sfL https://get.k3s.io | INSTALL_K3S_VERSION="${{ inputs.k3s-version }}" INSTALL_K3S_CHANNEL="${{ inputs.k3s-channel }}" sh -s -` — remote script piped directly to `sh`.
2. Helm installation: `curl -sf ${HELM_INSTALL_SCRIPT} | DESIRED_VERSION="${HELM_VERSION}" bash` — remote script piped directly to `bash`. The URL is constructed from a user-controlled input (`inputs.helm-version` determines which script URL is used).

Locations:

- `action.yml:115`
- `action.yml:168`

### unpinned-uses (severity: high)

Multiple `uses:` references in workflow files use mutable tags or version strings instead of pinned 40-character commit SHAs, making them vulnerable to supply-chain attacks if the referenced tag is moved or the repository is compromised.

- `.github/workflows/test_k3s.yml`: `uses: actions/checkout@v6` (tag `v6`)
- `.github/workflows/test_k3s.yml`: `uses: jupyterhub/action-k8s-namespace-report@v1` (tag `v1`)
- `.github/workflows/readme_example.yml`: `uses: jupyterhub/action-k3s-helm@v4` (tag `v4`)
- `.github/workflows/release_updates.yml`: `uses: Actions-R-Us/actions-tagger@v2` (tag `v2`)

Locations:

- `.github/workflows/test_k3s.yml:57`
- `.github/workflows/test_k3s.yml:152`
- `.github/workflows/readme_example.yml:14`
- `.github/workflows/release_updates.yml:17`

### missing-permissions (severity: medium)

Two workflow files have no top-level `permissions:` key and no job-level `permissions:` key on any of their jobs. Without explicit permissions, workflows run with the repository's default token permissions (which may be `write-all` for older repositories), granting unnecessarily broad access.

- `.github/workflows/test_k3s.yml`: jobs `test_install_k3s` and `status_all` have no `permissions:` block, and there is no top-level `permissions:` key.
- `.github/workflows/readme_example.yml`: job `k8s-test` has no `permissions:` block, and there is no top-level `permissions:` key.

Locations:

- `.github/workflows/test_k3s.yml:1`
- `.github/workflows/readme_example.yml:1`

### github-env-injection (severity: high)

The 'Setup k3s' step in action.yml writes `${{ inputs.k3s-version }}` and `${{ inputs.k3s-channel }}` directly as environment variable values passed to the `sh` invocation on the same line as the curl pipe. While these are not written to `$GITHUB_ENV`/`$GITHUB_OUTPUT`/`$GITHUB_PATH` directly, the 'Prepare a kubeconfig' step writes `KUBECONFIG=$HOME/.kube/config` to `$GITHUB_ENV` without sanitization. More critically, the 'Setup k3s' step passes `${{ inputs.extra-setup-args }}` as an unsanitized, unquoted argument directly into the shell command line, which can inject newlines and other control characters. The value is attacker-controlled and is interpolated before the shell sees it, bypassing any shell quoting.

Locations:

- `action.yml:115`
- `action.yml:131`

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

**Fixes applied:** script-injection, unsafe-shell, unpinned-uses, missing-permissions, github-env-injection, static-inline-injection

**Notes:**

Fixed all findings across action.yml and .github/workflows/*.yml:

1. action.yml - script-injection/static-inline-injection: Moved all ${{ inputs.* }} expressions from run: blocks to env: blocks in all four affected steps (Validate input, Setup k3s, Setup Helm, Wait for calico/coredns/metrics/traefik).

2. action.yml - unsafe-shell: K3s install script now downloaded to /tmp/k3s-install.sh before execution; Helm install script downloaded to /tmp/helm-install.sh before execution. No more curl|sh or curl|bash patterns.

3. action.yml - github-env-injection: KUBECONFIG value sanitized with tr -d '\n\r' before writing to $GITHUB_ENV. extra-setup-args tokenized via xargs into a bash array to prevent injection.

4. test_k3s.yml - script-injection: Moved ${{ steps.k3s.outputs.* }} and ${{ matrix.* }} expressions from run: blocks to env: blocks.

5. Unpinned actions pinned to full SHAs: actions/checkout@v6→d23441a48e516b6c34aea4fa41551a30e30af803, jupyterhub/action-k8s-namespace-report@v1→ccc0e3c37860245f3906e3857df01b0a53f9c3f8, jupyterhub/action-k3s-helm@v4→502a9ff4d816dad543e359971fe2c3128d0f4c6e, Actions-R-Us/actions-tagger@v2→330ddfac760021349fef7ff62b372f2f691c20fb.

6. Missing permissions: Added permissions: {} at top-level and contents: read at job-level to test_k3s.yml and readme_example.yml.

