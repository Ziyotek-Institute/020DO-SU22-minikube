# 020DO-SU22-minikube Deploy WebApp that stores data in MongoDB deployed on the same minikube cluster
 
## Prerequisites and dependencies
```
minikube status
minikube addons enable ingress
```

Change directory:
```
cd /home/admin/020DO-SU22-minikube/Mongo_webApp
```

1. Apply/create the k8s objects {{.kind.Deployment}} &  {{.kind.Service.spec.type.ClusterIp}}

```
kubectl apply -f mongo-cm.yaml -f mongo-secret.yaml
```
2. Apply the mongo.yaml

```
kubectl apply -f mongo.yaml
```
3. Apply/create the k8s objects {{.kind.Deployment}} &  {{.kind.Service.spec.type.NodePort}} 

```
kubectl apply -f webapp.yaml
```

4. Apply/create the k8s object {{.kind.Ingress}} 

5. Clean up

```
kubectl delete deployment.apps/mongo deployment.apps/webapp service/mongo-service service/webapp-service
```
