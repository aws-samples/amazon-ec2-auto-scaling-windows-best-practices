# Accelerate Amazon EC2 Auto Scaling for Microsoft Windows workloads

This repo hosts templates written for the AWS Blog Post "[Accelerate Amazon EC2 Auto Scaling for Microsoft Windows workloads](https://aws.amazon.com/blogs/modernizing-with-aws/how-to-create-an-amazon-ec2-ami-usage-and-billing-information-report/)" published on the [Microsoft Workloads on AWS](https://aws.amazon.com/blogs/modernizing-with-aws/) blog channel.

## Overview
This deploys infrastructure to run [Bob's Used Books](https://github.com/aws-samples/bobs-used-bookstore-sample), an e-commerce website built with .NET. This sample solution leverages Amazon EC2 Auto Scaling groups to launch additional instances seamlessly during traffic spikes. This maintains application performance even under heavy load.  This solution also uses EC2 fast launch technologies to streamline the instance boot process. Rather than taking 15+ minutes for a typical Windows Server OS launch with multiple reboots, fast launch skips unnecessary steps to boot freshly launched instances.

![Architectural Diagram](/ArchitecturalDiagram.png)


## Deployment
### Prerequisites
To deploy the application to AWS you need the following:

    An AWS account and a IAM User with an attached AdministratorAccess policy

### Getting Started
1. Download deployment template.  For this example we'll use the [WindowsAutoScaling.yml](/Templates/CloudFormation/WindowsAutoScaling.yml) CloudFormation template.
2. [Create a stack from the AWS CloudFormation Console](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-create-stack.html).  The CloudFormation template will create the resources shown in the architectural diagram above.

![Create Stack](/CreateStack.png)

### Template Parameters
The stack template includes the following parameters:

| Parameter | Required | Description |
| --- | --- | --- |
| Stack name | Yes | A stack is a collection of AWS resources that you can manage as a single unit. In other words, you can create, update, or delete a collection of resources by creating, updating, or deleting stacks. All the resources in a stack are defined by the stack's AWS CloudFormation template. |
| InstanceType | Yes | Amazon EC2 provides a wide selection of instance types optimized to fit different use cases. Instance types comprise varying combinations of CPU, memory, storage, and networking capacity and give you the flexibility to choose the appropriate mix of resources for your applications. Each instance type includes one or more instance sizes, allowing you to scale your resources to the requirements of your target workload. |
| KeyPair | Yes | A key pair, consisting of a public key and a private key, is a set of security credentials that you use to prove your identity when connecting to an Amazon EC2 instance. |
| MicrosoftADDomainName | Yes | Fully qualified domain name (FQDN) of the AWS Managed Microsoft AD domain e.g. corp.example.com |
| PrivateSubnets | Yes | A private subnet is a subnet that is associated with a route table that doesn't have a route to an internet gateway. You much provide at least two for the Auto Scaling group, Amazon Relational Database Service (RDS), Managed Active Directory (MAD) and other resources. |
| PublicSubnets | Yes | A public subnet is a subnet that is associated with a route table that has a route to an Internet gateway. This connects the VPC to the Internet and to other AWS services. You much provide at least two for the internet-facing load balancer. |
| VPC | Yes | A virtual private cloud (VPC) is a virtual network dedicated to your AWS account. It is logically isolated from other virtual networks in the AWS Cloud.


## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the MIT-0 License. See the LICENSE file.

