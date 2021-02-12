__Creating a simple VPC Using CloudFormation__

We can use JSON or YAML to instantiate the CloudFormation template.

AWSTemplateFormatVersion: 2010-09-09

Resources:

  myVPC:
  
    Type: AWS::EC2::VPC
    
    Properties:
    
      CidrBlock: !Ref CidrBlock
      
      EnableDnsHostnames: true
      
      EnableDnsSupport: true
      
      InstanceTenancy: default
      
      
PublicSubnetA:

    Type: AWS::EC2::Subnet
    
    Properties:
    
      VpcId: !Ref myVPC
      
      CidrBlock: !Ref CidrBlock
      
      AvailabilityZone: !Select [ 0, !GetAZs ]    # Get the first AZ in the list     
      
      Tags:
      - Key: Name
        Value: !Sub ${AWS::StackName}-Public-A
        
        
      
        - !Ref AzNumber
        
        - !GetAZs ‘’
        
