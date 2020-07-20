---
title: Gateway administration
linkTitle: Gateway administration
draft: false
weight: 130
description: Learn how to deploy your Discovery Agent and Traceability Agent so that you can manage your Axway API Gateway environment within AMPLIFY Central.
---

## Before you start

* Read [AMPLIFY Central and Axway API Manager connected overview](/docs/central/connect-api-manager/)
* Be sure you have [Prepared AMPLIFY Central](/docs/central/connect-api-manager/prepare-amplify-central/)
* You will need a basic knowledge of Axway API Management solution
    * Where the solution is running (host / port / path to the logs / users)
    * How to create / publish an API
    * How to call an API

## Objectives

Learn how to install, customize and run your Discovery and Traceability agents.

## Discovery Agent

The Discovery Agent is used to discover new published APIs or any updated APIs. Once they are discovered, the related APIs are published to AMPLIFY Central, in one of the following publication modes, so that they become available for any consumer:

* **Catalog item publication** (disconnected mode): Customers expose their APIs globally for their consumers but keep the API management at the Gateway level.
* **Environment / API Service publication** (connected mode): Customers manage their APIs from the AMPLIFY platform.
The Discovery Agent only discovers APIs that have the tag(s) defined in the agent configuration file. See [Filtering APIs to be discovered](/docs/central/connect-api-manager/filtering-apis-to-be-discovered/). By default, the filter is empty and thus the agent will discover all published APIs.

As soon as an API is published, the identifier of the asset in AMPLIFY Central is kept in a custom field at the API level in API Manager to help the agent remember what is already published.

The agent can run in the following modes:

* With a yaml configuration file having the same name as the agent binary - discovery_agent.yaml:
    * **Default**: located in the same directory as the agent binary.
    * **Optional**: use a dedicated folder where the configuration file is located (use the –pathConfig flag in the agent command line to access the file path).
    * **Advanced configuration**: properties inside the configuration file can reference environment variables. This enables you to set up only one configuration file that addresses different behaviors (depending on the environment variables). See [Discovery Agent variables](/docs/central/connect-api-manager/discovery-agent-variables/).

* With command line arguments. See [Discovery Agent flags](/docs/central/connect-api-manager/discovery-agent-flags/).

### Installing the Discovery Agent

To install the Discovery Agent:

1. Download the latest version of the zip file from the Axway public repository using the following command:

   ```shell
   curl -L "https://axway.bintray.com/generic-repo/v7-agents/v7_discovery_agent/latest/discovery_agent-latest.zip" -o discovery_agent-latest.zip
   ```

2. Unzip the file `discovery_agent-latest.zip` to get the agent binary (`discovery_agent`) and the template configuration (`discovery_agent.yml`).

3. Copy both files (`discovery_agent`, `discovery_agent.yml`) into a folder (i.e., `/home/APIC-agents`) on the machine where the API Manager environment is located.

4. Customize apimanager section by setting configuration values to point to the API Gateway, API Manager, and AMPLIFY Central.  There are 2 options to set values:

   * `env_vars` file
   * `discovery_agent.yml`:

   ```shell
   apimanager:
    host: localhost
    port: 8075
    discoveryIgnoreTags: tag1, tag2
    filter:
    proxyApicIDField: apicId
    subscriptionApplicationField: subscriptions
    pollInterval: 30s
    SSL:
     INSECURESKIPVERIFY: true
    auth:
     username: apiManagerUser
     password: apiManagerUserPassword

   central:
    url: https://apicentral.axway.com
    platformURL: https://platform.axway.com
    teamID: 6dff3d5c-8f7e-4f77-9061-e7219ee0b063
    tenantID: 655431797898152
    environment: my-v7-env
    apiServerVersion: v1alpha1
    mode: publishToEnvironmentAndCatalog
    pollInterval: 10s
    auth:
     url: https://login-preprod.axway.com/auth
     realm: Broker
     clientId: DOSA_66743...
     privateKey: /home/APIC-agents/private_key.pem
     publicKey: /home/APIC-agents/public_key.der
     keyPassword:
     timeout: 10s

   log:
    level: debug
    format: json
    output: stdout
    path: logs
    ```

   * The value for *teamID* can be found in [AMPLIFY Central > Access > Teams](https://apicentral.axway.com/access/teams/).

   * The value for *tenantID* can be found in AMPLIFY Central Platform > Organization.

   * The value for *clientId* can be found in Service Account. See [Create a service account](/docs/central/cli_central/cli_install/#create-a-service-account).

5. Run the Discovery Agent:
   * Open a shell and run the following commands to start up your agent.  Add necessary [Discovery Agent flags](/doc/central/connect-api-manager/discovery-agent-flags/).

     ```shell
     cd /home/APIC-agents
     ./discovery_agent
     ```

   * To verify that the agent is up and running, open another shell command and run:
      ```shell
      cd /home/APIC-agents
      ./discovery_agent --status
      ```

## Traceability Agent

The traceability agent is used to filter the Axway API Gateway logs that are related to discovered APIs and prepare the transaction events that are sent to AMPLIFY platform. Each time an already discovered API is called by a consumer, an event (summary + detail) is sent to AMPLIFY Central and is visible in API Observer.

The agent can run in the following modes:

* With a yaml configuration file having the same name as the agent binary - `traceability_agent.yaml`:

    * Default: located in the same directory as the agent binary.
    * Optional: use a dedicated folder where the configuration file is located (use the --path.config flag in the agent command line to access the file path).
    * Advanced configuration: properties inside the configuration file can reference environment variables. This enables you to set up only one configuration file that addresses different behaviors (depending on the environment variables). See [Discovery Agent variables](/docs/central/connect-api-manager/discovery-agent-variables/).
* With command line argument. See [Traceability Agent flags](/docs/central/connect-api-manager/traceability-agent-flags/).

### Installing the Traceability Agent

To install the Traceability Agent:

1. Download the Traceability Agent using the following command:

   ```shell
   curl -L "https://axway.bintray.com/generic-repo/v7-agents/v7_traceability_agent/latest/traceability_agent-latest.zip" -o traceability_agent-latest.zip
   ```

2. Unzip the file `traceability_agent-latest.zip` to get the agent binary (`traceability_agent`) and the template configuration (`traceability_agent.yml`).

3. Copy both files (`traceability_agent`, `traceability_agent.yml`) into a folder (i.e., `/home/APIC-agents`) on the machine where the API Manager environment is located.

4. Customize traceability_agent section by setting configuration values to point to the event logs path,  API Gateway, API Manager, and AMPLIFY Central.  There are 2 options to set values:

   * `env_vars` file
   * `discovery_agent.yml`:

   ```shell
   ################### Beat Configuration #########################
   traceability_agent:
    inputs:
     - type: log
       paths:
         - <PATH_TO>/group-X_instance-Y.log
      include_lines: ['.*"type":"transaction".*"type":"http".*']

   # Send output to Central Database
   output.traceability:
    enabled: true
    hosts: ${LOGSTASH_URL:ingestion-lumberjack.datasearch.axway.com:453}
    ssl:
     enabled: true
     verification_mode: none
     cipher_suites:
      - "ECDHE-ECDSA-AES-128-GCM-SHA256"
      - "ECDHE-ECDSA-AES-256-GCM-SHA384"
      - "ECDHE-ECDSA-AES-128-CBC-SHA256"
      - "ECDHE-ECDSA-CHACHA20-POLY1305"
      - "ECDHE-RSA-AES-128-CBC-SHA256"
      - "ECDHE-RSA-AES-128-GCM-SHA256"
      - "ECDHE-RSA-AES-256-GCM-SHA384"
     proxy_url: ${LOGSTASH_PROXYURL:""}
     agent:
      central:
       url: https://apicentral.axway.com
       tenantID: 68794y2
       deployment: prod
       environment: my-v7-env
       auth:
        url: https://login.axway.com/auth
        realm: Broker
        clientId: "DOSA_68732642t64545..."
        privateKey: /home/APIC-agents/private_key.pem}
        publicKey: /home/APIC-agents/public_key}
        keyPassword: ""
        timeout: 10s
     apigateway:
      getHeaders: true
      host: localhost
      port: 8090
      pollInterval: 1m
      auth:
        username: myApiGatewayOperatorUser
        password: myApiGatewayOperatorUserPassword
     apimanager:
      host: localhost
      port: 8075
      pollInterval: 1m
      apiVersion: 1.3
      proxyApicIDField: apicId
      auth:
        username: myApiManagerUserName
        password: myApiManagerUserPassword

   logging:
    metrics:
     enabled: false
    # Send all logging output to stderr
    to_stderr: true
    # Set log level
    level: ${LOG_LEVEL:info}
    ```

   * The value for *tenantID* can be found in AMPLIFY Central Platform > Organization.

   * The value for *clientId* can be found in Service Account. See [Create a Service in AMPLIFY Central](/docs/central/connect-api-manager/prepare-amplify-central/).

   * Traceability Agent variables can be found at [Traceability Agent variables](/docs/central/connect-api-manager/traceability-agent-variables/)

5. Once the YAML file is updated, start the Traceability Agent. If the YAML file is in the same folder, run `./traceability_agent` script. Otherwise, pass the command-line flags that are documented at [Traceability Agent flags](/docs/central/connect-api-manager/traceability-agent-flags/).

6. The Traceability Agent parses through the files based on the `event-file` path and pattern provided. Depending on the data found, the agent pushes it to AMPLIFY Central.

7. Go to AMPLIFY Central and open the API Observer tab to verify that the agent is working. You should see the monitoring data for the APIs discovered earlier. If you don’t see any data, then invoke a few different API methods in the exposed API.

8. Click on any of the transactions to see the details. You will see the lifecycle of an API call, such as time taken / request and response headers / etc.

## Configuring API Manager

### Tags

1. Configure filters in yaml file to determine which [APIs will be discovered](/docs/central/connect-api-manager/filtering-apis-to-be-discovered/) and published to AMPLIFY Central.

2. Discovery API tags.  Set filtering based on tag name, tag value, partial value and MatchRegEx to discover APIs that can be published to AMPLIFY Central:
   * In API Manager, select front-end API to edit.
   * The tags should match the values in your apimanager configuration file.
   * Save the API and publish.  Once published, the Discovery Agent attempts to match the polling criteria. If it matches, it publishes it to the AMPLIFY Central Catalog and Environments.
   * Once the API is published to the catalog, a reference value is generated by AMPLIFY Central and sent back to the API Manager (`APIC_ID`).  If you want to see that field or customize it, refer to **Add a custom property to APIs** in [Customize API Manager](/docs/apim_administration/apimgr_admin/api_mgmt_custom/#customize-api-manager-data) documentation.

### Custom fields

Two custom fields are used to keep Central information on the API Manager:

* asset identifier: custom field at the API level. The default value is `apicId` and is not visible in API Manager UI as it is a specific configuration. This field helps the discovery agent to know which API is already discovered and which is not. This field store the AMPLIFY Central identifier for the front-end proxy in API Manager. Removing the field value will force the discovery agent to re-discover the API.
* subscription identifier: custom field at the application level. The default value is `subscriptions` and is not visible in API Manager UI as it is a specific configuration. This fields helps the discovery agent to know if some AMPLIFY Central subscription are used for a particular API/application combination. Each time an AMPLIFY Central subscription succeed, the subscription identifier is kept in the field value of the corresponding API Manager application used to initiate the subscription. On the opposite, when the subscription is removed from AMPLIFY Central, the corresponding subscription identifier is removed in the corresponding API Manager application.

In order to visualize those fields in API Manager UI, you need to update the file `app.config` located in `<INSTALL-DIR>/apigateway/webapps/apiportal/vordel/apiportal/app` with the following:

```
    customPropertiesConfig: {
        user: {
            // custom properties...
        },
        organization: {
            // custom properties...
        },
        application: {
            // custom properties...
             subscriptions: {
                     label: 'Amplify Central subscription ID',
                     disabled:true
             }
        },
        api: {
            // custom properties...
              apicId: {
                     label: 'AMPLIFY Central identifier'
            }

        }
    },

```

You can also change the properties values (`apicId`, `subscriptions`) to suit your custom fields pattern. If you do so, be sure you update the agent configuration files accordingly (`proxyApicIDField` and `subscriptionApplicationField`). `apicId` should be editable as it could help to re-discover an API in case of issue by simply removing its value.

### Subscriptions - Manage client applications on APIManager for subscription process

You can use the **Clients** tab to manage client applications (for example, create, update, or remove client applications that invoke specific APIs). When an application is created, API administrators can also set authentication, quota, and sharing settings on the appropriate tab.

The API administrator must first specify the APIs that an organization is allowed to access before any of its client applications can have access to them. In API Manager, you can only add APIs to an application when they have been added to the organization. For more details, see [Manage access to APIs](/docs/apim_administration/apimgr_admin/api_mgmt_admin/).

#### Create application

To create an application:

1. Click **New application** in the toolbar and configure the following general fields:

   * **Image**: Click to add a graphical image for the application (for example, .png, .gif, or .jpeg file).
   * **Application name**: Enter the name of the application. This field is required.
   * **Organization**: Enter the name of the organization that the application belongs to. This field is required. The choice of organization determines which APIs are available to the application.
   * **Enabled**: Select whether the application is enabled. Applications are enabled by default.

2. Configure the following additional attributes:

   * **Email**: Enter an email address for the application.
   * **Phone**: Enter a phone number for the application.
   * **Description**: Enter a short description of the application.

3. Click **Add API** to select the APIs and methods used by the application. You can add multiple APIs for an application.

4. Click **Create** in the toolbar.

#### Edit application

Once applications have been created, you can click an application name in the *Managing applications* screen to edit its existing settings on the **Application** tab.

1. Add API to be recognized as an application to be used for subscription.
2. Make sure **Enabled** is active.

### Manage subscription in AMPLIFY Central

After configuring the application in API Manager, set up catalog subscriptions in AMPLIFY Central.

A consumer initiates the subscription in AMPLIFY Central:

1. Open an AMPLIFY Catalog Item.
2. Click **Subscribe**.
3. Enter the Team
4. Select an API Manager Application name (The displayed list should be the on set up on API Manager associated to the current API the subscriber is subscribing to).
5. Click **Subscribe**.

The Discovery Agent listens to the subscription event.

### Subscribing workflow

When a consumer wants to consume an API in AMPLIFY Central, he needs to subscribe to the API. For now, only an auto-approved workflow is available:

* The discovery agent listens to the subscription event,
* Check that the selected API and application are existing/published on API Manager,
* Search for the corresponding API credentials (API key or OAuth token) and send the credentials to the subscriber email,
* Send back to AMPLIFY Central the subscription status:
    * If active, the subscription ID is automatically added to the custom field of the application (`subscriptions`).
    * If failure, the subscription status **Subscription failed** will appear. You can delete the subscription and start again from the **Save the API and publish** step.
* In case of success, the subscriber consumes the API otherwise API cannot be consumed.

In case the application is not available because it is not in the same organization than the API, you can grant specific access to the API:

   1. In the UI, select the API.
   2. Expand **Manage selected**.
   3. Select **Grant access**.

### Unsubscription workflow

In API Manager, assume there is a FrontEnd API that is published, has been discovered by the Discovery Agent, and has an active subscription to it in AMPLIFY Central. To initiate an unsubscribe to AMPLIFY Central for that Catalog item:

* Unpublish that API in API Manager.
* The Discovery Agent discovers the change.
* The subscription status is set to **Unsubscribed**.
* The subscription ID is removed from the application’s **Custom** field.
* The subscription status is set to **Unsubscribed**.

### Managing Subscription notifications

For information, see [Deploy your agents](/docs/central/connect-api-manager/deploy-your-agents/)

```shell
subscriptions:
  smtp:
    host: mail.axway.int
    port: 25
    fromAddress: fromaddress@axway.com
    username: fromaddress@axway.com
    password:
    subscribe:
      subject: Subscription Notification
      body: |
        Subscription created for Catalog Item:  <a href= ${catalogItemUrl}> ${catalogItemName} </a> <br/>
        Subscription key: <b>${key}</b>
    unsubscribe:
      subject: Subscription Removal Notification
      body: |
        Subscription for Catalog Item: <a href= ${catalogItemUrl}> ${catalogItemName} </a> has been unsubscribed
    subscribeFailed:
      subject: Subscription Failed Notification
      body: |
        Could not subscribe to Catalog Item: <a href= ${catalogItemUrl}> ${catalogItemName} </a>
    unsubscribeFailed:
      subject: Subscription Removal Failed Notification
      body: |
        Could not unsubscribe to Catalog Item: <a href= ${catalogItemUrl}> ${catalogItemName} </a>
 ```
