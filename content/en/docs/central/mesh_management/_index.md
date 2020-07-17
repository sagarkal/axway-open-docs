---
title: Mesh management
linkTitle: Mesh management
weight: 120
date: 2019-07-30
description: Understand what mesh governance is, what a hybrid environment is, and how you can manage the APIs and microservices in a hybrid environment from AMPLIFY Central.
---

{{< alert title="Public beta" color="warning" >}}This feature is currently in **public beta** and not yet available for production use.{{< /alert >}}

## What is mesh governance?

AMPLIFY Central *mesh governance* enables you to govern and manage your APIs, public and private services, along with the hybrid environments where they are located. AMPLIFY Central provides a centralized SaaS control plane, and you define the data plane where the governance policies are enforced (Axway public cloud or your private cloud).

AMPLIFY Central mesh governance provides the following key capabilities:

* Manage your public and private services, wherever they are located
* Add a service mesh layer to your on-premise or private cloud hybrid environments
* Manage your mesh policies along with their related services and associated APIs
* Connect and manage those hybrid environments and their service meshes

The mesh governance capability of AMPLIFY Central currently supports adding a service mesh layer to an Amazon EC2 private cloud environment. This will be extended in the future to include other private cloud environments (for example, Microsoft Azure or Red Hat OpenShift).

## What is a hybrid environment?

AMPLIFY Central provides a central control plane, hosted in Axway public cloud, which manages your API traffic across multiple cloud and on-premise environments. It can manage data planes in the Axway public cloud as well as in numerous connected private cloud hybrid environments. In AMPLIFY Central, *hybrid* means working across SaaS, multi-cloud, and on-premise environments.

![AMPLIFY Central control plane](/Images/central/hybrid_control_data_plane.png)

### Control plane

The control plane is where you manage the API traffic flowing through the data plane. It is managed by Axway in the AMPLIFY Central public cloud and is common to all data planes and hybrid environments.

### Data plane

The data plane is where API transactions and related user microservices are hosted. The data plane is wherever you want it to be, for example, it can be Axway managed, or customer managed using Kubernetes, Amazon EKS, Google Kubernetes Engine, and so on.

The data plane in an AMPLIFY Central hybrid environment is logically split into a service mesh data plane and control plane.

* Service mesh data plane – Consists of a set of intelligent proxies (Envoy) deployed as sidecars on your microservices.
* Service mesh control plane – Axway mesh agents manage Istio, which in turn manages and configures the proxies to route traffic. Istio also controls how Envoy exposes proxies and routes traffic inside the mesh.

For more information on Istio and Envoy, see the [Istio documentation](https://istio.io/latest/docs/).

### AMPLIFY Central hybrid environment

An AMPLIFY Central hybrid environment is the private cloud environment that contains your microservices, with the addition of the Axway proprietary service mesh layer.

The service mesh layer separates the AMPLIFY Central public cloud control plane from your on-premise environments, while it allows the transaction data to stay in the local private cloud. This way, you can manage your microservices and APIs (both internal and external) locally.

## Axway mesh agents

Axway mesh agents provide the secure connection between your AMPLIFY Central hybrid environments and the AMPLIFY Central public cloud. A mesh agent runs in the service mesh in your hybrid environment and enables you to manage your microservices from AMPLIFY Central.

### Service discovery agent

The service discovery agent (SDA) listens for new services coming up in your hybrid environment and publishes the service information, and any API definitions found for the service, to AMPLIFY Central.

### Configuration synchronization agent

The configuration synchronization agent (CSA) takes policies and rules from AMPLIFY Central (such as proxy deployment information, security information, and so on) and transfers them to the hybrid environment where they are used by the service mesh layer to manage API transactions and service activity.

### Telemetry agent

The telemetry agent (TA) sends metrics and logs for API activity back to AMPLIFY Central so that you can monitor service activity and troubleshoot your services. Only a summary of the API transaction is captured and sent to AMPLIFY Central. Headers and payload remain in the hybrid data plane and can be operated on by other native tools.
