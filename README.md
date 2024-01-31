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
### List the namespaced resources

Ref: https://kubernetes.io/docs/reference/kubectl/#resource-types

```
kubectl api-resources --verbs=list --namespaced
```

### List the cluster level resources

```
kubectl api-resources --verbs=list --namespaced=false
```

### List both namespaced and cluster level resources with verbs

```
kubectl api-resources --verbs=list -o wide
```
### Get the full list of verbs

```
kubectl proxy
```
Run below command from other terminal

```
curl localhost:8001/api/v1
```

### Check your access

Ref: https://kubernetes.io/docs/reference/access-authn-authz/authorization/

```
kubectl auth can-i create deployments --namespace test
```

```
kubectl auth can-i list secrets --namespace test
```


Trigger Cronjob Manually

Ref: https://www.craftypenguins.net/blog/how-to-trigger-a-kubernetes-cronjob-manually/

```
kubectl create job --from=cronjob/<name of cronjob> <name of job>
```

For example, if the name of your cronjob is “pgdump”, then you might run:

```
kubectl create job --from=cronjob/pgdump pgdump-manual-001
```

To see a list of cron jobs, run `kubectl get cronjob`


Output decoded secrets without external tools

```
kubectl get secret my-secret -o go-template='{{range $k,$v := .data}}{{"### "}}{{$k}}{{"\n"}}{{$v|base64decode}}{{"\n\n"}}{{end}}'
```

List Events sorted by timestamp

```
kubectl get events --sort-by=.metadata.creationTimestamp
```
Delete all released pv's

```
kubectl get pv | tail -n+2 | awk '$5 == "Released" {print $1}' | xargs -I{} kubectl delete pv {}
```
Assign the External IP for LoadBalancer manually.

```
kubectl patch svc frontend -p '{"spec":{"externalIPs":["192.168.0.194"]}}'
```

Delete pvc's using shell script `delete_pvc.sh`

```
alias k='kubectl -n $n'
n=default
for pvc_name in $(k get pvc | grep pvcname | grep -v phani | awk '{print $1}'); do
    echo " delete $pvc_name "
    k delete pvc "$pvc_name"
done
```

```
sh delete_pvc.sh
```

Delete Released pv's using shell script `delete_pv.sh`

```
for pv_name in $(kubectl get pv | grep Released | awk '{print $1}'); do
    echo " delete $pv_name "
    kubectl delete pv "$pv_name"
done
```

```
sh delete_pv.sh
```
Get the configmap in proper format

```
kubectl -n kube-system get configmap coredns -o go-template={{.data.Corefile}}
```

Create basic auth secret
```
htpasswd -c auth foo

kubectl create secret generic basic-auth --from-file=auth
```

Add below annotations to ingress

```
annotations:
  kubernetes.io/ingress.class: nginx
  nginx.ingress.kubernetes.io/auth-secret: basic-auth
  nginx.ingress.kubernetes.io/auth-type: basic
```

Git submodule commands

```
git submodule add git@bitbucket.org:myproject/myrepo.git mydata

git submodule update --init --recursive

git submodule update --recursive --remote
```
Check the ssl certificate information

```
openssl x509 -subject -noout -in certificate.crt
```
for full information

```
openssl x509 -in certificate.crt -text -noout
```

Delete evicted pods from all namespaces

```
kubectl get pods --all-namespaces -o json | jq '.items[] | select(.status.reason!=null) | select(.status.reason | contains("Evicted")) | "kubectl delete pods \(.metadata.name) -n \(.metadata.namespace)"' | xargs -n 1 bash -c
```

Delete evicted pods from specific namespace

```
kubectl get pod -n test | grep Evicted | awk '{print $1}' | xargs kubectl delete pod -n test
```

