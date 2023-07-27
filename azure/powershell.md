# Azure Powershell

Set subscription:
```sh
Set-AzContext -Subscription "a4e61776-xxxx-4cc3-919f-486decb4e509"
```

Get subscription which are beeing used:
```sh
Get-AzContext
```

Connect with using specific Tenant (SSO)
```sh
Connect-AzAccount -TenantId cef04b19-7776-4a94-b89b-375c77a8f936
```
