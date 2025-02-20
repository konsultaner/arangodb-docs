---
layout: default
description: The ArangoDB Kubernetes Operator needs to be installed in your Kubernetes cluster first
---
# Using the ArangoDB Kubernetes Operator

## Installation

The ArangoDB Kubernetes Operator needs to be installed in your Kubernetes
cluster first.

If you have `Helm` available, we recommend installation using `Helm`.

### Installation with Helm

To install the ArangoDB Kubernetes Operator with [`helm`](https://www.helm.sh/){:target="_blank"},
run the following commands (replace `<version>` with the
[version of the operator](https://github.com/arangodb/kube-arangodb/releases){:target="_blank"}
that you want to install):

```bash
export URLPREFIX=https://github.com/arangodb/kube-arangodb/releases/download/<version>
helm install $URLPREFIX/kube-arangodb-crd-<version>.tgz
helm install $URLPREFIX/kube-arangodb-<version>.tgz
```

This installs operators for the `ArangoDeployment` and `ArangoDeploymentReplication`
resource types.

If you want to avoid the installation of the operator for the `ArangoDeploymentReplication`
resource type, add `--set=DeploymentReplication.Create=false` to the `helm install`
command.

To use `ArangoLocalStorage` resources, also run:

```bash
helm install $URLPREFIX/kube-arangodb-<version>.tgz --set "operator.features.storage=true"
```

The default CPU architecture of the operator is `amd64` (x86-64). To enable ARM
support (`arm64`) in the operator, overwrite the following setting:

```bash
helm install $URLPREFIX/kube-arangodb-<version>.tgz --set "operator.architectures={amd64,arm64}"
```

{% hint 'tip' %}
Use at least version 1.2.20 of the operator to use the ARM architecture.
{% endhint %}

Note that you need to set [`spec.architecture`](deployment-kubernetes-deployment-resource.html#specarchitecture-string)
in the deployment specification, too, in order to create a deployment that runs
on ARM chips.

For more information on installing with `Helm` and how to customize an installation,
see [Using the ArangoDB Kubernetes Operator with Helm](deployment-kubernetes-helm.html).

### Installation with Kubectl

To install the ArangoDB Kubernetes Operator without `Helm`,
run (replace `<version>` with the version of the operator that you want to install):

```bash
export URLPREFIX=https://raw.githubusercontent.com/arangodb/kube-arangodb/<version>/manifests
kubectl apply -f $URLPREFIX/arango-crd.yaml
kubectl apply -f $URLPREFIX/arango-deployment.yaml
```

To use `ArangoLocalStorage` resources, also run:

```bash
kubectl apply -f $URLPREFIX/arango-storage.yaml
```

To use `ArangoDeploymentReplication` resources, also run:

```bash
kubectl apply -f $URLPREFIX/arango-deployment-replication.yaml
```

You can find the latest release of the ArangoDB Kubernetes Operator
in the [kube-arangodb repository](https://github.com/arangodb/kube-arangodb/releases/latest){:target="_blank"}.

## ArangoDB deployment creation

After deploying the latest ArangoDB Kubernetes operator, use the command below to deploy your [license key](administration-license.html) as a secret which is required for the Enterprise Edition starting with version 3.9:

```bash
kubectl create secret generic arango-license-key --from-literal=token-v2="<license-string>"
```

Once the operator is running, you can create your ArangoDB database deployment
by creating a `ArangoDeployment` custom resource and deploying it into your
Kubernetes cluster.

For example (all examples can be found in the [kube-arangodb repository](https://github.com/arangodb/kube-arangodb/tree/master/examples){:target="_blank"}):

```bash
kubectl apply -f examples/simple-cluster.yaml
```
Additionally, you can specify the license key required for the Enterprise Edition starting with version 3.9 as seen below:

```yaml
spec:
  [...]
  image: arangodb/enterprise:3.9.1
  license:
    secretName: arango-license-key
```

## Deployment removal

To remove an existing ArangoDB deployment, delete the custom
resource. The operator will then delete all created resources.

For example:

```bash
kubectl delete -f examples/simple-cluster.yaml
```

**Note that this will also delete all data in your ArangoDB deployment!**

If you want to keep your data, make sure to create a backup before removing the deployment.

## Operator removal

To remove the entire ArangoDB Kubernetes Operator, remove all
clusters first and then remove the operator by running:

```bash
helm delete <release-name-of-kube-arangodb-chart>
# If `ArangoLocalStorage` operator is installed
helm delete <release-name-of-kube-arangodb-storage-chart>
```

or when you used `kubectl` to install the operator, run:

```bash
kubectl delete deployment arango-deployment-operator
# If `ArangoLocalStorage` operator is installed
kubectl delete deployment -n kube-system arango-storage-operator
# If `ArangoDeploymentReplication` operator is installed
kubectl delete deployment arango-deployment-replication-operator
```

## See also

- [Driver configuration](deployment-kubernetes-driver-configuration.html)
- [Scaling](deployment-kubernetes-scaling.html)
- [Upgrading](deployment-kubernetes-upgrading.html)
- [Using the ArangoDB Kubernetes Operator with Helm](deployment-kubernetes-helm.html)
