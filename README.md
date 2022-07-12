# k8s-commands

### List the tainted nodes

Ref https://stackoverflow.com/questions/43379415/how-can-i-list-the-taints-on-kubernetes-nodes

```
kubectl get nodes -o custom-columns=NAME:.metadata.name,TAINTS:.spec.taints --no-headers 
```

### Change persistentVolumeReclaimPolicy to Retain

```
kubectl patch pv pv-name -p '{"spec":{"persistentVolumeReclaimPolicy":"Retain"}}'
```

### Remove claimRef for pvc so that persistentVolume will become available

```
kubectl patch pv pv-name --type json -p '[{"op": "remove", "path": "/spec/claimRef"}]'
```

### Delete Evicted pods in a namespace

Ref https://www.studytonight.com/post/how-to-delete-all-the-evicted-pods-in-kubernetes

```
kubectl get pod -n namesapcename | grep Evicted | awk '{print $1}' | xargs kubectl delete pod -n namesapcename
```

### Exec inside the pod using any one of the shell

```
kubectl exec -i -t -n test test-nginx-6896db5f46-9mhg6 -c nginx -- sh -c "clear; (bash || ash || sh)"
```
