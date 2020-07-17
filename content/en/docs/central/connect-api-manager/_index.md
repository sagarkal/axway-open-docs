---
title: Connect API Manager
linkTitle: Connect API Manager
no_list: true
draft: false
weight: 130
description: >
  Understand why you would connect Axway API Manager to AMPLIFY Central. Learn
  how you can publish to the AMPLIFY Catalog from your API Management System in
  order to obtain a global view of your APIs and present this Catalog to your
  consumers.

  Learn how you can collect the traffic of all your gateways and see it in a single place in AMPLIFY Central Observability.
---
## What is Axway API Manager connected?

Connect your API Management system (v7.6.2 or above) to AMPLIFY Central by using two agents: Discovery and Traceability. These two agents will help you to represent and expose your API Management eco-system in AMPLIFY Central:

* Create an API Gateway environment in AMPLIFY Central that represent your actual API Management eco-system.
* Detect published API using the Discovery Agent. The Discovery Agent discovers the API from API Manager and makes them available in AMPLIFY Central. An API Service in Central is created to reference the API from API Manager and then you can optionally tell the agent to publish it to the AMPLIFY Catalog to allow your consumer to discover it.
* Manage consumer subscription using the Discovery Agent. When a consumer subscribes/unsubscribes to a Catalog asset, the Discovery Agent keeps track of the changes and maintains the API Management system accordingly.  
* Filter the Axway API Gateway logs using the Traceability Agent. The Traceability Agent uses the discovered API to filter API Gateway events to extract the transaction information and send it to the AMPLIFY platform Observability module.

### Discovery Agent

The Discovery Agent is used to discover new published APIs. The Discovery Agent pushes both REST and SOAP API definitions to AMPLIFY Central.

The Discovery Agent discovers APIs that have PassTrough / API Key / OAuth security only.

The related APIs are published to AMPLIFY Central in either disconnected mode (catalog item publication) or connected mode (Environment / API Service publication and optionally as Catalog item). For additional information, see [Discovery Agent](/docs/central/connect-api-manager/deploy-your-agents/#discovery-agent).

![Service Discovery](/Images/central/ServiceDiscoveryAPIM.png)

### Traceability Agent

The Traceability Agent sends log information about APIs that have been discovered and published to AMPLIFY Central.

## Pre-requisites

* An Axway AMPLIFY Central subscription in the AMPLIFY™ platform. See [Get started with AMPLIFY Central](/docs/central/quickstart/).
* An AMPLIFY Central Service Account. See [Manage an API proxy using AMPLIFY CLI](/docs/central/cli_central/cli_proxy_flow/).

## System requirements

* Axway API Manager / Axway API Gateway versions 7.6.2 SPx and 7.7 SPx up and running using a Linux installation (CentOS 6.x, 7.x, 8.x,  Oracle Linux 6.x, 7.x, Red Hat Enterprise Linux 6.x, 7.x, 8.x, SUSE Linux Enterprise Server 11.x, 12.x).
* The Linux machine where API Manager and API Gateway are running must be accessible and have `sudo` rights to run the Agents.
* The agents must be installed on the same machine that API Manager and/or API Gateway is running.

## Connect Axway API Manager to AMPLIFY Central quickstart

The following gives you a high-level overview of the required steps to connect a API-Manager V7 environment to AMPLIFY-Central.

### Create a new Service Account

* The agent will use this service account for the secure communication with the AMPLIFY platform
* Learn more how to install the CLI and create a service account. See [Install AMPLIFY Central CLI](/docs/central/cli_central/cli_install).

### Add your environment to Central

* Add your environment to AMPLIFY Central using either the [AMPLIFY Central CLI](/docs/central/cli_central/cli_environments/) or [the UI](/docs/central/mesh_management/add_env/#add-your-environment-to-amplify-central).

### Install and prepare the Discovery Agent

* The agent will be installed in the actual runtime environment and is using the service account for the communication with the platform
* Learn more how install and setup the [Discovery Agent](/docs/central/connect-api-manager/deploy-your-agents/#discovery-agent)*

### Install and prepare the Traceability Agent

* Quite similar to the discovery agent, also the Traceability Agent needs to be installed and configured in your environment
* Learn more how install and setup the [Traceability Agent](/docs/central/connect-api-manager/deploy-your-agents/#traceability-agent)*

\* If you need assistance setting up the configuration files, please contact your Sales Account Manager, or open a support ticket on support.axway.com.

The following demonstrates how to connect an Axway API-Gateway V7 to AMPLIFY-Central.

{{< youtube kugRyYVw5nI >}}