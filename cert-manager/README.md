# Introduction to cert-manager for Kubernetes

## We need a Kubernetes cluster



## Concepts 

It's important to understand the various concepts and new Kubernetes resources that <br/>
`cert-manager` introduces.

* Issuers [docs](https://cert-manager.io/docs/concepts/issuer/)
* Certificate [docs](https://cert-manager.io/docs/concepts/certificate/)
* CertificateRequests [docs](https://cert-manager.io/docs/concepts/certificaterequest/)
* Orders and Challenges [docs](https://cert-manager.io/docs/concepts/acme-orders-challenges/)

## Installation 

You can find the latest release for `cert-manager` on their [GitHub Releases page](https://github.com/jetstack/cert-manager/) <br/>

For this demo, I will use K8s 1.23 and `cert-manager` [v1.10.0](https://github.com/jetstack/cert-manager/releases/tag/v1.10.0)

```

# get cert-manager 

cd kubernetes/cert-manager/
curl -LO https://github.com/jetstack/cert-manager/releases/download/v1.10.0/cert-manager.yaml

mv cert-manager.yaml cert-manager-1.10.0.yaml

# install cert-manager 

kubectl apply --validate=false -f cert-manager-1.10.0.yaml
```

## Cert Manager Resources

We can see our components deployed

```
kubectl -n cert-manager get all

NAME                                           READY   STATUS    RESTARTS   AGE
pod/cert-manager-86548b886-2b8x7               1/1     Running   0          77s
pod/cert-manager-cainjector-6d59c8d4f7-hrs2v   1/1     Running   0          77s
pod/cert-manager-webhook-578954cdd-tphpj       1/1     Running   0          77s

NAME                           TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
service/cert-manager           ClusterIP   10.96.87.136   <none>        9402/TCP   77s
service/cert-manager-webhook   ClusterIP   10.104.59.25   <none>        443/TCP    77s

NAME                                      READY   UP-TO-DATE   AVAILABLE   AGE        
deployment.apps/cert-manager              1/1     1            1           77s
deployment.apps/cert-manager-cainjector   1/1     1            1           77s
deployment.apps/cert-manager-webhook      1/1     1            1           77s

NAME                                                 DESIRED   CURRENT   READY   AGE
replicaset.apps/cert-manager-86548b886               1         1         1       77s
replicaset.apps/cert-manager-cainjector-6d59c8d4f7   1         1         1       77s
replicaset.apps/cert-manager-webhook-578954cdd       1         1         1       77

```

## Test Certificate Issuing 

Let's create some test certificates

```
kubectl create ns cert-manager-test

kubectl apply -f ./selfsigned/issuer.yaml

kubectl apply -f ./selfsigned/certificate.yaml

kubectl describe certificate -n cert-manager-test
kubectl get secrets -n cert-manager-test

kubectl delete ns cert-manager-test
```

## Configuration 

https://cert-manager.io/docs/configuration/

## Setup my DNS

In my container, I can get the public IP address of my computer by running a simple command:

```
curl ifconfig.co

or 

minikube ip
```

I can log into my DNS provider and point my DNS A record to my IP.<br/>
Also setup my router to allow 80 and 443 to come to my PC <br/>

If you are running in the cloud, your Ingress controller and Cloud provider will give you a
public IP and you can point your DNS to that accordingly.

## Create Let's Encrypt Issuer for our cluster

We create a `ClusterIssuer` that allows us to issue certs in any namespace

```
kubectl apply -f cert-issuer-nginx-ingress.yaml

# check the issuer
kubectl describe clusterissuer letsencrypt-cluster-issuer

```

## Deploy a pod that uses SSL


## Issue Certificate 

```
kubectl apply -f certificate.yaml

# check the cert has been issued 

kubectl describe certificate example-app

# TLS created as a secret
kubectl get secrets
NAME                  TYPE                                  DATA   AGE
example-app-tls       kubernetes.io/tls                     2      84m
```
