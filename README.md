This repo is the documentation for Github Fury Kuberetes Monitoring repo. To be able to access the  fury-kubernetes-monitoring packages you must be a Sighup customer. If you're not a customer yet, contact us at info@sighup.io or http://www.sighup.io for info on how to get access!



# Fury Kubernetes Monitoring
 
This repo contains all components necessary to deploy monitoring tools on top of Kubernetes. We use Prometheus, a very popular open source monitoring and alerting toolkit for cloud-native applications. You can monitor both cluster itself and applications deployed on cluster via Prometheus. AlertManager which makes part of Prometheus stack, handles alerts sent by Prometheus server and let you manage alerts flexibly and route them receiver integrations such as email, Slack or PagerDuty. Thanks to the components in the Fury Kubernetes Monitoring stack, you're going to have full control on your cluster. On Kubernetes we use Prometheus Operator to deploy, configure and manage Prometheus instances and to manage Service Monitoring and Alerts. This repo contains a package to deploy Prometheus Operator and other packages to deploy Prometheus instances and exporters to be consumed by Prometheus. Packages with `-operated` postfix are packages which are deployed via Operator' therefore  you need Prometheus operator up and running to be able to deploy them.  


## Requirements

All packages in this repository have following dependencies, for package specific dependencies please visit the single package's documentation:

- [Kubernetes](kubernetes.io) >= `v1.10`
- [Furyctl](documentation_link) package manager to install Fury packages
- [Kustomize](https://github.com/kubernetes-sigs/kustomize) 



### Furyctl

In order to get Fury packages you should first install [Furyctl](documentation_link). Furyctl is package manager for Fury distribution. It’s simple to use and reads a single Furyfile which contains packages you want to get. To learn how to install and use Furyctl please see the link above.


### Kustomize

[Kustomize]() lets you create customized Kubernetes resources based on a Kubernetes YAML resource file, leaving the original YAML untouched and usable as is. You're going to use kustomize to patch our distribution based on your needs (for production, for staging etc), without modifying original resource files.

#### Basic usage of Kustomize

Customization is achieved via a `kustomize.yaml` file which will be "applied" on your resources to produces new resources with your modifications, without touching the original files.

A simple kustomization example would be something like this, where your `kustomization.yaml`, `deployment.yml` and `service.yml` are placed in `web-app` directory.

          
```yaml
kustomization.yaml         | deployment.yml          | service.yml 
---------------------      | ---------------------   | ---------------------
namespace: production      | apiVersion: apps/v1     |  apiVersion: v1                          
resources:                 | kind: Deployment        |  kind: Service                         
- deployment.yml           | metada:                 |  metada:                                      
- service.yml              |   name: web-app         |    name: web-app                              
configMapGenerator:        |   labels:               |  spec:              
- name: app-configuration  |     app: web-app        |    ports:
  files:                   | spec:                   |    ...                         
    - deafult.json         |   replicas: 1           | 
                               ...
```

In your `kustomization.yaml` you specify which resources you want your patches to be applied. You can overwrite existing fields and add new fields. In this example `namespace: production` field and configMap `app-configuration` will be added to resources `deployment.yaml` and `service.yaml`.

Then you can generate customized resources with:

`kubectl build /path/to/web-app`

And you can directly deploy them to the cluster with:

`kubectl build /path/to/web-app | kubectl apply -f -`


#### Customization of packages with overlays

To generate variants of a configuration for different cases (like development, staging or production) you need to use overlays of Kustomize. An overlay is just another kustomization, refering to the base and to the patches to apply on that base.

** overlay = kustomization file + patches + more resources **

Create a directory structure like following for you resources:

```
├── base
│   └── kustomization.yaml
├── production
│   ├── a_resource.yml
│   ├── kustomization.yaml
│   └── a_patch.yml
└── staging
    ├── a_resource.yml
    ├── kustomization.yaml
    └── a_patch.yml
```

Your `base` dir must include all bases and patches common to your different environments. It can also include other resources you want to add. Then in your overlaying directories you can apply patches to only differing bases.

Your `base/kustomization.yaml` file will look like that:

```yaml
bases:
  - /relative/path/to/vendor/bases/monitoring/alertmanager-operated
  - /relative/path/to/vendor/bases/monitoring/prometheus-operated
  - /relative/path/to/vendor/bases/monitoring/node_exporter
  // ...

patches:
  - ./prometheus-patch
  - ./alertmanager-patch
  //..

resources:
  - /relative/path/to/resource/file
  // ..
```

Fury distribution packages will be your bases to refer. Remember that `furyctl` downloads them under `vendor` directory. Ofcourse you can structure your directory gerarchy differently, as long as you use correct paths to your dirs and files, kustomize will work.


#### Example

So let say you want to customize `prometheus-operated` of Fury distribution with some `externalLabels` for your cluster, then you would create a patch file which has only minimal necessary information to apply your patch to the correct resource:

```yaml
apiVersion: monitoring.coreos.com/v1
kind: Prometheus
metadata:
  name: k8s
  namespace: monitoring
spec:
  externalLabels:
      k8s_cluster: mycluster
```

When you add `prometheus-operated` to your bases and patch file above to the patches in your kustomization.yaml it will be applied to the Prometheus resource which is in `monitoring` namespace and has name `k8s`: 

```yaml
bases:
 - /path/to/vendor/bases/monitoring/prometheus-operated
 //..

patches:
 - prometheus-patch.yaml
 //...
```
 
Now let say now you want to add `additionalScrapeConfig` to Prometheus for production environment. What you need to do is creating a `kustomization.yaml` in your production overlay directory, refering to your `bases` directory and adding the patch files you want to apply. Your patch file would be:

```yaml
apiVersion: monitoring.coreos.com/v1
kind: Prometheus
metadata:
  name: k8s
  namespace: monitoring
spec:
  additionalScrapeConfigs:
    name: prometheus-additional-scrapes
    key: prometheus-additional-scrapes.yml
```

And your kustomization file would be:

```yaml
bases:
  - ../bases

patches:
  - ./prometheus-patch.yml
```

Then you can generate resource files for environment you want by using its directory:

`$ kustomize build /path/to/production`

or apply it directly to a cluster:

`$ kustomize build /path/to/production | kubectl apply -f -`


You'are going to find examples in each package's documentation to customize Fury distribution resources. For further details please visit project's [repo](https://github.com/kubernetes-sigs/kustomize)

##  Monitoring Packages 

Following packages are included in Fury Kubernetes Monitoring katalog. All resources in these repositories are going to be deployed in `monitoring` namespace in your Kubernetes cluster.

- [prometheus-operator](https://github.com/sighup-io/fury-kubernetes-monitoring/blob/master/prometheus-operator/README.md) It manages full stack of monitoring.
- [prometheus-operated](https://github.com/sighup-io/fury-kubernetes-monitoring/blob/master/prometheus-operated/README.md) : Prometheus instance to deploy with Prometheus Operator
- [alertmanager-operated](https://github.com/sighup-io/fury-kubernetes-monitoring/blob/master/alertmanager-operated/README.md) : Prometheus Alert Manager to handle alerts sent by client applications such as the Prometheus server
- [grafana]() : Grafana lets you query and visualize metrics collected by Prometheus
- [gcp-sm]() : Service Monitor to collect metrics from GoogleCloud Kubernetes cluster kubelet components(?)
- [kubeadm-sm]() : Service Monitor to collect metrics exposed by CoreDNS server on your Kubernetes cluster
- [kube-state-metrics]() : Exporter for metrics about the state of Kubernetes objects such as Deployments, Nodes and Pods  
- [node-exporter]() : Exporter for hardware and OS metrics exposed by \*NIX kernels


You can click on each package to learn how to deploy and use each of them.


### Prometheus Operator

To be able to deploy monitoring components, first you should deploy Prometheus Operator. As explained above, it manages Prometheus instances, service monitoring and alert management. To learn how to deploy Prometheus Operator please follow the documentation of [prometheus-operator]() package.

To learn more about Prometheus and how to deploy it's instances follow [prometheus-operated]() package's documentation.


## License
For license details please see [LICENSE](license_link) 

