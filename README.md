# Azure: AKS Security Bootcamp </br> Implementing network security for AKS workloads

## Welcome

In this AKS-focused workshop, you will work with Azure and Calico Cloud to learn how implement network security for workloads to reduce the attack surface of applications running on AKS clusters.  

Cloud-native applications require a modern approach based on the zero-trust principles of identity-based access, least privilege access, and proactively detecting threats and reducing the blast radius in case of a breach.

Calico Cloud enables fine-grained, secure workload access controls between your microservices and external databases, cloud services, APIs, and other applications. It also prevents the lateral movement of threats with identity-aware segmentation that works across all of your workload environments, including hosts, VMs, Kubernetes components, and services.

You will come away from this workshop with an understanding of how others in your industry are securing and observing cloud-native applications in Azure, along with best practices that you can implement in your organization.

### Time Requirements

The estimated time to complete this workshop is 60-90 minutes.

### Target Audience

- Cloud Professionals
- DevSecOps Professional
- Site Reliability Engineers (SRE)
- Solutions Architects
- Anyone interested in Calico Cloud :)

### Learning Objectives

1. Learn how to deploy zero-trust workload access controls with namespace isolation recommendations
2. Extend firewall protection at the granular, workload level
3. Block lateral movement of APTs with identity-aware microsegmentation
4. Understand how to apply zero-trust security controls at application level.

## Workshop Environment Preparation

> [!WARNING]
> **For this workshop, you are expected to have access to a previously created AKS cluster.**

- Please, follow the instructions on the repository below if you don't have it ready:

  [Calico Cloud on AKS - Workshop Environment Preparation](https://github.com/tigera-solutions/aks-workshop-prep.git)

- We will run this workshop from the Azure Cloud Shell, as described in that repository.

- To start your cluster, reload the environment variables create in your Azure Cloud Shell first and then start the cluster. Use the following command:
  
  ```bash
  source ~/workshopvars.env
  az aks start --resource-group $RESOURCE_GROUP --name $CLUSTERNAME
  ```

## Modules

This workshop is organized in sequential modules. One module will build up on top of the previous module, so please, follow the order as proposed below.

Module 1 - [Connect the AKS cluster to Calico Cloud](modules/module-1-connect-calicocloud.md)  
Module 2 - [Workload Access Control with Namespace Isolation Recommendation](modules/module-2-ztac-ns-isolation.md)  
Module 3 - [Blocking Lateral Movement with Microsegmentation](modules/module-3-wkload-isolation.md)  
Module 4 - [Ingress and Egress access control using NetworkSets](modules/module-4-network-sets.md)  
Module 5 - [Application Level Observability](modules/module-5-application-observability.md)  
Module 6 - [Clean up](modules/module-6-clean-up.md)  

## Useful links

- [Project Calico](https://www.tigera.io/project-calico/)
- [Calico Academy - Get Calico Certified!](https://academy.tigera.io/)
- [O’REILLY EBOOK: Kubernetes security and observability](https://www.tigera.io/lp/kubernetes-security-and-observability-ebook)
- [Calico Users - Slack](https://slack.projectcalico.org/)

**Follow us on social media:**

- [LinkedIn](https://www.linkedin.com/company/tigera/)
- [Twitter](https://twitter.com/tigeraio)
- [YouTube](https://www.youtube.com/channel/UC8uN3yhpeBeerGNwDiQbcgw/)
- [Slack](https://calicousers.slack.com/)
- [Github](https://github.com/tigera-solutions/)
- [Discuss](https://discuss.projectcalico.tigera.io/)

> [!NOTE]
> The examples and sample code provided in this workshop are intended to be consumed as instructional content. These will help you understand how Calico Cloud can be configured to build a functional solution. These examples are not intended for use in production environments.