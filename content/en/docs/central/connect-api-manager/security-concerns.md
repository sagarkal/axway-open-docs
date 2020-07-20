---
title: Security concerns
description: >-
  This section summarizes the security aspects tied to the agents:

  how agents communicate with the Axway AMPLIFY Platform and what kind of data is sent?
---
## Connectivity with Axway AMPLIFY Platform

![Agent networking](/Images/central/connect-api-manager/agents-proxy2.png "Agent networking")

All communications are initiated by the agents so there are no requirements to change the inbound rules on your network infrastructure. Connections from the agents to Axway AMPLIFY platform are secured using TLS/SSL which use strong cipher suites. By default, TLS 1.2 is used. See [SSL/TLS advanced](https://docs.axway.com/bundle/axway-open-docs/page/docs/central/connect-api-manager/ssl-tls-advanced/index.html) for more information if you want to change the default cipher suites.

### Setting up the proxy configuration

If the DMZ where the API Management system is located cannot access the Internet world, then a proxy will be required. The following urls must be accessible from where the agents are installed (typically on the same machine where the API Management system is located) and added to the proxy configuration:

* Protocol: TCP - url: `https://apicentral.axway.com`  (Discovery / Traceability Agents)
* Protocol: TCP - url: `https://login.axway.com` (Discovery / Traceability Agents)
* Protocol: socks5 - url: `ingestion-lumberjack.datasearch.axway.com:453` (Traceability Agent)

In order for the agent to communicate correctly with the Axway platform, the agent configuration provides variables to connect through the proxy:

Discovery Agent: **central.proxyurl**

sample: `central.proxyurl: http[s]://username:password@hostname:port`

Traceability Agent: **output.traceability.proxyurl**

sample: `output.traceability.proxyurl: socks5://username:password@hostname:port`

### Authenticating to Axway AMPLIFY Platform

Once the agents get through the proxy, the authentication with Axway AMPLIFY platform is a Private Key JWT Client Authentication. Private Key JWT Client Authentication is an authentication method that can be used by clients to authenticate to the authorization server when using the token endpoint. In this authentication mechanism, only the clients that have registered a public key, signed a JWT using that key, can authenticate.

Axway platform knows the public key and the agents know the private key. A service account is created on Axway AMPLIFY platform with the public key. Agents sign their request with their private key, send the request using the service account identifier and the Axway AMPLIFY platform decodes it with the corresponding public key. The private key is never exchanged with the platform and it remains on premise.  See [Prepare AMPLIFY Central / Create a Service Account](https://docs.axway.com/bundle/axway-open-docs/page/docs/central/connect-api-manager/prepare-amplify-central/index.html) for instructions on creating the public/private key pair and assigning the public key to the service accounts.

## Connectivity with API Manager/API Gateway

Since the agents are installed on the same machine as the API management system, `localhost` url can be used in the agent configuration to connect to API Manager/Gateway. The ports are the same as the ones used to access the API Management system UI (`8075` for API Manager and `8090` for API Gateway Manager by default). Refer to `apimanager.host` / `apimanager.port` and `apigateway.host` / `apigateway.port` variables.

Each agent requires a specific user to access the API Management system. It is recommended to use a dedicated user in order to avoid conflicts with existing users and to allow an easier follow up.

### Discovery agent user

In order to be able to use API Manager APIs across all organizations, the Discovery Agent user must have an "API Manager Administrator" or "Organization administrator" role. With the "Organization administrator" role, the user can only discover APIs that belong to the same organization that he belongs to. If APIs from other organizations need to be discovered, then the user must be granted access to these organizations as well.  

The user is set using the following set of variables: `apimanager.auth.username` and `apimanager.auth.password`

### Traceability Agent users

Two users are needed for Traceability Agent: one with the "API Gateway Operator" role to read the data (headers of request/response) from the API Gateway Manager, and the second user to read API Manager APIs and know which API are published. This user is the same as the one needed for Discovery Agent.  

The users are set using variables: `apimanager.auth.username` / `apimanager.auth.password` and `apigateway.auth.username` / `apigateway.auth.password`

{{< alert title="Note" color="primary" >}}The API Gateway Manager configuration is only required to collect headers of request/response. It is possible to disable this collecting using the following property:  `output.traceability.agent.apigateway.getHeaders: false.` By default, the property is set to true and the API Gateway Manager configuration is required.{{< /alert >}}

{{< alert title="Password storage" color="warning" >}}All password are stored in clear in the agent configuration files.{{< /alert >}}

## Data exchanged

### Discovery Agent

Use variable `apimanager.filter` to select which API should be sent to Axway AMPLIFY platform. Only the matching APIs are transferred to Axway AMPLIFY platform. See [Filtering APIs to be discovered](docs/central/connect-api-manager/filtering-apis-to-be-discovered/). The Discovery Agent sends the following information to the Axway AMPLIFY platform:

* API definition using Swagger or WSDL depending on the API type (REST vs SOAP)
* API documentation

### Traceability Agent

Only traffic related to discovered APIs is sent to the platform.

The agent reads the logs written on the file system (\[INSTALL_DIR]/apigateway/events/group-X_instance-Y.log) by the Gateway(s) to get transaction summary:

* transaction HTTP status
* transction URLs (frontend / backend API)
* transaction duration and timestamp
* transaction service called: method (POST / GET...) + uri path

In order to submit details of the transaction, the Traceability Agent reads the Gateway system to get transaction details:

* request/response headers from each API call  

{{< alert title="Note" color="primary" >}}It is possible to disable the headers sending using the following property:  `output.traceability.agent.apigateway.getHeaders: false.` By default, the property is set to true. If the headers collecting is disabled, they will not be visible in Axway AMPLIFY platform observability module, as the Traceability Agent will send only the transaction summary data (status / url / duration / timestamp / transaction service called) to the platform.{{< /alert >}}

Once the information is extracted it is sent to the Axway platform using the TLS encryption.