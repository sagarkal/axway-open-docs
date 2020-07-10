---
title: Prepare AWS API Gateway
linkTitle: Prepare AWS API Gateway
draft: false
weight: 20
description: Learn how to use the provided CoudFormation templates to initialize
  AWS Services, which enables the agents to collect API / traffic data.
---

## Before you start

* Read [AMPLIFY Central AWS API Gateway connected overview](/docs/central/connect-aws-gateway/)
* You will need a basic knowledge of Amazon Web Services (AWS) and associated tools

## Objectives

Learn how to Configure CloudFormation to initialize the required AWS Services.

## Download the CloudFormation templates

   ```
   curl -L "https://axway.bintray.com/generic-repo/aws-agents/aws_apigw_agent_config/latest/aws_apigw_agent_config-latest.zip" -o aws_apigw_agent_config-latest.zip
   ```

This zip contains three files:

* `apigw_iam_setup.yaml`: CloudFormation template that will help you to create the required roles and secret key to access the AWS Services,
* `apigw_cloudformation.yaml`: CloudFormation template that will help you to configure the AWS Services required for the agents (CloudWatch / lambdas / SimpleQueueService),
* `traceability_lambda.zip`: a lambda used to parse the log.

{{< alert title="Note" color="primary" >}}You can either accept or update most of the template's required parameters.{{< /alert >}}

## Upload the Traceability Lambda to an S3 bucket

Upload the `Traceability_Lambda.zip` file in an AWS S3 bucket located on the same region the Cloud Formation template will be deployed.

## Configure the required AWS roles

Create a new stack with the provided template `apigw_iam_setup.yaml` using the "**With new resources (standard)**" option and configure the parameters:

* Set the **ConfigBucketExists** value to **true** to use an existing S3 bucket to store the AWS logs. Be sure the S3 bucket is accessible and is located in the same region where you run the Cloud Formation template. If set to **false**, the S3 bucket will be created for you using the ConfigBucketName value below.
* Accept the **ConfigBucketName** default value of  `apigw-config-bucket` to store AWS Config logs. The accountID and AWS region is appended to the value. Or use an existing S3 bucket name if previous option **ConfigBucketExists** has been set to true.
* Accept the **DiscoveryQueueName** default value of `aws-apigw-discovery` to hold changes made to the API Gateway resources. The AWS region is appended to the value.
* Enable **SetupAPIGWCWRole** to create an IAM role for writing logs in CloudWatch.
* Enable **SetupConfigService** to create an IAM role for using Config Service.
* Accept the **TraceabilityQueueName** default value of `aws-apigw-traceability` to hold the API call so that the Traceability Agent can push them into the AMPLIFY platform.
  
Execute the stack creation.

The output of the stack contains all required information for the next cloud formation template (**ConfigServiceRoleArn / APICAgentAccessKey / APICAgentSecretKey / TraceabilityAPIGWCWRoleArn / TraceabilityLambdaRoleArn**).

## Configure the required AWS Services

Create a new stack with the provided template `apigw_cloudformation.yaml` using the "**With new resources (standard)**" option and configure the parameters:

* Set the **ConfigBucketExists** value to **true** to use an existing S3 bucket to store the AWS logs. Be sure the S3 bucket is accessible and is located in the same region where you run the Cloud Formation template. If set to **false**, the S3 bucket will be created for you using the ConfigBucketName value below.
* Accept the **ConfigBucketName** default value of `apigw-config-bucket` to store AWS Config logs. You can reuse the same bucket name as the one used in `apigw_iam_setup`. The accountID and AWS region is appended to the value.
* Enter the **ConfigServiceRoleArn** value coming from the output of `apigw_iam_setup`.
* Accept the **DiscoveryQueueName** default value of `aws-apigw-discovery` to hold the changes made to the API Gateway resources. The AWS region is appended to the value.
* Enable **SetupConfigService** to configure the appropriate AWS Config Service and its dependencies.
* Enter the **TraceabilityAPIGWCWRoleArn** value from the output of `apigw_iam_setup`.
* Enter the **TraceabilityFunctionBucketname** of the S3 bucket that was created in "Upload the Traceability Lambda to an S3 bucket" section.
* Enter the **TraceabilityFunctionKey** key value of the `subscribe_lambda function` in the bucket (open the Lambda in your bucket and identify the key value).
* Accept the **TraceabilityQueueName** default value of `aws-apigw-traceability` as the name of the queue that will hold the API traffic logs. The AWS region is appended to the value.

Execute the stack creation.

The output of the stack contains information that will help you to configure the agents (**DiscoveryQueueName / TraceabilityLogGroupArn / TraceabilityQueueName**).

Now you are ready to [deploy your agents](/docs/central/connect-aws-gateway/deploy-your-agents-1)
