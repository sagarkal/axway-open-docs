---
title: AMPLIFY Central concepts
linkTitle: Concepts
weight: 20
date: 2019-07-30T00:00:00.000Z
description: Learn the fundamental concepts you will encounter in AMPLIFY Central.
---

## API and API proxy

Watch this short video to learn more about API proxies.

{{< youtube ja9HPLlzcn4 >}}

### What is an API?

An API is an interface to a service, that enables app developers to interact with it or _consume_ it. For example, you might have a weather service that app developers can use to show today's weather forecast to the users of their cycling app.

An API is clearly defined by way of its endpoints, request parameters, and responses. This makes it easy for app developers to use the API, as the API specification (for example, Open API or Swagger) tells them:

* What requests they can send and to what address
* What the supported request parameters are
* What responses they can expect

An API implies a _contract_, which provides app developers with an assurance that the API will change in a predictable manner over time, meaning that their app will continue to work with future changes to the API. AMPLIFY Central supports Swagger 2.0 and Open API 3.0 specifications.

### What is an API proxy?

In AMPLIFY Central, you create an API proxy to represent your back-end API to your API consumers. Instead of interacting directly with your API, app developers now interact with the API proxy.

![Interactions between app developers - API proxy - back-end API](/Images/central/api_proxy.png)

Managing your API in AMPLIFY Central by way of an API proxy offers the following benefits:

* You can change the implementation of the back-end service without impacting app developers as they continue to call the API proxy.
* You can apply policies to the API proxy to manage or secure how client apps use your API.
* You can analyze the usage of your APIs by client apps, and identify and analyze failed transactions, as all traffic that flows through an API proxy is monitored and recorded.

## Roles and teams

AMPLIFY Central has its own set of roles in addition to the AMPLIFY Platform roles. These AMPLIFY Central roles include AMPLIFY Central Admin, Developer, and Consumer type roles to manage and use assets (for example, API proxies, applications, environments, Unified Catalog, and so on).  

* AMPLIFY Central Admin has full access to manage AMPLIFY Central and the Unified Catalog.
* AMPLIFY Central Developer has access to the Unified Catalog, Application management, and traffic monitoring.
* AMPLIFY Central Consumer has access to the Unified Catalog and its Applications.

An AMPLIFY Central team is a group of users with varying abilities to manage and use assets. For example, a team member can share assets and their promotion to the Unified Catalog.

Users and teams of AMPLIFY Central are managed by the AMPLIFY Platform. For more information, see [Managing Organizations](https://docs.axway.com/bundle/AMPLIFY_Dashboard_allOS_en/page/managing_organizations.html).

<!-- ### AMPLIFY Central roles -->

<!-- The roles available in AMPLIFY Central and the capabilites of each role are: -->

<!-- TODO Add list of roles and what they can do -->

<!-- TODO Add something explaining a user can have a different role on each of the teams they are a member of. -->
