# Kubernetes deployment for `hello-world`

This kubernetes deployment was tested on [GKE](https://cloud.google.com/container-engine/). It utilizes the built-in [`kubeDNS`](https://github.com/kubernetes/kubernetes/tree/master/cluster/addons/dns) to allow the services to find the Consul cluster (used for internal application discovery).

All other service discovery and application orchestration is performed through the Autopilot Pattern using ContainerPilot. To deploy this example run the following from the project root:

```bash
# First start the consul instance for service discovery
$ kubectl apply -f consul/kube-deployment.yml

# Then expose the consul deployment through a service. 
# This is instead of using the Triton CNS
$ kubectl apply -f consul/kube-service.yml

# Once consul is deployed you can spin up your services.
$ kubectl apply -f hello/kube-deployment.yml
$ kubectl apply -f world/kube-deployment.yml
$ kubectl apply -f nginx/kube-deployment.yml
```

Once your deployments are created you can scale them up and down easily. ContainerPilot, in conjunction with Consul, automates the service discovery and the configuration (and re-configuration) of the components as we scale. To scale up the `hello` service change the number of replicas:

```yaml
spec:
  # replicas: 2
  replicas: 4
```

If you check the Consul logs you should see the new `hello` services register.

If you require a cloud load balancer to manage ingress to your cluster, also deploy the `nginx` service to allow traffic to the `hello-world` app:

```bash
$ kubectl apply -f nginx/kube-service.yml
```
