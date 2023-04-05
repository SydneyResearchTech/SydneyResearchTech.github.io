---
title: Hybrid Cloud
redirect_from:
  - /infra/gitops
authors:
  - Dean Taylor <dean.taylor@sydney.edu.au>
---

# Summary

Research Technology group has taken the initiative to prioritise a Hybrid Cloud model for research infrastructure deployment and long term support. A Hybrid Cloud allows for a support model that provides:
* Rapid development and migration to production ready services
* The ability to provide services within a context and/or location that aligns to the best outcome for the project
* Removes many restrictions imposed by more traditional technology approaches

Hybrid Cloud is a mixture of computing, storage, and service environments made up of public cloud, private cloud, on-premises infrastructure and edge compute. While this provides obvious advantages, especially to research oriented infrastructure, there needs to be consideration into how best to manage this level of complexity.

The University of Sydney, Research Technology group utilises GitOps practices to effectively manage multiple Kubernetes cluster deployments. The flexible structure allows oversight and administration of cloud, infrastructure and application life cycle. This technique provides a number of advantages including but not limited to:
* Greatly reduces the total cost of ownership
* Segregates out subject mater experts and business practices to better manage workflows and compliance
* Provides reproducible outcomes
* Enhances collaboration and visibility

GitOps is an infrastructure operational framework that utilises the advantages of version control, collaboration, compliance, and continuous integration/continuous delivery (CI/CD).

Kubernetes (K8s) is a system for automating deployment, scaling, and management of containerised applications. It is a common industry tool for the creation and management of a hybrid cloud infrastructure. Kubernetes is natively supported in many public cloud services, such as Amazon ([EKS](https://aws.amazon.com/eks/){:target="_blank"}), Azure ([AKS](https://azure.microsoft.com/en-au/products/kubernetes-service/){:target="_blank"}), Google ([GKE](https://cloud.google.com/kubernetes-engine){:target="_blank"}), etc., as well as implementation in private cloud (e.g., [kubeadm](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/]{:target="_blank"}, [OpenShift](https://www.redhat.com/en/technologies/cloud-computing/openshift){:target="_blank"}, [Rancher](https://www.rancher.com){:target="_blank"}, [Juju](https://juju.is){:target="_blank"} using [CharmHub](https://charmhub.io/charmed-kubernetes){:target="_blank"}) and edge compute ([Microk8s](https://microk8s.io){:target="_blank"}).

Adding Kubernetes to the hybrid cloud tool chain allows research applications to be provisioned in a predictable way within many and varied infrastructural conditions, security contexts, costing models and operational process. It also provides mechanisms to incorporate integrated and federated routing, security, monitoring, reporting and other functional characteristics. Some of these can be combined to support levels of trust between systems potentially paving the way for more collaboration between research groups and institutions.

# Security

## Policy as Code

Calico, network/security team
Istio, security team, policy per application and cross infrastructure interaction

# Cost

# Infrastructure as Code (IaC)

# Application life cycle management

# Collaboration

# Cloud components

| Cloud component           | Kubernetes component |
| ---------------           | -------------------- |
| Containers                | Native Kubernetes |
| Serverless & Event driven | [Knative](https://knative.dev/docs/){:target="_blank"} |
| WASM                      | [Krustlet](https://krustlet.dev){:target="_blank"} |

# Glossary

* Microservice
  * Microservices is an architectural design for building a distributed application. Microservices break an application into independent, loosely-coupled, individually deployable services. This can be achieved with any combination of container, serverless, or event driven code components.
* Kubernetes (K8s)
  * K8s is a system for automating deployment, scaling, and management of containerised applications.
* Cloud, multi-cloud, hybrid-cloud
  * Cloud computing is the on-demand availability of computer system resources, especially data storage (cloud storage) and computing power, without direct active management by the user.
  * A hybrid cloud infrastructure blends two or more different types of clouds, while multi-cloud blends different clouds of the same type.
* Infrastructure as Code (IaC)
  * IaC is the process of managing and provisioning computer data centers through machine-readable definition files.
* GitOps
  * GitOps is an infrastructure operational framework that utilises the advantages of version control.
* Edge computing
  * Edge computing is a part of a distributed computing topology in which information processing is located close to the edge where things and people produce or consume that information.
