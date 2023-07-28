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

## Connecting in a Virtual Machine using Azure Active Directory

You can use this commands to connect to a Virtual Machine using SSH and Bastion.

```sh
az login --use-device-code

az account set --subscription "bd1d7558-xxxx-4164-bbd5-ca1ba9bac4af"

az network bastion ssh --name "my-bastion" --resource-group "my-bastion-rg" --target-resource-id "id-of-the-target-virtual-machine" --auth-type "AAD"
```

