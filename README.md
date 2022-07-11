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
