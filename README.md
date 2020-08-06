# AKS_Architecture

This Azure CLI script deploys a secure Azure Kubernetes Service architecture and related components in your Azure vNet (private network) aligning with the Hub-Spoke topology architecture. In this architecture, AKS cluster nodes hosted within Spoke construct and Application Gateway configured as ingress controller for traffic destined for containers deployed inside Hub construct and management hosts or CI/CD tools deployed in a dedicated subnet under Spoke construct. Hub construct will act as ingress and egress point for network traffic from and to internet for AKS cluster nodes and containers deployed inside Spoke construct. 

The following AKS architecture that is marked with purple dotted line boundary will be deployed using Azure CLI script, other components outside of these dotted lines are deployed via Azure Scaffold templates –



‘Azure Kubernetes Service’ architecture and will describe how to deploy a secure AKS cluster and related components in your Azure vNet (private network) using Azure CLI, aligning with the Hub-Spoke Azure architecture constructed using the template.

The following are key highlights of AKS architecture deployed using Azure CLI script:

• AKS architecture is based on ‘kubenet’ networking model and deployed within Azure vNet (private network).

• Application Gateway Ingress Controller* deployed in the Hub construct aligning with the Azure Scaffold foundation Hub-Spoke architecture. Ingress traffic (internet inbound) traverse through Application Gateway placed in the Hub.

• Egress traffic (Internet outbound only) traverse through Azure Firewall deployed in the Hub construct.

• Azure Kubernetes Service Cluster nodes are deployed in the Spoke (Prod) subscription.

• Azure Container Registry service created in the Spoke using Private Endpoint to retain traffic on a private network from AKS cluster nodes.

• AKS cluster access is secured using ‘Private mode’ – JumpHost is deployed in a separate subnet to administer, access, and manage the AKS cluster. JumpHost subnet can be used for CI/CD tools as well.
