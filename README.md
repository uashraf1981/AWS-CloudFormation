# AWS-CloudFormation
AWS CloudFormation = Infrastructure as Code (IaC)
AWS CloudFormation simplifies infrastructure provisioning and management on the cloud.

__CloudFormation Template:__ Allows you to specify the required resources and their dependencies as a stack.

__Step 1: Pick a Template__
You can use pre-built templates.

Sample Template: In the sample template that we are going to use, it contains:
 - Wordpress Blog with MySQL within the same EC2 instance
 - EC2 Security Group that contains firewall settings 
 
 Template is ALWAYS either in:
 
  - JSON
  - YAML
 
 A CloudFormation template contains the following sections: __*AWSTemplateFormatVersion, Description, Parameters, Mappings, Resources, and Outputs*__
 
 The most important one is Resources which basically specifies the resources that you want to provision i.e. allows back-reference resources. Sample JSON:
 

The CloudFormation template has four main areas:

__1. Description:__ A description of the template usage
__2. Parameters:__ A set of inputs to customize the template in each deployment
__3. Resources:__ The set of AWS resources that need to be deployed and the relationships between them
__4 Outputs:__ Set of values that need to be made visible to the stack creator

You can also use other tools to generate the JSON template for AWS CloudFormation e.g. __trophosphere__ is a Python based tool to generate the JSON file.

*__Important__* References are used to prevent name collisions e.g. if you use the same template in the same account multiple times, then you may end up re-using the same resource names, so a Reference function allows for dynamic naming at execution time by CloudFormation.

While it will obviously depend on the infrastructure being designed, but generally the following elements are required at the least:

- VPC
- IGW
- EC2
- Subnets
- SGs
- NAT
- Routing Table/Routes -> By giving a default outbound route to the IGW, we make a subnet as public
