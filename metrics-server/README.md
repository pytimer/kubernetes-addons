# Metrics Server


## Deploy

You can follow [offical document](https://github.com/kubernetes-incubator/metrics-server#deployment) to deploy `metrics-server` on the Kubernetes cluster. But some parameters need to update.

### Download

```sh
$ git clone https://github.com/kubernetes-incubator/metrics-server.git
```

Update `deploy/1.8+/metrics-server-deployment.yaml`:

```yaml
command:
  - /metrics-server
  - --kubelet-insecure-tls
  - --kubelet-preferred-address-types=InternalIP
```

`kubectl apply -R -f deploy/1.8+`

## Usage

If `metrics-server` installed successfully, you can run `kubectl top node` lookup the node metrics after `metrics-server` running a few minutes.
