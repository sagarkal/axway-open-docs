{
    "title": "Set up API Manager",
    "linkTitle": "Set up API Manager",
    "weight": "10",
    "date": "2019-09-17",
    "description": "Learn how to enable API Manager after installation, and how to configure it with signed certificates, monitoring,  and HTTP proxy servers."
}

## Prerequisites

* Ensure that both API Manager and API Gateway and are installed. API Manager is a layered product running on API Gateway, which provides the underlying gateway capabilities. API Gateway is a prerequisite product for API Manager.
* Ensure that an API Gateway Admin Node Manager and an API Gateway instance have been created and started.
* Ensure that Apache Cassandra is installed and running, and that the Cassandra hosts have been configured in Policy Studio.

## Enable API Manager

{{< alert title="Note" color="primary" >}}If you installed API Manager using the **Complete** setup type, or installed API Manager and the Quick Start tutorial as part of a **Custom** setup type, API Manager is enabled by default, and you can skip to the next section. {{< /alert >}}

To enable API Manager, perform the following steps:

1. Open Policy Studio and open or create a new project.
2. Select **File > Configure API Manager**.
3. If you do not have any Cassandra hosts configured, you must add a Cassandra host before you can continue:

    * Enter a name for the Cassandra server (for example, `local_cassandra`).
    * Enter the Cassandra host name (for example, `localhost`).
    * Enter the Cassandra port (for example, `9042`).

4. On API Manager settings screen, enter the required values to set up API Manager.

    {{< alert title="Note" color="primary" >}}The default API administrator user name and password set in Policy Studio are used only when creating the administrator account in Cassandra. After the account has been created in Cassandra, you cannot change the credentials in Policy Studio. You must use API Manager to change the administrator credentials. {{< /alert >}}

5. Configure additional API Manager settings under **Server Settings > API Manager**. For example, you can specify custom policies that are called as traffic passes through API Manager. For more details, see [Configure API Manager settings in Policy Studio](/docs/apim_administration/apimgr_admin/api_mgmt_config_ps/).
6. Click the **Deploy** button in the toolbar to deploy the updated configuration to API Gateway.
7. After deployment completes, enter the following URL in your browser to log in to apimanager: `https://HOSTNAME:8075`
8. Log in using the API administrator credentials that you specified when installing API Manager, or when configuring API Manager in Policy Studio. For security reasons, you must change the default credentials.

{{< alert title="Note" color="primary" >}}In earlier versions, API Manager was configured using the `setup-apimanager` script in the `INSTALL_DIR/apigateway/posix/bin` directory. This script is still supported for backwards compatibility, but it is best to use Policy Studio to configure API Manager in this version. Cassandra must be installed and running before you run the `setup-apimanager` script. For more details, run the script with the `--help` option. {{< /alert >}}

## Configure signed certificates for API Manager ports

The default certificates used to secure the ports for API Manager and its runtime traffic are self-signed and must not be used in a production system. Instead, you must use a server certificate signed by a trusted Certificate Authority (for example, Verisign). The default certificates are signed by Axway and are for demonstration purposes only.

To configure signed server certificates for these API Manager ports, perform the following steps in Policy Studio:

1. Add the server certificates signed by a trusted Certificate Authority to the API Gateway certificate store, and ensure that their start and expiry dates are valid.
2. Configure the API Manager port to use the signed server certificate:

    1. Select **Environment Configuration > Listeners > API Gateway > API Portal > Ports**.
    2. Double-click **API Portal Port** on the right to open the **Configure HTTPS Interface** dialog. The default port is `8075`.
    3. On the **Network** tab, click **X.509 Certificate** to select the signed server certificate.

3. Configure the API Manager runtime traffic port to use the signed server certificate:

    1. Select **Environment Configuration > Listeners > API Gateway > API Manager Traffic > Ports**.
    2. Double-click **Portal Traffic HTTPS Interface** on the right to open the **Configure HTTPS Interface** dialog. The default port is `8065`.
    3. On the **Network** tab, click **X.509 Certificate** to select the signed server certificate.

## Configure an API Manager monitoring database

To monitor APIs in API Manager, you must perform the following steps:

1. Configure a JDBC-compliant database used to store historic traffic. The following databases are supported:

    * Oracle
    * MySQL
    * Microsoft SQL Server
    * IBM DB2

2. Configure the API Manager for monitoring. For example, you must ensure that real-time monitoring is enabled on the API Gateway, and that writing metrics data to the database is enabled.

For more details, see [Monitor APIs and applications in API Manager](/docs/apim_administration/apimgr_admin/api_mgmt_monitor/).

## Configure API Manager in network protected by an HTTP proxy

If you are using API Manager in a network protected by an HTTP proxy that requires authentication, perform the following steps:

### Configure a proxy server

For API Manager to connect to the back-end API through a proxy, configure the routing policy with a proxy server. For example, perform the following steps when using SSL-based routing:

1. In the Policy Studio tree, select **Policies > Generated Policies > REST APIs > Templates > Default SSL-based Routing**.
2. Double-click the **Connect to URL** filter to edit it, and select the **Settings** tab.
3. Select **Proxy > Send via proxy**.
4. In the **Proxy Server** field, browse to the configured proxy server, or right-click **Proxy Servers**, and select **Add a Proxy Server** to configure a new proxy server.
5. Click **Deploy** in the toolbar to deploy the updated configuration.

### Update the JVM settings

The following JVM setting is also required when importing the API in API Manager. This is because API Manager uses Java to download the API:

```
<ConfigurationFragment>
   <VMArg name="-Dhttp.proxyHost=IP_ADDRESS" />
   <VMArg name="-Dhttp.proxyPort=1234" />
   <VMArg name="-Dhttp.nonProxyHosts=localhost|127.0.0.1" />
   <VMArg name="-Dhttp.proxyUser=some_name" />
   <VMArg name="-Dhttp.proxyPassword=some_password" />
</ConfigurationFragment>
```

## Configure API Manager in a multi-datacenter environment

For details on configuring API Manager for a large amount of APIs and data in a multi-datacenter environment, see [Configure API Management in multiple datacenters](/docs/apimgmt_multi_dc/).
