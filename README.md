# k3d + NATS Helm

Run the [k3d](https://k3d.io) install script. For example:

```sh
curl -s https://raw.githubusercontent.com/k3d-io/k3d/main/install.sh | bash
```

Create a cluster with the specific ports we want to expose out of the Docker network. 4222 is the client port, 7422 is the leafnode port, and 8443 is the websocket port.

```sh
k3d cluster create nats \
    --port 4222:4222@loadbalancer \
    --port 7422:7422@loadbalancer \
    --port 8443:8443@loadbalancer \
    --servers 3
```

Install [Helm](https://helm.sh/docs/intro/install/), then add the NATS chart:

```sh
helm repo add nats https://nats-io.github.io/k8s/helm/charts/
```

Or if you already had installed, ensure it is up-to-date:

```sh
helm repo update nats
```

Create a `values.yaml` file with desired configuration. See [here](https://github.com/nats-io/k8s/blob/main/helm/charts/nats/values.yaml) for a list of all available options. A minimal one that will enable JetStream and expose the NATS endpoints:

```yaml
```

Now install the chart in the new cluster.

```sh
helm install nats nats/nats -f values.yaml
```

Install an load balancer service:

```sh
kubectl apply -f lb.yaml
```

Get the external IP addresses by running `kubectl get svc nats-lb`. Using any of those IP addresses you can now use the NATS CLI, for example:

```
nats -s 172.18.0.2:4222 pub test 'hello'
```

