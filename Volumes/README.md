# 020DO-SU22-minikube Volumes: Configuring Redis using a ConfigMap
Minikube is a single node Kubernetes deployment on local host
 
## Prerequisites and dependencies
```
minikube status
minikube status
```

Change directory:
```
cd /home/admin/020DO-SU22-minikube/Volumes
```

## 1. Apply the redis-cm.yaml, redis-deploy.yaml
```
kubectl apply -f redis-cm.yaml
kubectl apply -f redis-deploy.yaml
```

Examine the contents of the Redis pod manifest and note the following:

* A volume named config is created by spec.volumes[1]
* The key and path under spec.volumes[1].items[0] exposes the redis-config key from the example-redis-config ConfigMap as a file named redis.conf on the config volume.
* The config volume is then mounted at /redis-master by spec.containers[0].volumeMounts[1].

This has the net effect of exposing the data in data.redis-config from the example-redis-config ConfigMap above as /redis-master/redis.conf inside the Pod.

Examine created objects
```
kubectl get pod/redis configmap/example-redis-config 
```
Recall that we left redis-config key in the example-redis-config ConfigMap blank:
```
kubectl describe configmap/example-redis-config
```

You should see an empty redis-config key:
```
Name:         example-redis-config
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
redis-config:
```

## 2. Use kubectl exec to enter the pod and run the redis-cli tool to check the current configuration:

List the pods with label redis

```
kubectl get pod -l app=redis
```

Expected output
```
NAME                    READY   STATUS              RESTARTS   AGE
redis-66d8cf866-qdnnl   0/1     ContainerCreating   0          15s
```

Copy the pod name under Name column, and use it as an input for  

```
kubectl exec -it <redisPod> -- redis-cli
```

Check maxmemory:

```
127.0.0.1:6379> CONFIG GET maxmemory
```

It should show the default value of 0:

```
1) "maxmemory"
2) "0"
```

Similarly, check maxmemory-policy:

```
127.0.0.1:6379> CONFIG GET maxmemory-policy
```

Which should also yield its default value of noeviction:

```
1) "maxmemory-policy"
2) "noeviction"
```

Now let's add some configuration values to the example-redis-config ConfigMap:

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: example-redis-config
data:
  redis-config: |
    maxmemory 2mb
    maxmemory-policy allkeys-lru 
```

Apply the updated ConfigMap:

```
kubectl apply -f redis-cm.yaml
```

Confirm that the ConfigMap was updated:

```
kubectl describe configmap/example-redis-config
```

You should see the configuration values we just added:

```
Name:         example-redis-config
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
redis-config:
----
maxmemory 2mb
maxmemory-policy allkeys-lru
```

Check the Redis Pod again using redis-cli via kubectl exec to see if the configuration was applied:

```
kubectl exec -it redis -- redis-cli
```

Check maxmemory:

```
127.0.0.1:6379> CONFIG GET maxmemory
```

It remains at the default value of 0:
```
1) "maxmemory"
2) "0"
```

Similarly, maxmemory-policy remains at the noeviction default setting:

```
127.0.0.1:6379> CONFIG GET maxmemory-policy
```

Returns:

```
1) "maxmemory-policy"
2) "noeviction"
```

The configuration values have not changed because the Pod needs to be restarted to grab updated values from associated ConfigMaps. Let's rollout the deployment redis :

```
kubectl rollout restart deploy redis
```

## 3. Now re-check the configuration values one last time:

```
kubectl exec -it redis -- redis-cli
```

Check maxmemory:

```
127.0.0.1:6379> CONFIG GET maxmemory
```

It should now return the updated value of 2097152:

```
1) "maxmemory"
2) "2097152"
```

Similarly, maxmemory-policy has also been updated:

```
127.0.0.1:6379> CONFIG GET maxmemory-policy
```

It now reflects the desired value of allkeys-lru:

```
1) "maxmemory-policy"
2) "allkeys-lru"
```

It now reflects the desired value of allkeys-lru:

```
1) "maxmemory-policy"
2) "allkeys-lru"
```

## 4. Clean up your work by deleting the created resources:

```
kubectl delete deployment/redis configmap/example-redis-config
```
