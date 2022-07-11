# k8s-commands

### List the tainted nodes

Ref https://stackoverflow.com/questions/43379415/how-can-i-list-the-taints-on-kubernetes-nodes

```
kubectl get nodes -o custom-columns=NAME:.metadata.name,TAINTS:.spec.taints --no-headers 
```

### Change persistentVolumeReclaimPolicy to Retain

```
kubectl patch pv pvc-011346fb-0597-4cc1-8984-2234b00c6790 -p '{"spec":{"persistentVolumeReclaimPolicy":"Retain"}}'
```
