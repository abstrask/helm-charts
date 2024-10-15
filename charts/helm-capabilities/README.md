# Helm Capabilities chart

> [!TIP]
> This chart is not meant to, and cannot, be installed in Kubernetes.
>
> It's meant to use with the `template` command only, to show the capabilties available to Helm.

Produces a YAML document showing the Kubernetes and API versions available to Helm.

These are often used by charts to make decisions about what API version a given resource should be deployed with, or if it should be deployed at all.

These capabilities are normally read when installing the chart (`helm install`). When dry-running charts (`helm template`), the Kubernetes version included in the Helm command is used, along the built-in API versions of that Kubernetes version.

As I haven't found an easy way to output these using the Helm command directly, I made this chart that simply outputs the capabilities when used with the `helm template` command.

## Usage

```bash
# Bare-bones example
helm template --repo https://abstrask.github.io/helm-charts helm-capabilities

# Override the built-in Kubernetes version
helm template --repo https://abstrask.github.io/helm-charts helm-capabilities --kube-version 1.20 | \
  yq '.helmCapabilities.kubeVersion'

# Add avaialble API version to those built-in
helm template --repo https://abstrask.github.io/helm-charts helm-capabilities --api-versions myapi/v0 | \
  yq '.helmCapabilities.apiVersions[] | select(. == "myapi/v0")'

```
## Example Output

```yaml
---
# Source: helm-capabilities/templates/capabilities.yaml
helmCapabilities:
  apiVersions:
    - v1
    - admissionregistration.k8s.io/v1
    - admissionregistration.k8s.io/v1alpha1
    - admissionregistration.k8s.io/v1beta1
    - internal.apiserver.k8s.io/v1alpha1
    - apps/v1
    - apps/v1beta1
    - apps/v1beta2
    - authentication.k8s.io/v1
    - authentication.k8s.io/v1alpha1
    - authentication.k8s.io/v1beta1
    - authorization.k8s.io/v1
    - authorization.k8s.io/v1beta1
    - autoscaling/v1
    - autoscaling/v2
    - autoscaling/v2beta1
    - autoscaling/v2beta2
    - batch/v1
    - batch/v1beta1
    - certificates.k8s.io/v1
    - certificates.k8s.io/v1beta1
    - certificates.k8s.io/v1alpha1
    - coordination.k8s.io/v1alpha1
    - coordination.k8s.io/v1beta1
    - coordination.k8s.io/v1
    - discovery.k8s.io/v1
    - discovery.k8s.io/v1beta1
    - events.k8s.io/v1
    - events.k8s.io/v1beta1
    - extensions/v1beta1
    - flowcontrol.apiserver.k8s.io/v1
    - flowcontrol.apiserver.k8s.io/v1beta1
    - flowcontrol.apiserver.k8s.io/v1beta2
    - flowcontrol.apiserver.k8s.io/v1beta3
    - networking.k8s.io/v1
    - networking.k8s.io/v1alpha1
    - networking.k8s.io/v1beta1
    - node.k8s.io/v1
    - node.k8s.io/v1alpha1
    - node.k8s.io/v1beta1
    - policy/v1
    - policy/v1beta1
    - rbac.authorization.k8s.io/v1
    - rbac.authorization.k8s.io/v1beta1
    - rbac.authorization.k8s.io/v1alpha1
    - resource.k8s.io/v1alpha3
    - scheduling.k8s.io/v1alpha1
    - scheduling.k8s.io/v1beta1
    - scheduling.k8s.io/v1
    - storage.k8s.io/v1beta1
    - storage.k8s.io/v1
    - storage.k8s.io/v1alpha1
    - storagemigration.k8s.io/v1alpha1
    - apiextensions.k8s.io/v1beta1
    - apiextensions.k8s.io/v1
  helmVersion: v3.16.1
  kubeVersion: v1.31.0
```

Compared to templating the same chart with Helm 3.10.3 using [dyff](https://github.com/homeport/dyff):

```plain
     _        __  __
   _| |_   _ / _|/ _|  between 310.yaml
 / _' | | | | |_| |_       and 316.yaml
| (_| | |_| |  _|  _|
 \__,_|\__, |_| |_|   returned three differences
        |___/

helmCapabilities.apiVersions
  - one list entry removed:
    - flowcontrol.apiserver.k8s.io/v1alpha1

  + eight list entries added:
    - admissionregistration.k8s.io/v1alpha1
    - authentication.k8s.io/v1alpha1
    - certificates.k8s.io/v1alpha1
    - coordination.k8s.io/v1alpha1
    - flowcontrol.apiserver.k8s.io/v1
    - flowcontrol.apiserver.k8s.io/v1beta3
    - resource.k8s.io/v1alpha3
    - storagemigration.k8s.io/v1alpha1


helmCapabilities.helmVersion
  ± value change
    - v3.10.3
    + v3.16.1

helmCapabilities.kubeVersion
  ± value change
    - v1.25.0
    + v1.31.0
```

You can override the built-in Kubernetes version, and append API versions used for templating, by specifying the `--kube-version` and `--api-versions` [flags](https://helm.sh/docs/helm/helm_template/).
