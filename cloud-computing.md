# Cloud Computing - ALi Study

```bash
kubectl create -f 'xxx.yaml'
```
```bash
kubectl get nodes ['xxx']
kubectl get pods [--show-labels [-l 'key=value']] [-n 'namespacename']
kubectl get replicaset
kubectl get [--watch] deployments
kubectl get jobs
kubectl get daemonset
kubectl get 'classname/xxxname' -o [yaml/wide]
kubectl get pvc [-n 'namespacename']
kubectl get pv
kubectl get volumesnapshotconetnt
```
```bash
# configmap、secret
kubectl create configmap [name] [[--from-file]data] [--from-literal]
kubectl create secret [name] [[--from-file]data] [--from-literal] [type]
```
```bash
kubectl delete deployment 'deploymentname/xxxname'
kubectl delete -f 'yamlname'
```
```bash
kubectl exec -it 'podsname' -c 'containername' bash -n 'namespacename'
```
```bash
kubectl port-forward 'svc/app' -n 'namespacename'
```
```bash
kubectl apply --validate -f 'pod.yaml'
```
```bash
kubectl label pods 'podsname' key=value # add a label
kubectl label pods 'podsname' key- # delete label 'key'
```
```bash
kubectl annotate pods 'podsname' key=value
```
```bash
kubectl edit 'xxxname'
```
```bash
kubectl describe deployment 'deploymentname'
kubectl logs 'podsname'
kubectl debug 'podsname'
```
```bash
kubectl set image 'xxx'
```
```bash
kubectl rollout status 'xxxname'
kubectl rollout undo 'classname/xxxname' [--to-revision='x']
kubectl rollout history 'classname/xxxname'
```
```bash
watch 'command' # watch some commands
findmnt 'mountpoint'
```

Telepresence - Service debug
