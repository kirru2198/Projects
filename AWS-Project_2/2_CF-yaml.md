```yaml
AWSTemplateFormatVersion: 2010-09-09
Description: CloudFormation Template for SNS VPC Endpoints Tutorial
Parameters:
  KeyName:
    Description: Name of an existing EC2 KeyPair to enable SSH access to the instance
    Type: 'AWS::EC2::KeyPair::KeyName'
    ConstraintDescription: must be the name of an existing EC2 KeyPair.
  SSHLocation:
    Description: The IP address range that can be used to SSH to the EC2 instance
    Type: String
    MinLength: '9'
    MaxLength: '18'
    Default: 0.0.0.0/0
    AllowedPattern: '(\d{1,3})\.(\d{1,3})\.(\d{1,3})\.(\d{1,3})/(\d{1,2})'
    ConstraintDescription: must be a valid IP CIDR range of the form x.x.x.x/x.
Mappings:
  RegionMap:
    us-east-1:
      AMI: ami-0c293f3f676ec4f90
    us-east-2:
      AMI: ami-08b6f2a5c291246a0
    us-west-1:
      AMI: ami-051317f1184dd6e92
    us-west-2:
      AMI: ami-0b9f27b05e1de14e9
Resources:
  VPC:
    Type: 'AWS::EC2::VPC'
    Properties:
      CidrBlock: 10.0.0.0/16
      EnableDnsSupport: 'true'
      EnableDnsHostnames: 'true'
      Tags:
        - Key: Name
          Value: VPCE-Tutorial-VPC
  Subnet:
    Type: 'AWS::EC2::Subnet'
    Properties:
      VpcId: !Ref VPC
      CidrBlock: 10.0.1.0/24
      Tags:
        - Key: Name
          Value: VPCE-Tutorial-Subnet
  InternetGateway:
    Type: 'AWS::EC2::InternetGateway'
    Properties:
      Tags:
        - Key: Name
          Value: VPCE-Tutorial-InternetGateway
  VPCGatewayAttachment:
    Type: 'AWS::EC2::VPCGatewayAttachment'
    Properties:
      VpcId: !Ref VPC
      InternetGatewayId: !Ref InternetGateway
  RouteTable:
    Type: 'AWS::EC2::RouteTable'
    Properties:
      VpcId: !Ref VPC
      Tags:
        - Key: Name
          Value: VPCE-Tutorial-RouteTable
  SubnetRouteTableAssociation:
    Type: 'AWS::EC2::SubnetRouteTableAssociation'
    Properties:
      RouteTableId: !Ref RouteTable
      SubnetId: !Ref Subnet
  InternetGatewayRoute:
    Type: 'AWS::EC2::Route'
    Properties:
      RouteTableId: !Ref RouteTable
      GatewayId: !Ref InternetGateway
      DestinationCidrBlock: 0.0.0.0/0

  SecurityGroup:
    Type: 'AWS::EC2::SecurityGroup'
    Properties:
      GroupName: Tutorial Security Group
      GroupDescription: Security group for SNS VPC endpoing tutorial
      VpcId: !Ref VPC
      SecurityGroupIngress:
        - IpProtocol: '-1'
          CidrIp: 10.0.0.0/16
        - IpProtocol: tcp
          FromPort: '22'
          ToPort: '22'
          CidrIp: !Ref SSHLocation
      SecurityGroupEgress:
        - IpProtocol: '-1'
          CidrIp: 10.0.0.0/16
      Tags:
        - Key: Name
          Value: VPCE-Tutorial-SecurityGroup
          
  EC2Instance:
    Type: 'AWS::EC2::Instance'
    Properties:
      KeyName: !Ref KeyName
      InstanceType: t2.micro
      ImageId: !FindInMap 
        - RegionMap
        - !Ref 'AWS::Region'
        - AMI
      NetworkInterfaces:
        - AssociatePublicIpAddress: 'true'
          DeviceIndex: '0'
          GroupSet:
            - !Ref SecurityGroup
          SubnetId: !Ref Subnet
      IamInstanceProfile: !Ref EC2InstanceProfile
      Tags:
        - Key: Name
          Value: VPCE-Tutorial-EC2Instance
  EC2InstanceProfile:
    Type: 'AWS::IAM::InstanceProfile'
    Properties:
      Roles:
        - !Ref EC2InstanceRole
      InstanceProfileName: myEC2InstanceProfile-Sudip
  EC2InstanceRole:
    Type: 'AWS::IAM::Role'
    Properties:
      RoleName: VPCE-Tutorial-EC2InstanceRole
      AssumeRolePolicyDocument:
        Version: 2012-10-17
        Statement:
          - Effect: Allow
            Principal:
              Service: ec2.amazonaws.com
            Action: 'sts:AssumeRole'
      ManagedPolicyArns:
        - 'arn:aws:iam::aws:policy/AmazonSNSFullAccess'
  LambdaExecutionRole:
    Type: 'AWS::IAM::Role'
    Properties:
      AssumeRolePolicyDocument:
        Version: 2012-10-17
        Statement:
        - Effect: Allow
          Principal:
            Service: lambda.amazonaws.com
          Action: 'sts:AssumeRole'
      ManagedPolicyArns:
        - 'arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole'
  LambdaFunction1:
    Type: 'AWS::Lambda::Function'
    Properties:
      Code:
        ZipFile: |
          from __future__ import print_function
          print('Loading function')
          def lambda_handler(event, context):
            message = event['Records'][0]['Sns']['Message']
            print("From SNS: " + message)
            return message
      Description: SNS VPC endpoint tutorial lambda function 1
      FunctionName: VPCE-Tutorial-Lambda-1
      Handler: index.lambda_handler
      Role: !GetAtt
        - LambdaExecutionRole
        - Arn
      Runtime: python3.9
      Timeout: '3'
  LambdaPermission1:
    Type: 'AWS::Lambda::Permission'
    Properties:
      Action: 'lambda:InvokeFunction'
      FunctionName: !Ref LambdaFunction1
      Principal: sns.amazonaws.com
      SourceArn: !Ref SNSTopic
  LambdaLogGroup1:
    Type: 'AWS::Logs::LogGroup'
    Properties:
      LogGroupName: !Sub "/aws/lambda/${LambdaFunction1}"
      RetentionInDays: '7'
  LambdaFunction2:
    Type: 'AWS::Lambda::Function'
    Properties:
      Code:
        ZipFile: |
          from __future__ import print_function
          print('Loading function')
          def lambda_handler(event, context):
            message = event['Records'][0]['Sns']['Message']
            print("From SNS: " + message)
            return message
      Description: SNS VPC endpoint tutorial lambda function 2
      FunctionName: VPCE-Tutorial-Lambda-2
      Handler: index.lambda_handler
      Role: !GetAtt
        - LambdaExecutionRole
        - Arn
      Runtime: python3.9
      Timeout: '3'
  LambdaPermission2:
    Type: 'AWS::Lambda::Permission'
    Properties:
      Action: 'lambda:InvokeFunction'
      FunctionName: !Ref LambdaFunction2
      Principal: sns.amazonaws.com
      SourceArn: !Ref SNSTopic
  LambdaLogGroup2:
    Type: 'AWS::Logs::LogGroup'
    Properties:
      LogGroupName: !Sub "/aws/lambda/${LambdaFunction2}"
      RetentionInDays: '7'
  SNSTopic:
    Type: 'AWS::SNS::Topic'
    Properties:
      DisplayName: VPCE-Tutorial-Topic
      TopicName: VPCE-Tutorial-Topic
      Subscription:
        - Endpoint: !GetAtt
            - LambdaFunction1
            - Arn
          Protocol: lambda
        - Endpoint: !GetAtt
            - LambdaFunction2
            - Arn
          Protocol: lambda
```
