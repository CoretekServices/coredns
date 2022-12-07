# Azure Container Instance Core DNS Server

This can be used to provide a DNS forwarder for private DNS Zones without paying for the DNS resolver service from Azure.

```bash
az container create \
  --resource-group rg-coredns \
  --location eastus \
  --memory 0.5 \
  --name dns-forwarder \
  --image coredns/coredns:latest \
  --ports 53 \
  --protocol UDP \
  --ip-address Private \
  --gitrepo-url https://github.com/redocksoft/coredns-config-azure-forwarder \
  --gitrepo-mount-path /config \
  --command-line "/coredns -conf /config/Corefile" \
  --subnet /subscriptions/1cc95653-d611-4000-8ebf-afcf552fbe13/resourcegroups/rg-coredns/providers/Microsoft.Network/virtualNetworks/vnet-coredns/subnets/default \
  --restart-policy onFailure
```

## Steps to recreate

Create a new virtual network or create a subnet in an existing virtual network for CoreDNS container instances.
Run the command above to create a new container instance in the subnet of your choice - either a new one or existing.

