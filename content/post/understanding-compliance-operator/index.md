---
title: Understanding the Compliance Operator
url: /understanding-compliance-operator
subtitle: ""
summary: ""
authors: [ luiscachog ]
tags: [ Security, Operators, OpenShift, Cloud-Native ]
categories: [ Operators, OpenShift, Security, Cloud-Native ]
keywords: [ Compliance, Operator, Security, OpenShift ]
date: 2022-08-16
publishDate: 2022-08-16
lastmod: 2022-08-16
featured: true
draft: false 


# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---

{{% toc %}}

Compliance studies a company’s security processes. It details their security at a moment in time and compares it to a specific set of regulatory requirements. These requirements come in the form of legislation, industry regulations, or standards created from best practices.

Every company can protect its data accordingly if they follow Compliance frameworks and have quality security in place. To have proper protection, companies must understand that Compliance is not the same thing as security. However, security is a big part of Compliance.

Becoming secure and compliant means securing information assets, preventing damage, protecting it, eliminating potential attack vectors and decreasing the system's attack surface.

## Compliance Operator Introduction

The [Compliance Operator](https://github.com/openshift/compliance-operator) was released in OpenShift Container Platform v4.6, and allows OpenShift Container Platform administrators describe the desired compliance state of a cluster and provides them with an overview of gaps and ways to remediate them. The Compliance Operator assesses compliance of both the Kubernetes API resources of OpenShift Container Platform, as well as the nodes running the cluster. The Compliance Operator uses OpenSCAP, a NIST-certified tool, to scan and enforce security policies provided by the content.[^1]

{{% callout warning %}}
The Compliance Operator is available for Red Hat Enterprise Linux CoreOS (RHCOS) deployments only.
{{% /callout %}}

To secure your OpenShift cluster, it is necessary to consider both; platform (Kubernetes/OpenShift API) and host OS (RHCOS) perspectives, because Kubernetes is composed of control plane machines (master) and worker machines (node), then the Kubernetes services such as API Server, etcd, or controller manager run on the control plane to manage the workloads on the worker machines.

{{< figure src="posts/understanding-compliance-operator/rhocp_rhcos.png" caption="Platform and Host Perspective" id="compliance-perspective" theme="ligth">}}

The basic approach to perform hardening in a RHCOS host is described in the [documentation](https://docs.openshift.com/container-platform/4.7/security/container_security/security-hardening.html) and it is based in the [RHEL 8 Security Hardening](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/security_hardening/index#scanning-container-and-container-images-for-vulnerabilities_scanning-the-system-for-security-compliance-and-vulnerabilities). 
For instance, the hardening consists in validate if the file has appropriately restrictive file permissions, if the file ownership is appropriately set, if required systemd services or processes are launched with appropriate arguments or parameters in the configuration, or if the appropriate kernel parameters are set. Then, hardening the control plane is specific to the Kubernetes services and includes the control plane components or the master configuration files. It should validate if the API server has started with restrictive arguments regarding allowing specific admission plug-ins, enabling audit logging, applying etcd server and peer configurations, and restricting RBAC, among others. 
For clusters that use RHCOS, updating or upgrading are designed to become automatic events from the central control plane, because OpenShift completely controls the systems and services that run on each machine, including the operating system itself through the [Machine Config Operator](https://github.com/openshift/machine-config-operator). 

## Why Compliance Operator?

Why is the Compliance Operator needed to validate the hardening and apply changes in the configuration of the operating system and the platform?
The Compliance Operator is defined as follows:

*The Compliance Operator lets OpenShift Container Platform administrators describe the required compliance state of a cluster and provides them with an overview of gaps and ways to remediate them. The Compliance Operator assesses compliance of both the Kubernetes API resources of OpenShift Container Platform, as well as the nodes running the cluster. The Compliance Operator uses OpenSCAP, a NIST-certified tool, to scan and enforce security policies provided by the content.*[^1]

In other words, the Compliance Operator checks the host and the platform to detect gaps in compliance by specifying profiles for scan and creates summary reports about security compliance so that you will be able to find if there is any configuration that violates the policy in the cluster. The reports also show which remediations are applied, so you can choose if you want to apply the recommended configuration by hand, step-by-step, or automatically. In short, the whole process goes like this: choosing a profile for scanning, specifying the scan settings, then initiating the scan, and generating the reports.

{{< figure src="posts/understanding-compliance-operator/rhocp_compliance_operator.png" caption="Compliance Operator Overview" id="compliance-overview" theme="ligth">}}

The Compliance Operator leverages [OpenSCAP](https://www.open-scap.org/), a NIST-certified tool, to scan and enforce security policies, and the security policies for the compliance checks are derived through SCAP content and built from the community-based [ComplianceAsCode/content](https://github.com/ComplianceAsCode/content) project. A bundle of security policies, or profiles created by default when the operator is installed and profiles scheduled include NIST 800-53 Moderate (FedRAMP), Australian Cyber Security Centre (ACSC) Essential Eight, CIS OpenShift Benchmark, and others, so far. You can also create or tailor your own profiles so that you can pick the rules you want to run other than the profiles provided by default.

## How can I implement Compliance Operator in my cluster?

First of all, you will need to install the Compliance Operator, for that, you can follow the [documentation steps](https://docs.openshift.com/container-platform/4.8/security/compliance_operator/compliance-operator-installation.html)

Great, the Operator is installed and functional, now lets check how to create, run and evaluate a compliance scan.



**References:**
[^1]: [https://docs.openshift.com/container-platform/4.8/security/compliance_operator/compliance-operator-understanding.html](https://docs.openshift.com/container-platform/4.8/security/compliance_operator/compliance-operator-understanding.html)
