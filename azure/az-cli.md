# az cli

Login: 
```sh
az login --use-device-code
```

Set subscription
```sh
az account set --subscription a4e61776-xxxx-4cc3-919f-486decb4e509
```

Get the kubernetes vnet:
```sh
az aks show --name aks-nonprod-basic --resource-group AKS-RG-NON-PROD | grep vnetSubnetId
```