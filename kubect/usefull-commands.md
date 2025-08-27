# Usefull commands

Autheticating using a environment variable
```sh
$Env:KUBECONFIG="./aks-name.yaml"
```

Connecting to a POD:
```sh
kubectl exec --stdin --tty copy-customer-accounts-54c668d4f-ztpgt --namespace cloudeng-tools -- /bin/bash
```

Getting all logs from all pods in a deployment:
```sh
kubectl logs deployment/ingress-nginx-controller -n ingress-internal --all-containers=true > nginx-logs.txt
```

Getting all pods using an specific nodepool called "cloudeng"
```sh
kubectl get pods --all-namespaces -o wide --field-selector=spec.nodeName=$(kubectl get nodes -l agentpool=cloudeng -o jsonpath='{.items[0].metadata.name}')
```

Remove all pods in FAILED status in a specific namespace
```sh
kubectl get pods --field-selector=status.phase=Failed -n cattle-system -o json | kubectl delete -f -
```

Remove all pods in FAILED status in ANY namespace
```sh
kubectl get pods --field-selector=status.phase=Failed -A -o json | kubectl delete -f -
```

Remove all pods in SUCCEEDED/COMPLETED status in a specific namespace
```sh
kubectl get pods --field-selector=status.phase=Succeeded -n database-monitoring -o json | kubectl delete -f -
```

