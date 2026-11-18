<!-- markdownlint-disable -->

# Hardening Report: jupyterhub--action-k3s-helm/v4.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **jupyterhub--action-k3s-helm/v4.0.1** was hardened automatically. 24 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a): the 'Validate input' step puts expressions straight into the run: script: `if [[ -n "${{ inputs.k3s-version }}" && -n "${{ inputs.k3s-channel }}" ]]`.

Locations:

- `action.yml:87`

### script-injection (severity: high)

Rule (a): the 'Setup k3s' step puts expressions straight into the run: script, including `echo "::group::Setup k3s ${{ inputs.k3s-version }}${{ inputs.k3s-channel }}"`, `[[ "${{ inputs.metrics-enabled }}" != true ]]`, `[[ "${{ inputs.traefik-enabled }}" != true ]]`, `[[ "${{ inputs.docker-enabled }}" == true ]]`, `[[ "${{ inputs.extra-setup-args }}" != *--egress-selector-mode* ]]`, `INSTALL_K3S_VERSION="${{ inputs.k3s-version }}" INSTALL_K3S_CHANNEL="${{ inputs.k3s-channel }}"`, and an unquoted `${{ inputs.extra-setup-args }}` passed as an argument to the install script. Any of these inputs can inject shell commands.

Locations:

- `action.yml:105`
- `action.yml:122`
- `action.yml:125`
- `action.yml:131`

### script-injection (severity: high)

Rule (a): the 'Setup Helm' step puts expressions straight into the run: script: `echo "::group::Setup Helm ${{ inputs.helm-version }}"` and `DESIRED_VERSION="${{ inputs.helm-version }}" bash`.

Locations:

- `action.yml:189`
- `action.yml:190`

### script-injection (severity: high)

Rule (a): the 'Wait for calico, coredns, metrics server, traefik' step puts expressions straight into the run: script: `if [[ "${{ inputs.metrics-enabled }}" == true ]]` and `if [[ "${{ inputs.traefik-enabled }}" == true ]]`.

Locations:

- `action.yml:222`
- `action.yml:228`

### unsafe-shell (severity: high)

Remote content is piped straight to a shell: `curl -sfL https://get.k3s.io | INSTALL_K3S_VERSION=... sh -s - ...` in the 'Setup k3s' step.

Locations:

- `action.yml:125`

### unsafe-shell (severity: high)

Remote content is piped straight to a shell: `curl -sf https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | DESIRED_VERSION=... bash` in the 'Setup Helm' step. The script comes from the mutable master branch.

Locations:

- `action.yml:190`

### github-env-injection (severity: high)

The 'Prepare a kubeconfig' step writes `echo "KUBECONFIG=$HOME/.kube/config" >> $GITHUB_ENV`. $HOME is inherited from the calling environment, so the calling workflow controls it, and the step does not run it through `printf '%s' ... | tr -d '\n\r'` before the write.

Locations:

- `action.yml:145`

### github-env-injection (severity: high)

The 'Set version output' step writes values to $GITHUB_OUTPUT with no newline stripping. These are the inherited $HOME (`kubeconfig=$HOME/.kube/config`) and output from commands whose versions are chosen by inputs (k3s --version, helm version, the contents of calico.yaml). None of them go through `printf '%s' ... | tr -d '\n\r'` first.

Locations:

- `action.yml:198`

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

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed action.yml without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/action.yml b/action.yml
index 5c9f27e..a46a532 100644
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
+        if [[ -n "$INPUT_K3S_VERSION" && -n "$INPUT_K3S_CHANNEL" ]]; then
           echo "k3s-version and k3s-channel must not be specified simultaneously!"
           exit 1
         fi
         echo "::endgroup::"
       shell: bash
-
     # NOTE: We apply a workaround as of version 3.0.1 by passing
     #       --egress-selector-mode=disabled by default as not doing so following
     #       modern versions of k3s has led to issues with `kubectl exec` and
@@ -100,15 +113,22 @@ runs:
     #       and https://github.com/jupyterhub/action-k3s-helm/issues/59.
     #
     - name: Setup k3s ${{ inputs.k3s-version }}${{ inputs.k3s-channel }}
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
+        printf '::group::Setup k3s %s%s\n' "$INPUT_K3S_VERSION" "$INPUT_K3S_CHANNEL"
+        if [[ "$INPUT_METRICS_ENABLED" != true ]]; then
           k3s_disable_metrics="--disable metrics-server"
         fi
-        if [[ "${{ inputs.traefik-enabled }}" != true ]]; then
+        if [[ "$INPUT_TRAEFIK_ENABLED" != true ]]; then
           k3s_disable_traefik="--disable traefik"
         fi
-        if [[ "${{ inputs.docker-enabled }}" == true ]]; then
+        if [[ "$INPUT_DOCKER_ENABLED" == true ]]; then
           k3s_docker=--docker
         fi
         # We want to provide a new default value for the --egress-selector-mode
@@ -118,20 +138,33 @@ runs:
         # Details about the option available at
         # https://docs.k3s.io/installation/network-options#control-plane-egress-selector-configuration.
         #
-        if [[ "${{ inputs.extra-setup-args }}" != *--egress-selector-mode* ]]; then
+        if [[ "$INPUT_EXTRA_SETUP_ARGS" != *--egress-selector-mode* ]]; then
           default_extra_setup_args=--egress-selector-mode=disabled
         fi
-        curl -sfL https://get.k3s.io | INSTALL_K3S_VERSION="${{ inputs.k3s-version }}" INSTALL_K3S_CHANNEL="${{ inputs.k3s-channel }}" sh -s - \
+
+        # Token
... (truncated)
```

Locations:

- `action.yml`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection, unsafe-shell, github-env-injection, prior-fix

**Notes:**

action.yml: moved all inputs.* expressions out of run: scripts into step env: blocks (Validate input, Setup k3s, Setup Helm, Wait step); group headers use printf. extra-setup-args (an argument list upstream inlined as an expression) is tokenized with the quote-aware guarded xargs split into a bash array. k3s and helm installers are now downloaded to temp files and executed separately (dropped the stdin-specific 'sh -s -'). KUBECONFIG write to GITHUB_ENV and all GITHUB_OUTPUT writes are newline-stripped; command outputs captured with '|| true' to preserve upstream's echo-masked failure semantics.

