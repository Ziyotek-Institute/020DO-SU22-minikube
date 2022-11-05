# Prometheus and Grafana setup in Minikube #

## Introduction ##

In this LAB you will deploy [Prometheus](https://prometheus.io/) and [Grafana](https://grafana.com/) into your [Minikube](https://github.com/kubernetes/minikube) cluster using their provided Helm charts.
* Prometheus will help us monitor our Kubernetes Cluster and other resources running on it. 
* Grafana will help you visualize metrics recorded by Prometheus and display them in fancy [dashboards](https://grafana.com/grafana/dashboards/).

## Prerequisites ##

* [Minikube](https://github.com/kubernetes/minikube) 
* [Helm](https://helm.sh/)
```
mkdir -p ~/pre-reqs/; cd ~/pre-reqs/

curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3

chmod 700 get_helm.sh

./get_helm.sh

helm --help
```

1. Install Prometheus
```
#   We’ll start by adding the repository to our helm configuration:

helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

#   Once the repo is ready, we can install the provided charts by running the following commands:

helm install prometheus prometheus-community/prometheus

#   Get the Prometheus server URL by running these commands in the same shell:

export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")

kubectl --namespace default port-forward $POD_NAME 9090

# Open local browser and paster -> http://127.0.0.1:9090
```

2. Install Grafana
#   The recommended charts are the ones hosted by the Grafana Community Kubernetes Helm Charts repository.
```
#   Same as before, we’ll start by adding the repository to our helm configuration:

helm repo add grafana https://grafana.github.io/helm-charts


#   Next, we install Grafana by using the provided charts:

helm install grafana grafana/grafana


#   Get your 'admin' user password by running:

kubectl get secret --namespace default grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo

#   The Grafana server can be accessed via port 80 on the following DNS name from within your cluster:

nslookup grafana.default.svc.cluster.local

#    Get the Grafana URL to visit by running these commands in the same shell:

export POD_GRAFANA=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=grafana,app.kubernetes.io/instance=grafana" -o jsonpath="{.items[0].metadata.name}")

kubectl --namespace default port-forward $POD_GRAFANA 3000
```
3. Login with the password from step 1 and the username: admin
```
#################################################################################
######   WARNING: Persistence is disabled!!! You will lose your data when   #####
######            the Grafana pod is terminated.                            #####
#################################################################################
```

