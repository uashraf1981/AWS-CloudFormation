Creating a simple VPC Using CloudFormation

We can use JSON or YAML to instantiate the CloudFormation template.

Resources:
  myVPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: !Ref CidrBlock
      EnableDnsHostnames: true
      EnableDnsSupport: true
      InstanceTenancy: default
