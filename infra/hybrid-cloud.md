---
layout: page
title: Hybrid Cloud
redirect_from:
  - /infra/gitops
---

## Summary

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

Kubernetes (K8s) is a system for automating deployment, scaling, and management of containerised applications. It is a common industry tool for the creation and management of a hybrid cloud infrastructure. Kubernetes is natively supported in many public cloud services, such as Amazon (EKS), Azure (AKS), Google (GKE), etc., as well as implementation in private cloud (e.g., kubeadm, OpenShift, Rancher, juju) and edge compute (microk8s).

Adding Kubernetes to the hybrid cloud tool chain allows research applications to be provisioned in a predictable way within many and varied infrastructural conditions, costing models and operational process. It also provides mechanisms to incorporate integrated and federated routing, security, monitoring, reporting and other functional characteristics. Some of these can be combined to support levels of trust between systems potentially paving the way for more collaboration between research groups and institutions.
