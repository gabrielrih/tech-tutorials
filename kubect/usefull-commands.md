# Usefull commands

Getting all logs from all pods in a deployment:
```sh
kubectl logs deployment/ingress-nginx-controller -n ingress-internal --all-containers=true > nginx-logs.txt
```
