# [Canal manifests and docs](https://docs.projectcalico.org/latest/getting-started/kubernetes/installation/flannel) have moved to [docs.projectcalico.org](https://docs.projectcalico.org/)

Refer to [Canal/flannel Hosted Install](https://docs.projectcalico.org/latest/getting-started/kubernetes/installation/flannel)
for up to date installation directions and manifests.
This repo is deprecated and no further updates are expected here.

![Canal Logo](https://github.com/projectcalico/canal/raw/master/logos/canal-logo-type-full-color%20328x184.png)

# Policy based networking for cloud native applications

Canal is a community-driven initiative that aims to allow users to easily deploy Calico and flannel networking together as a unified networking solution - combining Calico’s industry-leading network policy enforcement with the rich superset of Calico and flannel overlay and non-overlay network connectivity options.

Canal represents the best-of-breed policy-based networking solution for cloud native applications, supporting any orchestrator that support the CNI network plugin API (including Kubernetes, Mesos, and others).

![Canal Diagram](https://github.com/projectcalico/canal/raw/master/Canal%20Phase%201%20Diagram.png)

Note that the Canal currently uses the Calico and flannel projects as is with no code modifications to either. Canal today is simply a deployment pattern for installing and configuring the projects to work together seamlessly as single network solution from the point of view of the user and orchestration system. In the future the Canal project will likely contribute code changes to Calico and flannel projects to further simplify install and configuration.

## Installing

- [Kubernetes self-hosted install (Recommended)](k8s-install/README.md)
- [CoreOS based Kubernetes cluster](https://github.com/coreos/coreos-kubernetes): This repository provides step-by-step instructions plus several automated install options to bring up a Kubernetes cluster with Canal networking (Calico policy + flannel VXLAN connectivity by default).
- [Step-by-step guide for expert rkt users](InstallGuide.md)
- [Step-by-step guide for expert users creating their own orchestrator integrations](OrchestratorIntegration.md)

### Other automated install options
Watch this space for news of further integration and install instructions coming soon!

## AWS Reference Architectures

There are essentially three options for AWS networking:

- VXLAN encap
- IPIP encap
- native VPC routing.

This will walk through the pros/cons and recommendations.

### Flannel VXLAN

*This is our default recommendation for Canal users today.*

Flannel's VXLAN provides simplicity and performance both inside of an AWS VPC and across VPCs. And with support to deploy simply using kubectl it is easy to get started as well. For most users this is a reasonable default recommended by teams from both CoreOS and Tigera.

**Note:** The AWS VPC will have no ability to enforce routing policy at the Pod level, only at the node level.

### Calico Linux Routing Inside a AZ and IPIP Encap Cross AZ

Because Amazon VPCs are L2 networks Calico can use native Linux routing between hosts in a VPC AZ / subnet. This means that packets flow between hosts using the normal Linux routing logic.

However, Amazon does L3 dest filtering between VPC AZ subnets and so for all routes outside of a VPC subnet Calico will do IPIP encapsulation. IPIP encap has advantages over VXLAN: no L2 announces to track/refresh, packets keep routing with control plane down, slightly fewer bytes for encap.

Customer VPCs must be configured with the correct settings on for the intra-vpc
routing to work correctly (see Calico AWS docs: [Routing Traffic Within a Single VPC Subnet](http://docs.projectcalico.org/latest/reference/public-cloud/aws)).

**Note:** The AWS VPC will have no ability to enforce routing policy at the Pod level, only at the node level.

### Native AWS VPC Routing

*We do not recommend this configuration.*

AWS VPC does have the ability to setup native routes which could be used to setup the Pod networking. However, VPC puts extremely low limits on these route entries (50 per VPC). Because of this limited scalability it is not a recommended configuration.

## Roadmap
- [x] Automated and manual install instructions for CoreOS based Kubernetes clusters
- [x] Step-by-step guide for users creating their own install solutions
- [x] Kubernetes self-hosted installation support (installing Calico & flannel in a single Kubernetes pod, run as a Daemonset)
- [x] Kubernetes self-hosted etcd-less installation support (Calico & flannel using k8s API server only, without direct etcd access)
- [ ] Calico Linux Routing Inside a AZ and IPIP Encap Cross AZ with Kubernetes Datastore
