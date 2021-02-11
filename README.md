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
 
 The most important one is Resources which basically specifies the resources that you want to provision. Sample JSON:
 
 *
  "Resources" : {
  ...    
  "WebServer": {
    "Type" : "AWS::EC2::Instance",
    "Properties": {
      "ImageId" : { "Fn::FindInMap" : [ "AWSRegionArch2AMI", { "Ref" : "AWS::Region" },
                        { "Fn::FindInMap" : [ "AWSInstanceType2Arch", { "Ref" : "InstanceType" }, "Arch" ] } ] },
      "InstanceType"   : { "Ref" : "InstanceType" },
      "SecurityGroups" : [ {"Ref" : "WebServerSecurityGroup"} ],
      "KeyName"        : { "Ref" : "KeyName" },
      "UserData" : { "Fn::Base64" : { "Fn::Join" : ["", [
                     "#!/bin/bash -xe\n",
                     "yum update -y aws-cfn-bootstrap\n",

                     "/opt/aws/bin/cfn-init -v ",
                     "         --stack ", { "Ref" : "AWS::StackName" },
                     "         --resource WebServer ",
                     "         --configsets wordpress_install ",
                     "         --region ", { "Ref" : "AWS::Region" }, "\n",

                     "/opt/aws/bin/cfn-signal -e $? ",
                     "         --stack ", { "Ref" : "AWS::StackName" },
                     "         --resource WebServer ",
                     "         --region ", { "Ref" : "AWS::Region" }, "\n"
      ]]}}
    },
    ...
  },
  ...  
  "WebServerSecurityGroup" : {
    "Type" : "AWS::EC2::SecurityGroup",
    "Properties" : {
      "GroupDescription" : "Enable HTTP access via port 80 locked down to the load balancer + SSH access",
      "SecurityGroupIngress" : [
        {"IpProtocol" : "tcp", "FromPort" : "80", "ToPort" : "80", "CidrIp" : "0.0.0.0/0"},
        {"IpProtocol" : "tcp", "FromPort" : "22", "ToPort" : "22", "CidrIp" : { "Ref" : "SSHLocation"}}
      ]
    }
  },
  ...    
},
*
  

