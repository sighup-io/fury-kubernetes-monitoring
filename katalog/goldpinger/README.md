# Goldpinger

**Goldpinger** makes calls between its instances for visibility and alerting. It runs as a `DaemonSet` on `Kubernetes`
and produces `Prometheus` metrics that can be scraped, visualized and alerted on.

*Source:* [bloomberg/goldpinger](https://github.com/bloomberg/goldpinger/tree/v3.2.0).

## Image repository and tag

- goldpinger image: `docker.io/bloomberg/goldpinger:v3.2.0`.
- goldpinger repository: [https://github.com/bloomberg/goldpinger](https://github.com/bloomberg/goldpinger).

## Requirements

- Tested with Kubernetes >= `1.18.X`.
- Tested with Kustomize = `v3.0.X`.
- [Service monitor](sm.yml) also requires [prometheus-operator](../prometheus-operator).

## Configuration

Fury distribution goldpinger is deployed with the following configuration:
- The resource limits are `80Mi` for memory.
- Listens on port `8080`.
- Exposes its own dashboard on port `8080`, path: `/`.
- Exposes Prometheus metrics on port `8080`, path: `/metrics`.
- Metrics are scraped by Prometheus with `30s` intervals.

Note: In big clusters with lots of nodes you can set environment variable `PING_NUMBER` 
so nodes ping only a subset of other nodes. It decreases number of metrics generated which 
causes problems on Prometheus in big clusters.

## Deployment

You can deploy goldpinger by running the following command in the root of this project:

```shell
kustomize build | kubectl apply -f -
```

## License

For license details please see [LICENSE](./../../LICENSE)
