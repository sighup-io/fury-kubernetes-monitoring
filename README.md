# Fury Kubernetes Monitoring

This repository contains all components necessary to deploy monitoring tools on
top of Kubernetes. We use [Prometheus](https://prometheus.io/), a very popular open-source monitoring and
alerting toolkit for cloud-native applications. You can monitor both the cluster
itself and applications deployed on the cluster via Prometheus. Alertmanager which
makes part of the Prometheus stack, handles alerts sent by the Prometheus server and lets
you manage alerts flexibly and route them through receiver integrations such as
email, Slack, or PagerDuty. Thanks to the components in the Fury Kubernetes
Monitoring stack, you're going to have full control over your cluster. On
Kubernetes, we use Prometheus Operator to deploy, configure, and manage Prometheus
instances and to manage service monitoring and alerts. This repository contains
a package to deploy Prometheus Operator and other packages to deploy Prometheus
instances, rules, alerts, and exporters. Packages with `-operated` postfix are
deployed via Operator's CRD, therefore you need Prometheus Operator up and
running to be able to deploy them.

## Monitoring Packages

The following packages are included in the Fury Kubernetes Monitoring katalog. All
resources in these repositories are going to be deployed in `monitoring`
namespace in your Kubernetes cluster.

- [prometheus-operator](katalog/prometheus-operator): Operator to deploy and
  manage Prometheus and related resources. Version: **0.48.1**
- [prometheus-operated](katalog/prometheus-operated): Prometheus instance
  deployed with Prometheus Operator's CRD. Version: **2.27.1**
- [alertmanager-operated](katalog/alertmanager-operated): Alertmanager instance
  deployed with Prometheus Operator's CRD, pay attention to change the
  [config](katalog/alertmanager-operated/secret.yml) as needed. Version: **0.22.2**
- [grafana](katalog/grafana): Grafana deployment to query and visualize metrics
  collected by Prometheus. Version: **7.5.7**
- [goldpinger](katalog/goldpinger): **Goldpinger** makes calls between its instances for visibility and alerting.
  Version: **3.2.0**
- [aks-sm](katalog/aks-sm): Service Monitor to collect Kubernetes components
  metrics from AKS
- [gke-sm](katalog/gke-sm): Service Monitor to collect Kubernetes components
  metrics from GKE
- [eks-sm](katalog/eks-sm): Service Monitor to collect Kubernetes components
  metrics from EKS
- [ovh-sm](katalog/ovh-sm): Service Monitor to collect Kubernetes components
  metrics from OVH Kubernetes Service.
- [kubeadm-sm](katalog/kubeadm-sm): Service Monitors, Prometheus rules and
  alerts for Kubernetes components of unmanaged/on-premise clusters.
- [kube-proxy-metrics](katalog/kube-proxy-metrics): RBAC Proxy to expose kube-proxy metrics. Works in all
environments *(managed and unmanaged Kubernetes clusters)*. **0.10.0**.
- [kube-state-metrics](katalog/kube-state-metrics): Service Monitor for
  Kubernetes objects such as Deployments, Nodes and Pods. Version: **2.0.0**
- [node-exporter](katalog/node-exporter): Service Monitor for hardware and OS
  metrics exposed by \*NIX kernels. Version: **1.1.2**
- [metrics-server](katalog/metrics-server): Resource metrics collection from
  kubelet and exposition through [Metrics API](https://github.com/kubernetes/metrics).
  Version: **0.5.0**
- [Thanos](katalog/thanos): Thanos is an opensource Prometheus setup that allows having 2 important features:
  - High availability on Prometheus *(setting multiple Prometheus replicas)*.
  - Long term storage capacity relying on an external object storage.
  Version: **v0.20.2**
- [x509-exporter](katalog/x509-exporter): Provides monitoring for certificates.
  [Upstream Project](://github.com/enix/x509-certificate-exporter). Version: **2.9.2**

You can click on each package to see its documentation.

## Requirements

All packages in this repository have the following dependencies, for package
specific dependencies please visit the single package's documentation:

- [Kubernetes](https://kubernetes.io) >= `v1.18.0`
- [Furyctl](https://github.com/sighupio/furyctl) package manager to download
  Fury packages >= [`v0.2.2`](https://github.com/sighupio/furyctl/releases/tag/v0.2.2)
- [Kustomize](https://github.com/kubernetes-sigs/kustomize) = `v3.3.0`

## Compatibility

| Module Version / Kubernetes Version |       1.14.X       |       1.15.X       |       1.16.X       |       1.17.X       |       1.18.X       |       1.19.X       |       1.20.X       |  1.21.X   |
| ----------------------------------- | :----------------: | :----------------: | :----------------: | :----------------: | :----------------: | :----------------: | :----------------: | :-------: |
| v1.0.0                              |                    | :white_check_mark: |                    |                    |                    |                    |                    |           |
| v1.1.0                              | :white_check_mark: | :white_check_mark: |        :x:         |                    |                    |                    |                    |           |
| v1.2.0                              | :white_check_mark: | :white_check_mark: |        :x:         |                    |                    |                    |                    |           |
| v1.3.0                              | :white_check_mark: | :white_check_mark: | :white_check_mark: |                    |                    |                    |                    |           |
| v1.4.0                              |     :warning:      |     :warning:      | :white_check_mark: |                    |                    |                    |                    |           |
| v1.4.1                              |     :warning:      |     :warning:      | :white_check_mark: |                    |                    |                    |                    |           |
| v1.5.0                              |     :warning:      |     :warning:      | :white_check_mark: |                    |                    |                    |                    |           |
| v1.6.0                              |     :warning:      |     :warning:      | :white_check_mark: |                    |                    |                    |                    |           |
| v1.6.1                              |     :warning:      |     :warning:      | :white_check_mark: |                    |                    |                    |                    |           |
| v1.7.0                              |     :warning:      |     :warning:      | :white_check_mark: |                    |                    |                    |                    |           |
| v1.7.1                              |     :warning:      |     :warning:      | :white_check_mark: |                    |                    |                    |                    |           |
| v1.8.0                              |                    |                    | :white_check_mark: | :white_check_mark: | :white_check_mark: |                    |                    |           |
| v1.9.0                              |                    |                    | :white_check_mark: | :white_check_mark: | :white_check_mark: |                    |                    |           |
| v1.10.0                             |                    |                    | :white_check_mark: | :white_check_mark: | :white_check_mark: |     :warning:      |                    |           |
| v1.10.1                             |                    |                    | :white_check_mark: | :white_check_mark: | :white_check_mark: |     :warning:      |                    |           |
| v1.10.2                             |                    |                    | :white_check_mark: | :white_check_mark: | :white_check_mark: |     :warning:      |                    |           |
| v1.11.0                             |                    |                    |                    | :white_check_mark: | :white_check_mark: | :white_check_mark: |     :warning:      |           |
| v1.12.0                             |                    |                    |                    |                    | :white_check_mark: | :white_check_mark: | :white_check_mark: | :warning: |
| v1.12.1                             |                    |                    |                    |                    | :white_check_mark: | :white_check_mark: | :white_check_mark: | :warning: |
| v1.12.2                             |                    |                    |                    |                    | :white_check_mark: | :white_check_mark: | :white_check_mark: | :warning: |

- :white_check_mark: Compatible
- :warning: Has issues
- :x: Incompatible

### Warning

- [kube-state-metrics](katalog/kube-state-metrics) is not able to scrape
    `ValidatingWebhookConfiguration` in Kubernetes < 1.16.X.
- :warning: : module version: `v1.11.0` and Kubernetes Version: `1.20.x`. It works as expected. Marked as warning
because it is not officially supported by [SIGHUP](https://sighup.io).
- :warning: : module version: `v1.12.0` and Kubernetes Version: `1.21.x`. It works as expected. Marked as warning
because it is not officially supported by [SIGHUP](https://sighup.io).

## Deployment

To start using Fury Kubernetes Monitoring, you need to use
[furyctl](https://github.com/sighup-io/furyctl/blob/master/README.md)
and create a `Furyfile.yml` with the list of all the packages that you want to
download.

You can download the packages for a full monitoring stack including
Prometheus Operator, Prometheus, Alertmanager, node-exporter, kube-state-metrics
and Grafana using the following `Furyfile.yml` :

```yaml
bases:
    - name: monitoring/prometheus-operator
      version: v1.12.2
    - name: monitoring/prometheus-operated
      version: v1.12.2
    - name: monitoring/alertmanager-operated
      version: v1.12.2
    - name: monitoring/node-exporter
      version: v1.12.2
    - name: monitoring/kube-state-metrics
      version: v1.12.2
    - name: monitoring/grafana
      version: v1.12.2
    - name: monitoring/goldpinger
      version: v1.12.2
```

and execute

```bash
furyctl vendor
```

to download the packages under `./vendor/katalog/monitoring`.

See `furyctl`
[documentation](https://github.com/sighup-io/furyctl/blob/master/README.md)
for details about `Furyfile.yml` format.

To deploy the packages to your cluster, define a `kustomization.yaml` with the
following content:

```yaml
bases:
    - ./vendor/katalog/monitoring/prometheus-operator
```

and execute

```shell
kustomize build . | kubectl apply -f -
```

to deploy Prometheus Operator and create the Custom Resource Definitions needed
by the other packages.

Now you can add the other packages to `kustomization.yaml`, the final file will
have the following content:

```yaml
bases:
    - ./vendor/katalog/monitoring/prometheus-operator
    - ./vendor/katalog/monitoring/prometheus-operated
    - ./vendor/katalog/monitoring/alertmanager-operated
    - ./vendor/katalog/monitoring/node-exporter
    - ./vendor/katalog/monitoring/kube-state-metrics
    - ./vendor/katalog/monitoring/grafana
    - ./vendor/katalog/monitoring/goldpinger
```

See `kustomize`
[documentation](https://github.com/kubernetes-sigs/kustomize/blob/master/docs/README.md)
for details about `kustomization.yaml` format.

To deploy all the packages to your cluster, execute the following command:

```bash
kustomize build . | kubectl apply -f -
```

The following cluster architectures are supported to obtain metrics from
Kubernetes components:

- on-premise or unmanaged cloud clusters
- Google Kubernetes Engine (GKE)
- Azure Kubernetes Service (AKS)
- Elastic Kubernetes Service (EKS)
- OVH Kubernetes Service

### On-premise or unmanaged cloud clusters

- Add `monitoring/kubeadm-sm` and `monitoring/configs` to `Furyfile.yml`.
- Download package with `furyctl vendor`
- Add `./vendor/katalog/monitoring/kubeadm-sm` to `kustomization.yaml`.
- Deploy package with `kustomize build . | kubectl apply -f -`

### Google Kubernetes Engine (GKE)

- Add `monitoring/gke-sm` and `monitoring/configs` to `Furyfile.yml`.
- Download package with `furyctl vendor`
- Add `./vendor/katalog/monitoring/gke-sm` to `kustomization.yaml`.
- Deploy package with `kustomize build . | kubectl apply -f -`

### Elastic Kubernetes Service (EKS)

- Add `monitoring/eks-sm` and `monitoring/configs` to `Furyfile.yml`.
- Download package with `furyctl vendor`
- Add `./vendor/katalog/monitoring/eks-sm` to `kustomization.yaml`.
- Deploy package with `kustomize build . | kubectl apply -f -`

### Azure Kubernetes Service (AKS)

- Add `monitoring/aks-sm` and `monitoring/configs` to `Furyfile.yml`.
- Download package with `furyctl vendor`
- Add `./vendor/katalog/monitoring/aks-sm` to `kustomization.yaml`.
- Deploy package with `kustomize build . | kubectl apply -f -`

### OVH Kubernetes Service

- Add `monitoring/ovh-sm` and `monitoring/configs` to `Furyfile.yml`.
- Download package with `furyctl vendor`
- Add `./vendor/katalog/monitoring/ovh-sm` to `kustomization.yaml`.
- Deploy package with `kustomize build . | kubectl apply -f -`

If you need to customize our packages you can do it with `kustomize`. It lets
you create customized Kubernetes resources based on other Kubernetes resource
files, leaving the original YAML untouched and usable as-is. To learn how to
create your customization layer with it please see the `kustomize`
[repository](https://github.com/kubernetes-sigs/kustomize).

For further details please refer to the single package directories in this
repository.

## Examples

To see examples on how to customize Fury Kubernetes Monitoring packages, please
go to [examples](examples) directory.

## License

For license details please see [LICENSE](LICENSE)
