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
  
  "WebServer": {\
  
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

As we can see, we can specify the Image ID (Amazon Machine Image - AMI), the instance ID and the Key Name in an easy manner here. Here, WebServer is the name of the logical Resource the name of the stack that we want to spin up. We add the __Parameters__ section to pass actual values to the template:

*"Parameters" : {

  ...
  
  "KeyName": {
  
    "Description" : "Name of an existing EC2 KeyPair to enable SSH access to the instances",
    
    "Type": "AWS::EC2::KeyPair::KeyName",
    
    "ConstraintDescription" : "must be the name of an existing EC2 KeyPair."
    
  },

  "InstanceType" : {
  
    "Description" : "WebServer EC2 instance type",
    
    "Type" : "String",
    
    "Default" : "t2.small",
    
    "AllowedValues" : [ "t1.micro", "t2.nano", "t2.micro", "t2.small", "t2.medium", "t2.large", "m1.small", "m1.medium", "m1.large", "m1.xlarge", "m2.xlarge", "m2.2xlarge", "m2.4xlarge", "m3.medium", "m3.large", "m3.xlarge", "m3.2xlarge", "m4.large", "m4.xlarge", "m4.2xlarge", "m4.4xlarge", "m4.10xlarge", "c1.medium", "c1.xlarge", "c3.large", "c3.xlarge", "c3.2xlarge", "c3.4xlarge", "c3.8xlarge", "c4.large", "c4.xlarge", "c4.2xlarge", "c4.4xlarge", "c4.8xlarge", "g2.2xlarge", "g2.8xlarge", "r3.large", "r3.xlarge", "r3.2xlarge", "r3.4xlarge", "r3.8xlarge", "i2.xlarge", "i2.2xlarge", "i2.4xlarge", "i2.8xlarge", "d2.xlarge", "d2.2xlarge", "d2.4xlarge", "d2.8xlarge", "hi1.4xlarge", "hs1.8xlarge", "cr1.8xlarge", "cc2.8xlarge", "cg1.4xlarge"],
    "ConstraintDescription" : "must be a valid EC2 instance type."
    
  },
  
...

*

The CloudFormation template has four main areas:

__1. Description:__ A description of the template usage
__2. Parameters:__ A set of inputs to customize the template in each deployment
__3. Resources:__ The set of AWS resources that need to be deployed and the relationships between them
__4 Outputs:__ Set of values that need to be made visible to the stack creator




