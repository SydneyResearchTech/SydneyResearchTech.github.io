---
title: Kubernetes
---

## Summary

Kubernetes, also known as K8s, is an open-source system for automating deployment, scaling, and management of containerised applications.

## Core infrastructural components

These services are the core components of all Research Technology Kubernetes deployments.

### Certificate management (X.509)

Certificate management in K8s allows application maintainers to specify X.509 requirements within the deployment manifests.
Upon an X.509 signing request the K8s plugin will generate the certificate signing request (CSR) and submit the request to the appropriate signing authority.
When the certificate signing authority has verified and signed the CSR the K8s X.509 manager will ensure the valid certificates are put in the appropriate locations and loaded within the corresponding application.
At regular intervals each certificate is assessed to ensure it's continued validity and will be automatically renewed prior to the expiry date.

[cert-manager](https://cert-manager.io/docs/) is utilised to support Public Key Infrastructure (PKI) services and can be used with [Let's Ecrypt](https://letsencrypt.org/) when an institutional PKI is not available.

### External DNS

[ExternalDNS](https://github.com/kubernetes-sigs/external-dns) is a  mechanism to manage DNS providers and control DNS records dynamically via Kubernetes resources. Please see the link at the start of this section to find supported providers.

### Ingress and Load Balancer controller

Ingress controllers reside at layer 7 of the OSI model and are instrumental in the reliability, performance and security of web based access to services. It is industry best practice to ensure that services are not directly accessed via the Internet and that an ingress controller (also known as reverse proxy or web accelerator) are positioned as an edge device.

Load balancers reside at layer 4 of the OSI model and provide resilience and performance of low level service endpoints. Some examples would be file transfer services, instrumentation communications, virtual private network (VPN) endpoints, etc.

Ingress and load balancer controllers are deployment specific services as this feature commonly relies on hardware implemented within the wider infrastructure. We will talk about common configuration patterns and touch on a small number of specific deployments, however an extensive discussion is outside the scope of this document.

| Infrastructure | Service | K8s integration |
| -------------- | ------- | --------------- |
| OpenStack | Octavia |
| Amazon EKS | ALB + NLB |
| Azure AKS | Azure LB |
| Google GKE |
| Private cloud | See bellow |
| Standalone | None | [MetalLB](https://metallb.universe.tf/) |

*Private cloud* is segregated out as this is highly dependent on the infrastructure teams selection of hardware or service provider.
Many load balancer hardware vendors now provide Kubernetes compliant controller functionality as part of their service offerings.
This would need to be assessed as part of the purchasing process and configured as per the vendor instructions.
Some examples follow with no perceived preference or recommendation to demonstrate this point:
* [F5 BIG-IP Controller for Kubernetes](https://clouddocs.f5.com/products/connectors/k8s-bigip-ctlr/v1.8/)
* [Kemp Ingress Controller for Kubernetes](https://kemptechnologies.com/techpreviews/kubernetes)
* [NetScaler; Citrix ADC ingress controller for Kubernetes](https://docs.netscaler.com/en-us/citrix-k8s-ingress-controller.html)
* CloudFlare Load Balancer or Argo Tunnel Ingress Controller
  * [Using Kubernetes on GKE and AWS with Cloudflare Load Balancer](https://developers.cloudflare.com/support/third-party-software/others/using-kubernetes-on-gke-and-aws-with-cloudflare-load-balancer/)
  * [Cloudflare + Kubernetes](https://www.cloudflare.com/en-au/integrations/kubernetes/)

### Persistent volume (CSI)
### Autoscaler
### Logging and metrics

In production Kubernetes system, a centralised cluster-wide logging solution is required to allow administrators to examine historical logs and analyze trends. Without such a system, the application logs can be lost while pods are replaced during failure or evicted from its node when a scaling down event is triggered. Since Kubernetes doesn't provide any kind of centralised logging, addtional components are often required. These components often consist of log gathering agent, centralised index management system and a web tool for visualing the data. 

We use FluentD agents, which are deployed as pods on all nodes inside the cluster, gather logs from all application containers, tagging with pod-specific information, assgining them with pre-defined index name before sending them to OpenSearch engine.

OpenSearch is community-driven open sourced project that includes OpenSearch (derived from ElasticSearch 7.10.2) and OpenSearch Dashboard (derived from Kibana 7.10.2). We utilise Amazon OpenSearch Service which is an AWS-managed service that lets you run and scale OpenSearch clusters.

* https://opensearch.org/
* https://aws.amazon.com/what-is/opensearch/

