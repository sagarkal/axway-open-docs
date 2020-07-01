---
title: Network traffic - API Manager
linkTitle: Network traffic - API Manager
draft: false
weight: 120
description: Traffic is always initiated by the Agent to API Manager, API Gateway, and AMPLIFY Central. No sessions are ever initiated back to the Agent.
---
{{< alert title="Note" color="primary" >}}The Axway API Gateway connectivity to AMPLIFY Central is currently available in an alpha review mode; current functionality and configuration may change before release. Therefore, this connectivity is available for trial use only and is not supported for production API management or connectivity.{{< /alert >}}

## Data destinations

The destination for:

* Agent Authentication data is `login.axway.com`

* API definition (Swagger or WSDL) and API documentation data is `apicentral.axway.com`

* API Event data, the transaction summary and headers, is `ingestion-lumberjack.datasearch.axway.com`

## Communication ports

All outbound traffic is sent over SSL via TCP / UDP.

Open the following ports so that agents can communicate to the AMPLIFY platform:

**Outbound**:

| Host                                       | Port               | Protocol  | Data                                |
|--------------------------------------------|--------------------|-----------|-------------------------------------|
| apicentral.axway.com                       | 443                | HTTPS     | API definitions, Subscription info  |
| login.axway.com                            | 443                | HTTPS     | Authentication                      |
| ingestion-lumberjack.datasearch.axway.com  | 453                | TCP       | API event data                      |

Other ports which may need to be opened so that the Agent may monitor API Gateway / Manager are:

**Internal**:

| Host                                       | Port               | Protocol  | Data                                |
|--------------------------------------------|--------------------|-----------|-------------------------------------|
| API Manager Host                           | 8075 (default)     | HTTPS     | API Discovery                       |
| API Gateway Host                           | 8090 (default)     | HTTPS     | API Transaction Header data (see [APIGATEWAY GETHEADERS](/docs/central/traceability-agent-variables/))        |

**Inbound (used for the agent status server)**:

| Host                                       | Port               | Protocol  | Data                                |
|--------------------------------------------|--------------------|-----------|-------------------------------------|
| Agent Host(s)                              | 8989 (default)     | HTTPS     | Serves the status of the agent and its dependencies for monitoring  |

## Using proxies

If your network policy restricts outbound traffic such that outbound traffic must pass through a proxy. A proxy can be configured in the configuration file of the agents.

### HTTP/HTTPS Proxy

Use a HTTP/HTTPS Proxy for communication to the AMPLIFY Platform.  This configuration is set for both the [Discovery](/docs/central/discovery-agent-variables/) and [Traceability Agents](/docs/central/traceability-agent-variables/).

### SOCKS5 Proxy

Use a SOCKS5 Proxy for communication to the AMPLIFY Platform when sending API Traffic Events.  This configuration is set only for [Traceability Agents](/docs/central/traceability-agent-variables/).

### Proxy authentication

Both proxy types will use one of two authentication mechanisms, none or username/password authentication. The username authentication is specified within the URL `http://{user}:{pass}@{proxy}:{port}`.
