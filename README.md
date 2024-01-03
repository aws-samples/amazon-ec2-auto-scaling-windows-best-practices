# Accelerate Amazon EC2 Auto Scaling for Microsoft Windows workloads

This repo hosts templates written for the AWS Blog Post "[Accelerate Amazon EC2 Auto Scaling for Microsoft Windows workloads](https://aws.amazon.com/blogs/modernizing-with-aws/how-to-create-an-amazon-ec2-ami-usage-and-billing-information-report/)" published on the [Microsoft Workloads on AWS](https://aws.amazon.com/blogs/modernizing-with-aws/) blog channel.

## Overview
This is a sample solution using [AWS Systems Manager Automation](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-automation.html) to execute Python code that extracts metadata from the Amazon EC2 API about running Amazon EC2 instances in a single account or across all regions and accounts in your organization using [ Multi-account Multi-region AWS Systems Manager automations](https://docs.aws.amazon.com/systems-manager/latest/userguide/running-automations-multiple-accounts-regions.html) and then stores the output in a CSV file in an [Amazon Simple Storage Service (Amazon S3)](https://aws.amazon.com/s3/) bucket.


## Deployment
### CloudFormation
### Prerequisites
For **AWS Organizations multi-account, multi-region deployment**, Designate a central account and region to run your Automation documents from. This could be a shared services account. To learn why this is needed, please refer to [Running automations in multiple AWS Regions and accounts](https://docs.aws.amazon.com/systems-manager/latest/userguide/running-automations-multiple-accounts-regions.html).

### Walkthrough

Start with downloading the [EC2BillingReport.yaml](https://github.com/aws-samples/amazon-ec2-ami-billing-report/blob/main/Templates/CloudFormation/EC2BillingReport.yaml) CloudFormation template, [create a stack on the AWS CloudFormation console](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-create-stack.html). The CloudFormation template will create the following resources:

#### For **Single account deployment**.
1. Create an [Amazon S3 bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/create-bucket-overview.html).
2. Create an [AWS Systems Manager automation document](https://docs.aws.amazon.com/systems-manager/latest/userguide/automation-document-builder.html). The automation document run the automation action [aws:executeScript](https://docs.aws.amazon.com/systems-manager/latest/userguide/automation-action-executeScript.html). The script will do the followings.
    - [DescribeInstances](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeInstances.html) In each region.
    - Create or update existing csv file with the billing information to S3 bucket created on step 1.
3. Create AWS Identity and Access Management (IAM) [policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html) and a [role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html) to to delegate permissions to the Systems Manager automation document.

For more details about launching a stack and stackset, refer to [Creating a Stack on the AWS CloudFormation Console](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-create-stack.html) and [Working with AWS CloudFormation StackSets](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacksets-getting-started-create.html).

#### For **AWS Organizations multi-account, multi-region deployment**.
1. (Central account) Create an [Amazon S3 bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/create-bucket-overview.html).
2. (Central account) Create an [Amazon S3 bucket policy](https://docs.aws.amazon.com/AmazonS3/latest/userguide/bucket-policies.html) to allow access for the member accounts of the organization.
3. (Central account) Create an [AWS Systems Manager automation document](https://docs.aws.amazon.com/systems-manager/latest/userguide/automation-document-builder.html). The automation document runs the automation action [aws:executeScript](https://docs.aws.amazon.com/systems-manager/latest/userguide/automation-action-executeScript.html). The script will do the followings.
    - [DescribeInstances](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeInstances.html) In each region.
    - Create or update existing csv file with the billing information to S3 bucket created on step 1.
4. Create AWS Identity and Access Management (IAM) [policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html) and a [role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html) to
    - Create role needed [Running automations in multiple AWS Regions and accounts](https://docs.aws.amazon.com/systems-manager/latest/userguide/running-automations-multiple-accounts-regions.html).
    - (Central account) Create a role for AWS Lambda function.
6. (Central account) Create a [AWS Lambda function](https://docs.aws.amazon.com/lambda/latest/dg/getting-started.html) to
5. (Central account, Optional) Create a [Amazon EventBridge rule](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-create-rule-schedule.html) that runs on a schedule to invoke AWS Lambda function.

### Template Parameters
The stack template includes the following parameters:

#### For **Single account deployment**.
| Parameter | Required | Description |
| --- | --- | --- |
| Local/current account | Yes | Generate the report for the current and single account only. Creating the resources in the current account only. If **true** selected, leave all other parameters at their default values. |

#### For Central account with **AWS Organizations multi-account, multi-region deployment**.
| Parameter | Required | Description |
| --- | --- | --- |
| Central AWS Account | Yes | Creating the resources in the central account. Used to generate the report for the multiple accounts at once. If **true** selected, complete the parameters in this section. |
| Central AWS Account ID | Yes | AWS Account ID of the central account (the account from which AWS Systems Manager Automation will be started). |
| Organization ID | Yes | AWS Organizations ID. This is used only when you plan to target all accounts within the specified AWS Organization. |
| Target Accounts and OUs for Systems Manager multi-account multi-region automation | Yes | Comma separated list of AWS Account ids and organizational units (OUs) for the target account(s). For example: ou-srdk-01234567,012345678901,ou-srdk-01234567. |
| RunOnSchedule | Yes | Select **true** if you want the automation to run (generate the report) on schedule. |
| EventBridgeRuleSchedule | No | If **RunOnSchedule** set to **true**. The [cron or rate expression](https://docs.aws.amazon.com/eventbridge/latest/userguide/scheduled-events.html) to use for the EventBridge rule. For example: cron(0 12 ?SAT *) or rate(7 days). Important: The time zone used is UTC.

#### For target account(s) with **AWS Organizations multi-account, multi-region deployment**.
| Parameter | Required | Description |
| --- | --- | --- |
| Target AWS Account | Yes | Creating the resources in the target account(s). Used to generate the report for the multiple accounts at once. If **true** selected, complete the parameters in this section. |
| Central AWS Account ID | Yes | AWS Account ID of the central account (the account from which AWS Systems Manager Automation will be started). |
| Organization ID | Yes | AWS Organizations ID. This is used only when you plan to target all accounts within the specified AWS Organization. |
| Central S3 Bucket | Yes | The S3 bucket generated after running this CloudFormation template on the central account. |
| AWS Systems Manager Automation Administration Role Name | Yes | The AWS Systems Manager Automation Administration Role Name generated after running this CloudFormation template on the central account. |
| AWS Systems Manager Automation Execution Role Name | Yes | The AWS Systems Manager Automation Execution Role Name generated after running this CloudFormation template on the central account.

## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the MIT-0 License. See the LICENSE file.

