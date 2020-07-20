---
title: Subscription for the consumer
linkTitle: Subscription for the consumer
draft: false
weight: 55
description: A subscription provides the consumer, or subscriber, with the
  required security, quota and endpoint materials to correctly consume the API.
---
## Subscription workflow

1. An administrator creates a usage plan on AWS API Gateway that provides the necessary security feature (API key / authorizer) and quota, if needed.
2. A consumer initiates the subscription in AMPLIFY Central:

    1. Open an AMPLIFY Catalog item.
    2. Click **Subscribe**.
    3. Enter the Team and the usage plan that the Administrator created. **Warning**: The names must match. Otherwise, the subscription will fail.

    For additional information, see [Manage AMPLIFY Catalog subscriptions](/docs/catalog/manage_subscriptions/index.html).

3. The Discovery Agent receives the subscription event:

    Subscription status: **Subscribing...**

    1. Associate the API to the selected application.
    2. Add a tag `Subscriptions-<subscriptionID from AMPLIFY Central>` with the value `<apiId>-<Apistage>` on the usage plan.
    3. Send back the subscription status.

    Subscription status: **Active**

    * If failure, subscription status: **Subscription failed**. Refer to the Discovery Agent log for more information. You can delete the subscription and start again from Step 2.
4. The subscriber consumes the API:

    * The API can be consumed once the subscription details are received.

## Unsubscribe workflow

1. A consumer initiates unsubscribe:

    1. Open the AMPLIFY Catalog and navigate to the **Subscription** tab.
    2. Delete the subscription.

    For additional information, see [Manage AMPLIFY Catalog subscriptions](/docs/catalog/manage_subscriptions/index.html).
  
2. The Discovery Agent receives the Unsubscribe event:

    * The `subscriptions-<subscriptionID from AMPLIFY Central>` is removed from the usage plan.

    * The API is removed from the usage plan.
