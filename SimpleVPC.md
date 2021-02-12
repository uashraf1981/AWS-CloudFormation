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
 
PublicSubnetB:

    Type: AWS::EC2::Subnet
    
    Properties:
    
      VpcId: !Ref VPC
      
      CidrBlock: 10.1.20.0/24
      
      AvailabilityZone: !Select [ 1, !GetAZs ]    # Get the second AZ in the list 
      
      Tags:
      
      - Key: Name
      
        Value: !Sub ${AWS::StackName}-Public-B
        
  PrivateSubnetA:
  
    Type: AWS::EC2::Subnet
    
    Properties:
    
      VpcId: !Ref VPC
      
      CidrBlock: 10.1.50.0/24
      
      AvailabilityZone: !Select [ 0, !GetAZs ]    # Get the first AZ in the list 
      
      Tags:
      
      - Key: Name
        Value: !Sub ${AWS::StackName}-Private-A
        
  PrivateSubnetB:
  
    Type: AWS::EC2::Subnet
    
    Properties:
    
      VpcId: !Ref VPC
      
      CidrBlock: 10.1.60.0/24
      
      AvailabilityZone: !Select [ 1, !GetAZs ]    # Get the second AZ in the list 
      
      Tags:
      
      - Key: Name
      
        Value: !Sub ${AWS::StackName}-Private-B

  # Here is a private route table:
  
  PrivateRouteTable:
  
    Type: AWS::EC2::RouteTable
    
    Properties:
    
      VpcId: !Ref VPC
      
      Tags:
      
      - Key: Name
      
        Value: Private
        
  PrivateRoute1:            # Private route table can access web via NAT (created below)
  
    Type: AWS::EC2::Route
    
    Properties:
      
      RouteTableId: !Ref PrivateRouteTable
      
      DestinationCidrBlock: 0.0.0.0/0
      
      # Route traffic through the NAT Gateway:
      
      NatGatewayId: ???
      
 # A NAT Gateway:
 
 NATGateway:
 
   Type: AWS::EC2::NatGateway
   
   Properties:
   
     AllocationId: !GetAtt ElasticIPAddress.AllocationId
     
     SubnetId: !Ref PublicSubnetA
     
     Tags:
     
     - Key: Name
     
       Value: !Sub NAT-${AWS::StackName}
       
 ElasticIPAddress:
 
   Type: AWS::EC2::EIP
   
   Properties:
   
     Domain: VPC
     
     
     
PrivateRoute1:            # Private route table can access web via NAT (created below)

    Type: AWS::EC2::Route
    
    Properties:
    
      RouteTableId: !Ref PrivateRouteTable
      
      DestinationCidrBlock: 0.0.0.0/0
      
      # Route traffic through the NAT Gateway:
      
      NatGatewayId: !Ref NATGateway

# Attach the public subnets to public route tables,

  # and attach the private subnets to private route tables:   
  
  PublicSubnetARouteTableAssociation:
  
    Type: AWS::EC2::SubnetRouteTableAssociation
    
    Properties:
    
      SubnetId: !Ref PublicSubnetA
      
      RouteTableId: !Ref PublicRouteTable
      
  PublicSubnetBRouteTableAssociation:
  
    Type: AWS::EC2::SubnetRouteTableAssociation
    
    Properties:
    
      SubnetId: !Ref PublicSubnetB
      
      RouteTableId: !Ref PublicRouteTable
      
  PrivateSubnetARouteTableAssociation:
  
    Type: AWS::EC2::SubnetRouteTableAssociation
    
    Properties:
    
      SubnetId: !Ref PrivateSubnetA
      
      RouteTableId: !Ref PrivateRouteTable
      
  PrivateSubnetBRouteTableAssociation:
  
    Type: AWS::EC2::SubnetRouteTableAssociation
    
    Properties:
    
      SubnetId: !Ref PrivateSubnetB
      
      RouteTableId: !Ref PrivateRouteTable
