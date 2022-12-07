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
  --gitrepo-url https://github.com/CoretekServices/coredns \
  --gitrepo-mount-path /config \
  --command-line "/coredns -conf /config/Corefile" \
  --subnet /subscriptions/1cc95653-d611-4000-8ebf-afcf552fbe13/resourcegroups/rg-coredns/providers/Microsoft.Network/virtualNetworks/vnet-coredns/subnets/default \
  --restart-policy onFailure
```

## Steps to recreate

Create a new virtual network or create a subnet in an existing virtual network for CoreDNS container instances.
Run the command above to create a new container instance in the subnet of your choice - either a new one or existing.
  The end result should be a container instance deployed with a private IP address which will used later --- keep note of it.
Create a new private dns zone for internal resolution or private endpoint
  Create some dummy records for testing
attach the private dns zone using viratul network links to the virtual network where the CoreDNS server is deployed 
  Caveat - You cannot use "bring your own DNS servers" when using this option - this will require some additional testing if required.
Tom's recommendation - deploy a dedicate vNet, RG, and Container instance, and peer that to the hub

To test: you can deploy a virtual machine which can access the coredns container instance using the private address and test using nslookup `nslookup test.coreteklabs.local <private IP of the container instance>`

The results should match the configuration in the private dns zone.