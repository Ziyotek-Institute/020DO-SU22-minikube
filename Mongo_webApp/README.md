# 020DO-SU22-minikube Deploy WebApp that stores data in MongoDB deployed on the same minikube cluster
 
## Prerequisites and dependencies
```
minikube status
```

Change directory:
```
cd /home/admin/020DO-SU22-minikube/Mongo_webApp
```

1. Apply the mongo-cm.yaml, mongo-secret.yaml

```
kubectl apply -f mongo-cm.yaml -f mongo-secret.yaml
```
2. Apply the mongo.yaml

```
kubectl apply -f mongo.yaml
```
3. Apply the webapp.yaml

```
kubectl apply -f webapp.yaml
```

4. Clean up

```
kubectl delete deployment.apps/mongo deployment.apps/webapp service/mongo-service service/webapp-service
```
