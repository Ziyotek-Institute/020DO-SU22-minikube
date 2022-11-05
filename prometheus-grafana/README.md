# Minikube and Prometheus Demo #

This is a quick demo of using

* [minikube](https://github.com/kubernetes/minikube) to test
* [Prometheus](https://prometheus.io/).  
* [Grafana](https://grafana.com/) 

## Prerequisites ##

You can check that the node is up and running by running: `minikube
status`. You should see something like:
```
minikube: Running
cluster: Running
kubectl: Correctly Configured: pointing to minikube-vm at 192.168.64.48
```

If you want to stop the cluster, run `minikube stop`. To start it, run
`minikube start`.

Run `kubectl cluster-info` and you should see something like:
```
kubernetes master is running at https://192.168.64.2:8443
kubernetes-dashboard is running at https://192.168.64.2:8443/api/v1/proxy/namespaces/kube-system/services/kubernetes-dashboard
```

## Deploying Prometheus and Grafana ##


