# AKS_Architecture

This Azure CLI script deploys a secure Azure Kubernetes Service architecture and related components in your Azure vNet (private network) aligning with the Hub-Spoke topology architecture as discussed in the Azure Scaffold templates blog <p><a href="https://techcommunity.microsoft.com/t5/azure-architecture-blog/azure-scaffold-templates/ba-p/1455574">(link)</a>. In this architecture, AKS cluster nodes hosted within Spoke construct and Application Gateway configured as ingress controller for traffic destined for containers deployed inside Hub construct and management hosts or CI/CD tools deployed in a dedicated subnet under Spoke construct. Hub construct will act as ingress and egress point for network traffic from and to internet for AKS cluster nodes and containers deployed inside Spoke construct. 

The following AKS architecture that is marked with purple dotted line boundary will be deployed using Azure CLI script, other components outside of these dotted lines are deployed via Azure Scaffold templates –

![Architecture Image](https://github.com/Sumitkup1/AKS_Architecture/raw/master/aks_architecture_v01.png)


The following are key highlights of AKS architecture deployed using Azure CLI script:

* AKS architecture is based on ‘kubenet’ networking model and deployed within Azure vNet (private network).
* Application Gateway Ingress Controller* deployed in the Hub construct aligning with the Azure Scaffold foundation Hub-Spoke architecture. Ingress traffic (internet inbound) traverse through Application Gateway placed in the Hub.
* Egress traffic (Internet outbound only) traverse through Azure Firewall deployed in the Hub construct.
* Azure Kubernetes Service Cluster nodes are deployed in the Spoke (Prod) subscription.
* Azure Container Registry service created in the Spoke using Private Endpoint to retain traffic on a private network from AKS cluster nodes.
* AKS cluster access is secured using ‘Private mode’ – JumpHost is deployed in a separate subnet to administer, access, and manage the AKS cluster. JumpHost subnet can be used for CI/CD tools as well.


As part of this scripted deployment, the following Azure resources are deployed:

* AKS Nodes Subnet
* Ingress Subnet
* Private Endpoint Subnet
* Route tables for AKS nodes and Application Gateway Ingress Controller
* Service principal
* AKS Cluster
* Log Analytics Workspace
* Azure Container Registry


## Deployment of AKS environment:

There are two sections in aks_architecture_v01.azcli script--

1. Deploy components in Hub subscription
2. Deploy AKS cluster in Spoke subscription

Note - You can create separate out these sections into two separate scripts if login credentials for Hub and Spoke subscriptions are different. 

### Manual Step:

In this architecture, Application Gateway Ingress Controller (AGIC) is deployed in Hub subscription in an isolated subnet with a separate Route table to create complete end-to-end private vNet AKS cluster, therefore you will have to add static route to the table associated to AGIC subnet when you provision a container in your cluster environment.

This step can be executed using CI/CD pipeline after container has been created and following CLI command can be used to update AGIC route table.

Note - You will have to update --address-prefix and --next-hop-ip-address parameters values.

##### az network route-table route create --resource-group $ask_ingress_rsg --name aks-nodepool1-32505125-vmss000000 --route-table-name $aks_ingress_udr_name --address-prefix 10.244.0.0/24 --next-hop-type VirtualAppliance --next-hop-ip-address 10.0.33.5
