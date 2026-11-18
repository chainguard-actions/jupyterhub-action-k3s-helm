<!-- markdownlint-disable -->

# Hardening Report: jupyterhub--action-k3s-helm/v4.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **jupyterhub--action-k3s-helm/v4.1.0** was hardened automatically. 23 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a): step 'Validate input' puts expressions directly into the run script: `if [[ -n "${{ inputs.k3s-version }}" && -n "${{ inputs.k3s-channel }}" ]]; then`

Locations:

- `action.yml:86`

### script-injection (severity: high)

Rule (a): step 'Setup k3s' puts inputs.k3s-version, inputs.k3s-channel, inputs.metrics-enabled, inputs.traefik-enabled, inputs.docker-enabled and inputs.extra-setup-args directly into the run script. The worst case is the unquoted `${{ inputs.extra-setup-args }} \` passed to the install command. Others include `echo "::group::Setup k3s ${{ inputs.k3s-version }}${{ inputs.k3s-channel }}"` and `INSTALL_K3S_VERSION="${{ inputs.k3s-version }}"`.

Locations:

- `action.yml:104`
- `action.yml:121`
- `action.yml:124`
- `action.yml:130`

### script-injection (severity: high)

Rule (a): step 'Setup Helm' puts an expression directly into the run script: `HELM_VERSION="${{ inputs.helm-version }}"`. Rule (b): it also expands a variable unquoted: `curl -sf ${HELM_INSTALL_SCRIPT} | ...`.

Locations:

- `action.yml:182`

### script-injection (severity: high)

Rule (a): step 'Wait for calico, coredns, metrics server, traefik' puts expressions directly into the run script: `if [[ "${{ inputs.metrics-enabled }}" == true ]]; then` and `if [[ "${{ inputs.traefik-enabled }}" == true ]]; then`

Locations:

- `action.yml:220`

### unsafe-shell (severity: high)

Remote script piped straight into a shell: `curl -sfL https://get.k3s.io | INSTALL_K3S_VERSION=... sh -s - ...`

Locations:

- `action.yml:124`

### unsafe-shell (severity: high)

Remote script piped straight into a shell: `curl -sf ${HELM_INSTALL_SCRIPT} | DESIRED_VERSION="${HELM_VERSION}" bash`. The script comes from the mutable helm master branch.

Locations:

- `action.yml:189`

### github-env-injection (severity: high)

Step 'Prepare a kubeconfig' writes the inherited process env var $HOME to $GITHUB_ENV with no sanitization (`printf '%s' | tr -d '\n\r'`): `echo "KUBECONFIG=$HOME/.kube/config" >> $GITHUB_ENV`

Locations:

- `action.yml:144`

### github-env-injection (severity: high)

Step 'Set version output' writes the inherited process env var $HOME to $GITHUB_OUTPUT with no sanitization: `echo "kubeconfig=$HOME/.kube/config" >> $GITHUB_OUTPUT`

Locations:

- `action.yml:197`

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

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action.yml without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/action.yml b/action.yml
index e89c6db..facde20 100644
--- a/action.yml
+++ b/action.yml
@@ -7,20 +7,16 @@
 #
 # Reference: https://docs.github.com/en/actions/creating-actions/metadata-syntax-for-github-actions
 #
----
 name: K3S with Calico and Helm
 description: |
   Install Kubernetes (K3S) and Helm 3.
   Includes Calico network plugin for network policy support.
-
 branding:
   icon: server
   color: purple
-
 # Copied from
 # https://github.com/jupyterhub/zero-to-jupyterhub-k8s/blob/08c13609c1d0c6cb07d45d49d0a876100cf941eb/ci/common
 # Thanks @consideratio !
-
 inputs:
   k3s-version:
     description: K3S version (https://github.com/rancher/k3s/releases)
@@ -50,7 +46,6 @@ inputs:
     description: Addition arguments to be passed to the K3S setup script
     required: false
     default: ""
-
 outputs:
   kubeconfig:
     description: Path to kubeconfig file
@@ -67,10 +62,26 @@ outputs:
   helm-version:
     description: "Installed helm version, such as v3.13.0"
     value: "${{ steps.set-output.outputs.helm-version }}"
-
 runs:
   using: "composite"
   steps:
     # https://rancher.com/docs/k3s/latest/en/installation/install-options/how-to-flags/
     #
     # NOTE: k3s has a Network Policy controller called kube-router, but it is
@@ -81,15 +92,17 @@ runs:
     #       ref: https://github.com/rancher/k3s/issues/947#issuecomment-627641541
     #
     - name: Validate input
+      env:
+        INPUT_K3S_VERSION: ${{ inputs.k3s-version }}
+        INPUT_K3S_CHANNEL: ${{ inputs.k3s-channel }}
       run: |
         echo "::group::Validate input"
-        if [[ -n "${{ inputs.k3s-version }}" && -n "${{ inputs.k3s-channel }}" ]]; then
+        if [[ -n "${INPUT_K3S_VERSION}" && -n "${INPUT_K3S_CHANNEL}" ]]; then
           echo "k3s-version and k3s-channel must not be specified simultaneously!"
           exit 1
         fi
         echo "::endgroup::"
       shell: bash
-
     # NOTE: We apply a workaround as of version 3.0.1 by passing
     #       --egress-selector-mode=disabled by default as not doing so following
     #       modern versions of k3s has led to issues with `kubectl exec` and
@@ -99,16 +112,23 @@ runs:
     #       https://github.com/k3s-io/k3s/issues/5633
     #       and https://github.com/jupyterhub/action-k3s-helm/issues/59.
     #
-    - name: Setup k3s ${{ inputs.k3s-version }}${{ inputs.k3s-channel }}
+    - name: Setup k3s
+      env:
+        INPUT_K3S_VERSION: ${{ inputs.k3s-version }}
+        INPUT_K3S_CHANNEL: ${{ inputs.k3s-channel }}
+        INPUT_METRICS_ENABLED: ${{ inputs.metrics-enabled }}
+        INPUT_TRAEFIK_ENABLED: ${{ inputs.traefik-enabled }}
+        INPUT_DOCKER_ENABLED: ${{ inputs.docker-enabled }}
+        INPUT_EXTRA_SETUP_ARGS: ${{ inputs.extra-setup-args }}
       run: |
-        echo "::group::Setup k3s ${{ inputs.k3s-version }}${{ inputs.k3s-channel }}"
-        if [[ "${{ inputs.metrics-enabled }}" != true ]]; then
+        echo "::group::Setup k3s ${INPUT_K3S_VERSION}${INPUT_K3S_CHANNEL}"
+        if [[ "${INPUT_METRICS_ENABLED}" != true ]]; then
           k3s_disable_metrics="--disable metrics-server"
         fi
-        if [[ "${{ inputs.traefik-enabled }}" != true ]]; then
+        if [[ "${INPUT_TRAEFIK_ENABLED}" != true ]]; then
           k3s_disable_traefik="--disable traefik"
         fi
-        if [[ "${{ inputs.docker-enabled }}" == true ]]; then
+        if [[ "${INPUT_DOCKER_ENABLED}" == true ]]; then
           k3s_docker=--docker
         fi
         # We want to provide a new default value for the --egress-selector-mode
@@ -118,20 +138,32 @@ runs:
         # Details about the option available at
         # https://docs.k3s.io/installation/network-options#control-plane-egress-selector-configuration.
         #
-        if [[ "${{ inputs.extra-setup-args }}" != *--egress-selector-mode* ]]; then
+        if [[ "${INPUT_EXTRA_SETUP_ARGS}" != *--egress-selector-mode* ]]; then
           default_extra_setup_args=--egress-selector-mode=disabled
         fi
-        curl -sfL https://get.k3s.io | INSTALL_K3S_VERSION="${{ inputs.k3s-version }
... (truncated)
```

Locations:

- `action.yml`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection, unsafe-shell, github-env-injection, prior-fix

**Notes:**

Moved all inputs.* expressions in run: scripts into step env: blocks (Validate input, Setup k3s, Setup Helm, Wait step); removed expressions from step names. extra-setup-args (upstream pasted inline as an expression) is now tokenized with the quote-aware xargs split into a bash array and passed as "${extra_args[@]}". k3s installer is downloaded to a temp file and run with 'sh "$file" <args>' (dropped the '-s -' shell option terminator). Helm installer is pinned to helm/helm commit 5a09b86219f949e5653eecac91e6bb8de930f0f9 instead of master, downloaded to a file, then run with bash. KUBECONFIG/kubeconfig writes to GITHUB_ENV/GITHUB_OUTPUT now strip newlines and quote the target file.

