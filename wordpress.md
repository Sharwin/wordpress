# Create Networking in Cloudformation

Login to an AWS account and ensure your region is set to us-east-1 N. Virginia use this template in cloudformation

```yaml

Description:  VPC
Resources:
  VPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.16.0.0/16
      EnableDnsSupport: true
      EnableDnsHostnames: true
      Tags:
        - Key: Name
          Value: VPC
  IPv6CidrBlock:
    Type: AWS::EC2::VPCCidrBlock
    Properties:
      VpcId: !Ref VPC
      AmazonProvidedIpv6CidrBlock: true
  InternetGateway:
    Type: 'AWS::EC2::InternetGateway'
    Properties:
      Tags:
      - Key: Name
        Value: IGW
  InternetGatewayAttachment:
    Type: 'AWS::EC2::VPCGatewayAttachment'
    Properties:
      VpcId: !Ref VPC
      InternetGatewayId: !Ref InternetGateway
  RTPub: 
    Type: 'AWS::EC2::RouteTable'
    Properties:
      VpcId: !Ref VPC
      Tags:
      - Key: Name
        Value: vpc-rt-pub
  RTPubDefaultIPv4: 
    Type: 'AWS::EC2::Route'
    DependsOn: InternetGatewayAttachment
    Properties:
      RouteTableId:
        Ref: RTPub
      DestinationCidrBlock: '0.0.0.0/0'
      GatewayId:
        Ref: InternetGateway
  RTPubDefaultIPv6: 
    Type: 'AWS::EC2::Route'
    DependsOn: InternetGatewayAttachment
    Properties:
      RouteTableId:
        Ref: RTPub
      DestinationIpv6CidrBlock: '::/0'
      GatewayId:
        Ref: InternetGateway
  RTAssociationPubA:
    Type: 'AWS::EC2::SubnetRouteTableAssociation'
    Properties:
      SubnetId: !Ref SNPUBA
      RouteTableId:
        Ref: RTPub
  RTAssociationPubB:
    Type: 'AWS::EC2::SubnetRouteTableAssociation'
    Properties:
      SubnetId: !Ref SNPUBB
      RouteTableId:
        Ref: RTPub
  RTAssociationPubC:
    Type: 'AWS::EC2::SubnetRouteTableAssociation'
    Properties:
      SubnetId: !Ref SNPUBC
      RouteTableId:
        Ref: RTPub
  SNDBA:
    Type: AWS::EC2::Subnet
    DependsOn: IPv6CidrBlock
    Properties:
      VpcId: !Ref VPC
      AvailabilityZone: !Select [ 0, !GetAZs '' ]
      CidrBlock: 10.16.16.0/20
      AssignIpv6AddressOnCreation: true
      Ipv6CidrBlock: 
        Fn::Sub:
          - "${VpcPart}${SubnetPart}"
          - SubnetPart: '01::/64'
            VpcPart: !Select [ 0, !Split [ '00::/56', !Select [ 0, !GetAtt VPC.Ipv6CidrBlocks ]]]
      Tags:
        - Key: Name
          Value: sn-db-A
  SNDBB:
    Type: AWS::EC2::Subnet
    DependsOn: IPv6CidrBlock
    Properties:
      VpcId: !Ref VPC
      AvailabilityZone: !Select [ 1, !GetAZs '' ]
      CidrBlock: 10.16.80.0/20
      AssignIpv6AddressOnCreation: true
      Ipv6CidrBlock: 
        Fn::Sub:
          - "${VpcPart}${SubnetPart}"
          - SubnetPart: '05::/64'
            VpcPart: !Select [ 0, !Split [ '00::/56', !Select [ 0, !GetAtt VPC.Ipv6CidrBlocks ]]]
      Tags:
        - Key: Name
          Value: sn-db-B
  SNDBC:
    Type: AWS::EC2::Subnet
    DependsOn: IPv6CidrBlock
    Properties:
      VpcId: !Ref VPC
      AvailabilityZone: !Select [ 2, !GetAZs '' ]
      CidrBlock: 10.16.144.0/20
      AssignIpv6AddressOnCreation: true
      Ipv6CidrBlock: 
        Fn::Sub:
          - "${VpcPart}${SubnetPart}"
          - SubnetPart: '09::/64'
            VpcPart: !Select [ 0, !Split [ '00::/56', !Select [ 0, !GetAtt VPC.Ipv6CidrBlocks ]]]
      Tags:
        - Key: Name
          Value: sn-db-C
  SNAPPA:
    Type: AWS::EC2::Subnet
    DependsOn: IPv6CidrBlock
    Properties:
      VpcId: !Ref VPC
      AvailabilityZone: !Select [ 0, !GetAZs '' ]
      CidrBlock: 10.16.32.0/20
      AssignIpv6AddressOnCreation: true
      Ipv6CidrBlock: 
        Fn::Sub:
          - "${VpcPart}${SubnetPart}"
          - SubnetPart: '02::/64'
            VpcPart: !Select [ 0, !Split [ '00::/56', !Select [ 0, !GetAtt VPC.Ipv6CidrBlocks ]]]
      Tags:
        - Key: Name
          Value: sn-app-A
  SNAPPB:
    Type: AWS::EC2::Subnet
    DependsOn: IPv6CidrBlock
    Properties:
      VpcId: !Ref VPC
      AvailabilityZone: !Select [ 1, !GetAZs '' ]
      CidrBlock: 10.16.96.0/20
      AssignIpv6AddressOnCreation: true
      Ipv6CidrBlock: 
        Fn::Sub:
          - "${VpcPart}${SubnetPart}"
          - SubnetPart: '06::/64'
            VpcPart: !Select [ 0, !Split [ '00::/56', !Select [ 0, !GetAtt VPC.Ipv6CidrBlocks ]]]
      Tags:
        - Key: Name
          Value: sn-app-B
  SNAPPC:
    Type: AWS::EC2::Subnet
    DependsOn: IPv6CidrBlock
    Properties:
      VpcId: !Ref VPC
      AvailabilityZone: !Select [ 2, !GetAZs '' ]
      CidrBlock: 10.16.160.0/20
      AssignIpv6AddressOnCreation: true
      Ipv6CidrBlock: 
        Fn::Sub:
          - "${VpcPart}${SubnetPart}"
          - SubnetPart: '0A::/64'
            VpcPart: !Select [ 0, !Split [ '00::/56', !Select [ 0, !GetAtt VPC.Ipv6CidrBlocks ]]]
      Tags:
        - Key: Name
          Value: sn-app-C
  SNPUBA:
    Type: AWS::EC2::Subnet
    DependsOn: IPv6CidrBlock
    Properties:
      VpcId: !Ref VPC
      AvailabilityZone: !Select [ 0, !GetAZs '' ]
      CidrBlock: 10.16.48.0/20
      MapPublicIpOnLaunch: true
      Ipv6CidrBlock: 
        Fn::Sub:
          - "${VpcPart}${SubnetPart}"
          - SubnetPart: '03::/64'
            VpcPart: !Select [ 0, !Split [ '00::/56', !Select [ 0, !GetAtt VPC.Ipv6CidrBlocks ]]]
      Tags:
        - Key: Name
          Value: sn-pub-A
  SNPUBB:
    Type: AWS::EC2::Subnet
    DependsOn: IPv6CidrBlock
    Properties:
      VpcId: !Ref VPC
      AvailabilityZone: !Select [ 1, !GetAZs '' ]
      CidrBlock: 10.16.112.0/20
      MapPublicIpOnLaunch: true
      Ipv6CidrBlock: 
        Fn::Sub:
          - "${VpcPart}${SubnetPart}"
          - SubnetPart: '07::/64'
            VpcPart: !Select [ 0, !Split [ '00::/56', !Select [ 0, !GetAtt VPC.Ipv6CidrBlocks ]]]
      Tags:
        - Key: Name
          Value: sn-pub-B
  SNPUBC:
    Type: AWS::EC2::Subnet
    DependsOn: IPv6CidrBlock
    Properties:
      VpcId: !Ref VPC
      AvailabilityZone: !Select [ 2, !GetAZs '' ]
      CidrBlock: 10.16.176.0/20
      MapPublicIpOnLaunch: true
      Ipv6CidrBlock: 
        Fn::Sub:
          - "${VpcPart}${SubnetPart}"
          - SubnetPart: '0B::/64'
            VpcPart: !Select [ 0, !Split [ '00::/56', !Select [ 0, !GetAtt VPC.Ipv6CidrBlocks ]]]
      Tags:
        - Key: Name
          Value: sn-pub-C
  IPv6WorkaroundSubnetPUBA:
    Type: Custom::SubnetModify
    Properties:
      ServiceToken: !GetAtt IPv6WorkaroundLambda.Arn
      SubnetId: !Ref SNPUBA
  IPv6WorkaroundSubnetPUBB:
    Type: Custom::SubnetModify
    Properties:
      ServiceToken: !GetAtt IPv6WorkaroundLambda.Arn
      SubnetId: !Ref SNPUBB
  IPv6WorkaroundSubnetPUBC:
    Type: Custom::SubnetModify
    Properties:
      ServiceToken: !GetAtt IPv6WorkaroundLambda.Arn
      SubnetId: !Ref SNPUBC
  IPv6WorkaroundRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
        - Effect: Allow
          Principal:
            Service:
            - lambda.amazonaws.com
          Action:
          - sts:AssumeRole
      Path: "/"
      Policies:
        - PolicyName: !Sub "ipv6-fix-logs-${AWS::StackName}"
          PolicyDocument:
            Version: '2012-10-17'
            Statement:
            - Effect: Allow
              Action:
              - logs:CreateLogGroup
              - logs:CreateLogStream
              - logs:PutLogEvents
              Resource: arn:aws:logs:*:*:*
        - PolicyName: !Sub "ipv6-fix-modify-${AWS::StackName}"
          PolicyDocument:
            Version: '2012-10-17'
            Statement:
            - Effect: Allow
              Action:
              - ec2:ModifySubnetAttribute
              Resource: "*"
  IPv6WorkaroundLambda:
    Type: AWS::Lambda::Function
    Properties:
      Handler: "index.lambda_handler"
      Code: #import cfnresponse below required to send respose back to CFN
        ZipFile:
          Fn::Sub: |
            import cfnresponse
            import boto3

            def lambda_handler(event, context):
                if event['RequestType'] is 'Delete':
                  cfnresponse.send(event, context, cfnresponse.SUCCESS)
                  return

                responseValue = event['ResourceProperties']['SubnetId']
                ec2 = boto3.client('ec2', region_name='${AWS::Region}')
                ec2.modify_subnet_attribute(AssignIpv6AddressOnCreation={
                                                'Value': True
                                              },
                                              SubnetId=responseValue)
                responseData = {}
                responseData['SubnetId'] = responseValue
                cfnresponse.send(event, context, cfnresponse.SUCCESS, responseData, "CustomResourcePhysicalID")
      Runtime: python3.9
      Role: !GetAtt IPv6WorkaroundRole.Arn
      Timeout: 30
  SGWordpress:
    Type: 'AWS::EC2::SecurityGroup'
    Properties:
      VpcId: !Ref VPC
      GroupDescription: Control access to Wordpress Instance(s)
      SecurityGroupIngress: 
        - Description: 'Allow HTTP IPv4 IN'
          IpProtocol: tcp
          FromPort: '80'
          ToPort: '80'
          CidrIp: '0.0.0.0/0'
  SGDatabase:
    Type: 'AWS::EC2::SecurityGroup'
    Properties:
      VpcId: !Ref VPC
      GroupDescription: Control access to Database
      SecurityGroupIngress: 
        - Description: 'Allow MySQL IN'
          IpProtocol: tcp
          FromPort: '3306'
          ToPort: '3306'
          SourceSecurityGroupId: !Ref SGWordpress
  SGLoadBalancer:
    Type: 'AWS::EC2::SecurityGroup'
    Properties:
      VpcId: !Ref VPC
      GroupDescription: Control access to Load Balancer
      SecurityGroupIngress: 
        - Description: 'Allow HTTP IPv4 IN'
          IpProtocol: tcp
          FromPort: '80'
          ToPort: '80'
          CidrIp: '0.0.0.0/0'
  SGEFS:
    Type: 'AWS::EC2::SecurityGroup'
    Properties:
      VpcId: !Ref VPC
      GroupDescription: Control access to EFS
      SecurityGroupIngress: 
        - Description: 'Allow NFS/EFS IPv4 IN'
          IpProtocol: tcp
          FromPort: '2049'
          ToPort: '2049'
          SourceSecurityGroupId: !Ref SGWordpress
  WordpressRole:
    Type: 'AWS::IAM::Role'
    Properties:
      AssumeRolePolicyDocument:
        Version: 2012-10-17
        Statement:
          - Effect: Allow
            Principal:
              Service:
              - ec2.amazonaws.com
            Action:
              - 'sts:AssumeRole'
      Path: /
      ManagedPolicyArns: 
        - "arn:aws:iam::aws:policy/CloudWatchAgentServerPolicy"
        - "arn:aws:iam::aws:policy/AmazonSSMFullAccess"
        - "arn:aws:iam::aws:policy/AmazonElasticFileSystemClientFullAccess"
  WordpressInstanceProfile:
    Type: 'AWS::IAM::InstanceProfile'
    Properties:
      Path: /
      Roles:
        - !Ref WordpressRole
  CWAgentConfig:
    Type: AWS::SSM::Parameter
    Properties:
      Type: 'String'
      Value: |
        {
          "agent": {
            "metrics_collection_interval": 60,
            "run_as_user": "root"
          },
          "logs": {
            "logs_collected": {
              "files": {
                "collect_list": [
                  {
                    "file_path": "/var/log/secure",
                    "log_group_name": "/var/log/secure",
                    "log_stream_name": "{instance_id}"
                  },
                  {
                    "file_path": "/var/log/httpd/access_log",
                    "log_group_name": "/var/log/httpd/access_log",
                    "log_stream_name": "{instance_id}"
                  },
                  {
                    "file_path": "/var/log/httpd/error_log",
                    "log_group_name": "/var/log/httpd/error_log",
                    "log_stream_name": "{instance_id}"
                  }
                ]
              }
            }
          },
          "metrics": {
            "append_dimensions": {
              "AutoScalingGroupName": "${aws:AutoScalingGroupName}",
              "ImageId": "${aws:ImageId}",
              "InstanceId": "${aws:InstanceId}",
              "InstanceType": "${aws:InstanceType}"
            },
            "metrics_collected": {
              "collectd": {
                "metrics_aggregation_interval": 60
              },
              "cpu": {
                "measurement": [
                  "cpu_usage_idle",
                  "cpu_usage_iowait",
                  "cpu_usage_user",
                  "cpu_usage_system"
                ],
                "metrics_collection_interval": 60,
                "resources": [
                  "*"
                ],
                "totalcpu": false
              },
              "disk": {
                "measurement": [
                  "used_percent",
                  "inodes_free"
                ],
                "metrics_collection_interval": 60,
                "resources": [
                  "*"
                ]
              },
              "diskio": {
                "measurement": [
                  "io_time",
                  "write_bytes",
                  "read_bytes",
                  "writes",
                  "reads"
                ],
                "metrics_collection_interval": 60,
                "resources": [
                  "*"
                ]
              },
              "mem": {
                "measurement": [
                  "mem_used_percent"
                ],
                "metrics_collection_interval": 60
              },
              "netstat": {
                "measurement": [
                  "tcp_established",
                  "tcp_time_wait"
                ],
                "metrics_collection_interval": 60
              },
              "statsd": {
                "metrics_aggregation_interval": 60,
                "metrics_collection_interval": 10,
                "service_address": ":8125"
              },
              "swap": {
                "measurement": [
                  "swap_used_percent"
                ],
                "metrics_collection_interval": 60
              }
            }
          }
        }
```


# Create an EC2 Instance to run wordpress

Move to the EC2 console https://console.aws.amazon.com/ec2/v2/home?region=us-east-1  
Click `Launch Instance`  

For `name` use `Wordpress-Manual`  
Select `Amazon Linux`  
From the dropdown make sure `Amazon Linux 2023` AMI is selected  
ensure `64-bit (x86)` is selected in the architecture dropdown.  
Under `instance type`  
Select whatever instance shows as `Free tier eligible` (probably t2 or t3.micro)  
Under `Key Pair(login)` select `Proceed without a KeyPair (not recommended)`  
For `Network Settings`, click `Edit` and in the VPC download select `VPC`  
for `Subnet` select `sn-Pub-A`  
Make sure for both `Auto-assign public IP` and `Auto-assign IPv6 IP` you set to `Enable`  
Under security Group
Check `Select an existing security group`  
Select `VPC-SGWordpress` it will have randomness after it, thats ok :)  
We will leave storage as default so make no changes here  
Expand `Advanced Details`  
For `IAM instance profile role` select `VPC-WordpressInstanceProfile`  **THIS BIT IS IMPORTANT**  
Find the `Credit Specification Dropdown` and choose `Standard` (some accounts aren't enabled for Unlimited)
Click `Launch Instance`    
Click `View All instances`  

Wait for the instance to be in a `RUNNING` state  
_you can continue to stage 1B below while the instance is provisioning_

# STAGE 1B - Create SSM Parameter Store values for wordpress

Storing configuration information within the SSM Parameter store scales much better than attempting to script them in some way.
In this sub-section you are going to create parameters to store the important configuration items for the platform you are building.  

Open a new tab to https://console.aws.amazon.com/systems-manager/home?region=us-east-1  
Click on `Parameter Store` on the menu on the left

**MAKE SURE WITH THE BELOW ... NO WHITESPACE BEFORE OR AFTER .. MAKE SURE THE UPPER/LOWER CASE IS CORRECT**

## Create Parameter - DBUser (the login for the specific wordpress DB)  
Click `Create Parameter`
Set Name to `/Wordpress/DBUser`
Set Description to `Wordpress Database User`  
Set Tier to `Standard`  
Set Type to `String`  
Set Data type to `text`  
Set `Value` to `wordpressuser`  
Click `Create parameter`  

## Create Parameter - DBName (the name of the wordpress database)  
Click `Create Parameter`
Set Name to `/Wordpress/DBName`
Set Description to `Wordpress Database Name`  
Set Tier to `Standard`  
Set Type to `String`  
Set Data type to `text`  
Set `Value` to `wordpressdb`  
Click `Create parameter` 

## Create Parameter - DBEndpoint (the endpoint for the wordpress DB .. )  
Click `Create Parameter`
Set Name to `/Wordpress/DBEndpoint`
Set Description to `Wordpress Endpoint Name`  
Set Tier to `Standard`  
Set Type to `String`  
Set Data type to `text`  
Set `Value` to `localhost`  
Click `Create parameter`  

## Create Parameter - DBPassword (the password for the DBUser)  
Click `Create Parameter`
Set Name to `/Wordpress/DBPassword`
Set Description to `Wordpress DB Password`  
Set Tier to `Standard`  
Set Type to `SecureString`  
Set `KMS Key Source` to `My Current Account`  
Leave `KMS Key ID` as default (should be `alias/aws/ssm`).  
Set `Value` to `MySecurePassw0rd`  
Click `Create parameter`  

## Create Parameter - DBRootPassword (the password for the database root user, used for self-managed admin)  
Click `Create Parameter`
Set Name to `/Wordpress/DBRootPassword`
Set Description to `Wordpress DBRoot Password`  
Set Tier to `Standard`  
Set Type to `SecureString`  
Set `KMS Key Source` to `My Current Account`  
Leave `KMS Key ID` as default (should be `alias/aws/ssm`).   
Set `Value` to `MySecurePassw0rd`  
Click `Create parameter`  

# STAGE 1C - Connect to the instance and install a database and wordpress

Right click on `Wordpress-Manual` choose `Connect`
Choose `Session Manager`  
Click `Connect`  (if connect isn't highlighted then just wait a few minutes and try again).
*if after 5-10 minutes this still doesn't let you connect, it's possible you didn't add the `VPC-WordpressInstanceProfile` instance role. You need to right click on the instance, security, modify IAM role and then add `VPC-WordpressInstanceProfile`.  Once done, reboot the instance*   
type `sudo bash` and press enter  
type `cd` and press enter  
type `clear` and press enter

## Bring in the parameter values from SSM

Run the commands below to bring the parameter store values into ENV variables to make the manual build easier. 
**IF AT ANY POINT YOU ARE DISCONNECTED, RERUN THE BLOCK BELOW, THEN CONTINUE FROM WHERE YOU DISCONNECTED FROM**

```
DBPassword=$(aws ssm get-parameters --region us-east-1 --names /Wordpress/DBPassword --with-decryption --query Parameters[0].Value)
DBPassword=`echo $DBPassword | sed -e 's/^"//' -e 's/"$//'`

DBRootPassword=$(aws ssm get-parameters --region us-east-1 --names /Wordpress/DBRootPassword --with-decryption --query Parameters[0].Value)
DBRootPassword=`echo $DBRootPassword | sed -e 's/^"//' -e 's/"$//'`

DBUser=$(aws ssm get-parameters --region us-east-1 --names /Wordpress/DBUser --query Parameters[0].Value)
DBUser=`echo $DBUser | sed -e 's/^"//' -e 's/"$//'`

DBName=$(aws ssm get-parameters --region us-east-1 --names /Wordpress/DBName --query Parameters[0].Value)
DBName=`echo $DBName | sed -e 's/^"//' -e 's/"$//'`

DBEndpoint=$(aws ssm get-parameters --region us-east-1 --names /Wordpress/DBEndpoint --query Parameters[0].Value)
DBEndpoint=`echo $DBEndpoint | sed -e 's/^"//' -e 's/"$//'`

```

## Install updates

```
sudo dnf -y update

```

## Install Pre-Reqs and Web Server

```
sudo dnf install wget php-mysqlnd httpd php-fpm php-mysqli mariadb105-server php-json php php-devel stress -y

```

## Set DB and HTTP Server to running and start by default

```
sudo systemctl enable httpd
sudo systemctl enable mariadb
sudo systemctl start httpd
sudo systemctl start mariadb
```

## Set the MariaDB Root Password

```
sudo mysqladmin -u root password $DBRootPassword
```

## Download and extract Wordpress

```
sudo wget http://wordpress.org/latest.tar.gz -P /var/www/html
cd /var/www/html
sudo tar -zxvf latest.tar.gz
sudo cp -rvf wordpress/* .
sudo rm -R wordpress
sudo rm latest.tar.gz
```

## Configure the wordpress wp-config.php file 

```
sudo cp ./wp-config-sample.php ./wp-config.php
sudo sed -i "s/'database_name_here'/'$DBName'/g" wp-config.php
sudo sed -i "s/'username_here'/'$DBUser'/g" wp-config.php
sudo sed -i "s/'password_here'/'$DBPassword'/g" wp-config.php
```

## Fix Permissions on the filesystem

```
sudo usermod -a -G apache ec2-user   
sudo chown -R ec2-user:apache /var/www
sudo chmod 2775 /var/www
sudo find /var/www -type d -exec chmod 2775 {} \;
sudo find /var/www -type f -exec chmod 0664 {} \;
```

## Create Wordpress User, set its password, create the database and configure permissions

```
sudo echo "CREATE DATABASE $DBName;" >> /tmp/db.setup
sudo echo "CREATE USER '$DBUser'@'localhost' IDENTIFIED BY '$DBPassword';" >> /tmp/db.setup
sudo echo "GRANT ALL ON $DBName.* TO '$DBUser'@'localhost';" >> /tmp/db.setup
sudo echo "FLUSH PRIVILEGES;" >> /tmp/db.setup
sudo mysql -u root --password=$DBRootPassword < /tmp/db.setup
sudo rm /tmp/db.setup
```

## Test Wordpress is installed

Open the EC2 console https://console.aws.amazon.com/ec2/v2/home?region=us-east-1#Instances:sort=desc:tag:Name  
Select the `Wordpress-Manual` instance  
copy the `IPv4 Public IP` into your clipboard (**DON'T CLICK THE OPEN LINK ... just copy the IP**)
Open that IP in a new tab  
You should see the wordpress welcome page  

## Perform Initial Configuration and make a post

in `Site Title` enter `Catagram`  
in `Username` enter `admin`
in `Password` enter `MySecurePassw0rd`  
in `Your Email` enter your email address  
Click `Install WordPress`
Click `Log In`  
In `Username or Email Address` enter `admin`  
in `Password` enter the previously noted down strong password  
Click `Log In`  

This is your working, manually installed and configured wordpress

# STAGE 1 TIDYUP

Right click on the manual instance you created in the previous step called `Wordpress-Manual` and select `Terminate Instance` and confirm that termination.  

# STAGE 2A - Create the Launch Template

Open the EC2 console https://console.aws.amazon.com/ec2/v2/home?region=us-east-1#Instances:sort=desc:tag:Name  
Click `Launch Templates` under `Instances` on the left menu  
Click `Create Launch Template`  
Under `Launch Template Name` enter `Wordpress`  
Under `Template version description` enter `Single server DB and App`  
Check the `Provide guidance to help me set up a template that I can use with EC2 Auto Scaling` box  

Under `Application and OS Images (Amazon Machine Image)` click `Quick Start`  
Click `Amazon Linux`  
in the `Amazon Machine Image` dropdown, locate `Amazon Linux 2023 AMI` and set the Architecture to `64-bit (x86)`  
Under `Instance Type` select whichever instance is free tier eligible from either `t3.micro` and `t2.micro`    
Under `Key pair (login)` select `Don't include in launch template`  
Under `networking Settings` `select existing security group` and choose `VPC-SGWordpress`
Leave storage volumes unchanged  
Leave Resource Tags Unchanged  
Expand `Advanced Details`
Under `IAM instance profile` select `VPC-WordpressInstanceProfile` there will be some random at the end, thats ok!  
Under `Credit specification` select `Standard`

# STAGE 2B - Add Userdata

At this point we need to add the configuration which will build the instance
Enter the user data below into the `User Data` box

```
#!/bin/bash -xe

DBPassword=$(aws ssm get-parameters --region us-east-1 --names /Wordpress/DBPassword --with-decryption --query Parameters[0].Value)
DBPassword=`echo $DBPassword | sed -e 's/^"//' -e 's/"$//'`

DBRootPassword=$(aws ssm get-parameters --region us-east-1 --names /Wordpress/DBRootPassword --with-decryption --query Parameters[0].Value)
DBRootPassword=`echo $DBRootPassword | sed -e 's/^"//' -e 's/"$//'`

DBUser=$(aws ssm get-parameters --region us-east-1 --names /Wordpress/DBUser --query Parameters[0].Value)
DBUser=`echo $DBUser | sed -e 's/^"//' -e 's/"$//'`

DBName=$(aws ssm get-parameters --region us-east-1 --names /Wordpress/DBName --query Parameters[0].Value)
DBName=`echo $DBName | sed -e 's/^"//' -e 's/"$//'`

DBEndpoint=$(aws ssm get-parameters --region us-east-1 --names /Wordpress/DBEndpoint --query Parameters[0].Value)
DBEndpoint=`echo $DBEndpoint | sed -e 's/^"//' -e 's/"$//'`

dnf -y update

dnf install wget php-mysqlnd httpd php-fpm php-mysqli mariadb105-server php-json php php-devel stress -y

systemctl enable httpd
systemctl enable mariadb
systemctl start httpd
systemctl start mariadb

mysqladmin -u root password $DBRootPassword

wget http://wordpress.org/latest.tar.gz -P /var/www/html
cd /var/www/html
tar -zxvf latest.tar.gz
cp -rvf wordpress/* .
rm -R wordpress
rm latest.tar.gz

sudo cp ./wp-config-sample.php ./wp-config.php
sed -i "s/'database_name_here'/'$DBName'/g" wp-config.php
sed -i "s/'username_here'/'$DBUser'/g" wp-config.php
sed -i "s/'password_here'/'$DBPassword'/g" wp-config.php
sed -i "s/'localhost'/'$DBEndpoint'/g" wp-config.php

usermod -a -G apache ec2-user   
chown -R ec2-user:apache /var/www
chmod 2775 /var/www
find /var/www -type d -exec chmod 2775 {} \;
find /var/www -type f -exec chmod 0664 {} \;

echo "CREATE DATABASE $DBName;" >> /tmp/db.setup
echo "CREATE USER '$DBUser'@'localhost' IDENTIFIED BY '$DBPassword';" >> /tmp/db.setup
echo "GRANT ALL ON $DBName.* TO '$DBUser'@'localhost';" >> /tmp/db.setup
echo "FLUSH PRIVILEGES;" >> /tmp/db.setup
mysql -u root --password=$DBRootPassword < /tmp/db.setup
rm /tmp/db.setup


```

Ensure to leave a blank line at the end  
Click `Create Launch Template`  
Click `View launch templates`  

# STAGE 2C - Launch an instance using it

Select the launch template in the list ... it should be called `Wordpress`  
Click `Actions` and `Launch instance from template`
Under `Key Pair(login` click the dropdown and select `Proceed without a key pair(Not Recommended)`  
Scroll down to `Network settings` and under `Subnet` select `sn-pub-A`  
Scroll to `Resource Tags` click `Add tag`
Set `Key` to `Name` and `Value` to `Wordpress-LT`
Scroll to the bottom and click `Launch Instance`  
Click the instance id in the `Success` box

# STAGE 2D - Test
*you will need to wait until the instance is running with 2/2 status checks before contuining*  

Open the EC2 console https://console.aws.amazon.com/ec2/v2/home?region=us-east-1#Instances:sort=desc:tag:Name  
Select the `Wordpress-LT` instance  
copy the `IPv4 Public IP` into your clipboard, don't click the link to open, this will use https and we want http, just copy the IP.   
Open that IP in a new tab  
You should see the WordPress welcome page  

## Perform Initial COnfiguration and make a post

in `Site Title` enter `Catagram`  
in `Username` enter `admin`
in `Password` enter `MySecurePassw0rd`  
in `Your Email` enter your email address  
Click `Install WordPress`
Click `Log In`  
In `Username or Email Address` enter `admin`  
in `Password` enter the previously noted down strong password 
Click `Log In`  

Click `Posts` in the menu on the left  
Select `Hello World!` 
Click `Bulk Actions` and select `Move to Trash`
Click `Apply`   

This is your working, auto built WordPress instance
** don't terminate the instance this time - we're going to migrate the database in stage 3**

# STAGE 3A - Create RDS Subnet Group

A subnet group is what allows RDS to select from a range of subnets to put its databases inside  
In this case you will give it a selection of 3 subnets sn-db-A / B and C  
RDS can then decide freely which to use.  

Move to the RDS Console https://console.aws.amazon.com/rds/home?region=us-east-1#  
Click `Subnet Groups`  
Click `Create DB Subnet Group`  
Under `Name` enter `WordPressRDSSubNetGroup`  
Under `Description` enter `RDS Subnet Group for WordPress`  
Under `VPC` select `VPC`  

Under `Add subnets` 
In `Availability Zones` select `us-east-1a` & `us-east-1b` & `us-east-1c`  
Under `Subnets` check the box next to 

- 10.16.16.0/20 (this is sn-db-A)
- 10.16.80.0/20 (this is sn-db-B)
- 10.16.144.0/20 (this is sn-db-C)

Click `Create`  

# STAGE 3B - Create RDS Instance

In this sub stage of the demo, you are going to provision an RDS instance using the subnet group to control placement within the VPC.   
Normally you would use multi-az for production, to keep costs low, for now you should use a single AZ as per the instructions below.  

Click `Databases`  
Click `Create Database`  
Click `Standard Create`  
Click `MySql`  
Under `Version` select `MySQL 8.0.32`   

Scroll down and select `Free Tier` under templates
_this ensures there will be no costs for the database but it will be single AZ only_

under `Db instance identifier` enter `WordPress`
under `Master Username` enter `wordpressuser`  
under `Master Password` and `Confirm Password` enter `MySecurePassw0rd`   

Under `DB Instance size`, then `DB instance class`, then `Burstable classes (includes t classes)` make sure db.t3.micro or db.t2.micro or db.t4g.micro is selected  

Scroll down, under `Connectivity`, `Compute resource` select `Don’t connect to an EC2 compute resource`  
under `Connectivity`, `Network type` select `IPv4`  
under `Connectivity`, `Virtual private cloud (VPC)` select `VPC`  
under `Subnet group` that `wordpressrdssubnetgroup` is selected  
Make sure `Public Access` is set to `No`  
Under `VPC security groups` make sure `choose existing` is selected, remove `default` and add `VPC-SG-Database`  
Under `Availability Zone` set `us-east-1a`  

**IMPORTANT .. DON'T MISS THIS STEP**
Scroll down past `Database Authentication` & `Monitoring` and expand `Additional configuration`  
in the `Initial database name` box enter `wordpressdb`  
Scroll to the bottom and click `create Database`  

** this will take anywhere up to 30 minutes to create ... it will need to be fully ready before you move to the next step - coffee time !!!! **

# STAGE 3C - Migrate WordPress data from MariaDB to RDS

Open the EC2 Console https://console.aws.amazon.com/ec2/v2/home?region=us-east-1#Home:  
Click `Instances`  
Locate the `WordPress-LT` instance, right click, `Connect` and choose `Session Manager` and then click `Connect`  
Type `sudo bash`  
Type `cd`  
Type `clear`  

## Populate Environment Variables

You're going to do an export of the SQL database running on the local ec2 instance

First run these commands to populate variables with the data from Parameter store, it avoids having to keep locating passwords  
```
DBPassword=$(aws ssm get-parameters --region us-east-1 --names /Wordpress/DBPassword --with-decryption --query Parameters[0].Value)
DBPassword=`echo $DBPassword | sed -e 's/^"//' -e 's/"$//'`

DBRootPassword=$(aws ssm get-parameters --region us-east-1 --names /Wordpress/DBRootPassword --with-decryption --query Parameters[0].Value)
DBRootPassword=`echo $DBRootPassword | sed -e 's/^"//' -e 's/"$//'`

DBUser=$(aws ssm get-parameters --region us-east-1 --names /Wordpress/DBUser --query Parameters[0].Value)
DBUser=`echo $DBUser | sed -e 's/^"//' -e 's/"$//'`

DBName=$(aws ssm get-parameters --region us-east-1 --names /Wordpress/DBName --query Parameters[0].Value)
DBName=`echo $DBName | sed -e 's/^"//' -e 's/"$//'`

DBEndpoint=$(aws ssm get-parameters --region us-east-1 --names /Wordpress/DBEndpoint --query Parameters[0].Value)
DBEndpoint=`echo $DBEndpoint | sed -e 's/^"//' -e 's/"$//'`

```

## Take a Backup of the local DB

To take a backup of the database run

```
mysqldump -h $DBEndpoint -u $DBUser -p$DBPassword $DBName > WordPress.sql
```
** in production you wouldnt put the password in the CLI like this, its a security risk since a ps -aux can see it .. but security isnt the focus of this demo its the process of rearchitecting **

## Restore that Backup into RDS

Move to the RDS Console https://console.aws.amazon.com/rds/home?region=us-east-1#databases:  
Click the `WordPressdb` instance  
Copy the `endpoint` into your clipboard  
Move to the Parameter store https://console.aws.amazon.com/systems-manager/parameters?region=us-east-1  
Check the box next to `/Wordpress/DBEndpoint` and click `Delete` (please do delete this, not just edit the existing one)  
Click `Create Parameter`  

Under `Name` enter `/Wordpress/DBEndpoint`  
Under `Descripton` enter `WordPress DB Endpoint Name`  
Under `Tier` select `Standard`    
Under `Type` select `String`  
Under `Data Type` select `text`  
Under `Value` enter the RDS endpoint endpoint you just copied  
Click `Create Parameter`  

Update the DbEndpoint environment variable with 

```
DBEndpoint=$(aws ssm get-parameters --region us-east-1 --names /Wordpress/DBEndpoint --query Parameters[0].Value)
DBEndpoint=`echo $DBEndpoint | sed -e 's/^"//' -e 's/"$//'`
```

Restore the database export into RDS using

```
mysql -h $DBEndpoint -u $DBUser -p$DBPassword $DBName < WordPress.sql 
```

## Change the WordPress config file to use RDS

this command will substitute `localhost` in the config file for the contents of `$DBEndpoint` which is the RDS instance

```
sudo sed -i "s/'localhost'/'$DBEndpoint'/g" /var/www/html/wp-config.php
```


# STAGE 3D - Stop the MariaDB Service

```
sudo systemctl disable mariadb
sudo systemctl stop mariadb
```


# STAGE 3E - Test WordPress

Move to the EC2 Console https://console.aws.amazon.com/ec2/v2/home?region=us-east-1#Instances:sort=desc:tag:Name  
Select the `WordPress-LT` Instance  
copy the `IPv4 Public IP` into your clipboard  
Open the IP in a new tab  
You should see the blog, working, even though MariaDB on the EC2 instance is stopped and disabled
Its now running using RDS  

# STAGE 3F - Update the LT so it doesnt install 

Move to the EC2 Console https://console.aws.amazon.com/ec2/v2/home?region=us-east-1#Home:  
Under `Instances` click `Launch Templates`  
Select the `WordPress` launch Template (select, dont click)
Click `Actions` and `Modify Template (Create new version)`  
This template version will be based on the existing version ... so many of the values will be populated already  
Under `Template version description` enter `Single server App Only - RDS DB`

Scroll down to `Advanced details` and expand it  
Scroll down to `User Data` and expand the text box as much as possible


Locate and remove the following lines

```
systemctl enable mariadb
systemctl start mariadb
mysqladmin -u root password $DBRootPassword


echo "CREATE DATABASE $DBName;" >> /tmp/db.setup
echo "CREATE USER '$DBUser'@'localhost' IDENTIFIED BY '$DBPassword';" >> /tmp/db.setup
echo "GRANT ALL ON $DBName.* TO '$DBUser'@'localhost';" >> /tmp/db.setup
echo "FLUSH PRIVILEGES;" >> /tmp/db.setup
mysql -u root --password=$DBRootPassword < /tmp/db.setup
rm /tmp/db.setup

```


Click `Create Template Version`  
Click `View Launch Template`  
Select the template again (dont click)
Click `Actions` and select `Set Default Version`  
Under `Template version` select `2`  
Click `Set as default version`  

# STAGE 4A - Create EFS File System

## File System Settings

Move to the EFS Console https://console.aws.amazon.com/efs/home?region=us-east-1#/get-started  
Click on `Create file System`  
We're going to step through the full configuration options, so click on `Customize`  
For `Name` type `-WORDPRESS-CONTENT`  
This is critical data so for `Storage Class` leave this set to `Standard` and ensure `Enable Automatic Backups` is enabled.  
for `LifeCycle management` ...  
for `Transition into IA` set to `30 days since last access`  
for `Transition out of IA` set to `None`  
Untick `Enable encryption of data at rest` .. in production you would leave this on, but for this demo which focusses on architecture it simplifies the implementation. 

Scroll down...  
For `throughput modes` choose `Bursting`   
Expand `Additional Settings` and ensure `Performance Mode` is set to `General Purpose`  
Click `Next`

## Network Settings

In this part you will be configuing the EFS `Mount Targets` which are the network interfaces in the VPC which your instances will connect with.  

In the `Virtual Private Cloud (VPC)` dropdown select `VPC`  
You should see 3 rows.  
Make sure `us-east-1a`, `us-east-1b` & `us-east-1c` are selected in each row.  
In `us-east-1a` row, select `sn-App-A` in the subnet ID dropdown, and in the security groups dropdown select `VPC-SGEFS` & remove the default security group  
In `us-east-1b` row, select `sn-App-B` in the subnet ID dropdown, and in the security groups dropdown select `VPC-SGEFS` & remove the default security group  
In `us-east-1c` row, select `sn-App-C` in the subnet ID dropdown, and in the security groups dropdown select `VPC-SGEFS` & remove the default security group  

Click `next`  
Leave all these options as default and click `next`  
We wont be setting a file system policy so click `Create`  

The file system will start in the `Creating` State and then move to `Available` once it does..  
Click on the file system to enter it and click `Network`  
Scroll down and all the mount points will show as `creating` keep hitting refresh and wait for all 3 to show as available before moving on.  

Note down the `fs-XXXXXXXX` or `DNS name` (either will work) once visible at the top of this screen, you will need it in the next step.  


# STAGE 4B - Add an fsid to parameter store

Now that the file system has been created, you need to add another parameter store value for the file system ID so that the automatically built instance(s) can load this safely.

Move to the Systems Manager console https://console.aws.amazon.com/systems-manager/home?region=us-east-1#  
Click on `Parameter Store` on the left menu  
Click `Create Parameter`  
Under `Name` enter `/Wordpress/EFSFSID` 
Under `Description` enter `File System ID for Wordpress Content (wp-content)`  
for `Tier` set `Standard`  
For `Type` set `String`  
for `Data Type` set `text`  
for `Value` set the file system ID `fs-XXXXXXX` which you just noted down (use your own file system ID)  
Click `Create Parameter`  


# STAGE 4C - Connect the file system to the EC2 instance & copy data

Open the EC2 console and go to running instances https://console.aws.amazon.com/ec2/v2/home?region=us-east-1#Instances:sort=desc:tag:Name  
Select the `Wordpress-LT` instance, right click, `Connect`, Select `Session Manager` and click `Connect`  
type `sudo bash` and press enter   
type `cd` and press enter  
type `clear` and press enter  

First we need to install the amazon EFS utilities to allow the instance to connect to EFS. EFS is based on NFS which is standard but the EFS tooling makes things easier.  

```
sudo dnf -y install amazon-efs-utils
```

next you need to migrate the existing media content from wp-content into EFS, and this is a multi step process.

First, copy the content to a temporary location and make a new empty folder.

```
cd /var/www/html
sudo mv wp-content/ /tmp
sudo mkdir wp-content
```

then get the efs file system ID from parameter store

```
EFSFSID=$(aws ssm get-parameters --region us-east-1 --names /Wordpress/EFSFSID --query Parameters[0].Value)
EFSFSID=`echo $EFSFSID | sed -e 's/^"//' -e 's/"$//'`
```

Next .. add a line to /etc/fstab to configure the EFS file system to mount as /var/www/html/wp-content/

```
echo -e "$EFSFSID:/ /var/www/html/wp-content efs _netdev,tls,iam 0 0" >> /etc/fstab
```

```
mount -a -t efs defaults
```

now we need to copy the origin content data back in and fix permissions

```
mv /tmp/wp-content/* /var/www/html/wp-content/
```

```
chown -R ec2-user:apache /var/www/

```

# STAGE 4D - Test that the wordpress app can load the media

run the following command to reboot the EC2 wordpress instance
```
reboot
```

Once it restarts, ensure that you can still load the wordpress blog which is now loading the media from EFS.  

# STAGE 4E - Update the launch template with the config to automate the EFS part

Next you will update the launch template so that it automatically mounts the EFS file system during its provisioning process. This means that in the next stage, when you add autoscaling, all instances will have access to the same media store ...allowing the platform to scale.

Go to the EC2 console https://console.aws.amazon.com/ec2/v2/home?region=us-east-1#Home:  
Click `Launch Templates`  
Check the box next to the `Wordpress` launch template, click `Actions` and click `Modify Template (Create New Version)`  
for `Template version description` enter `App only, uses EFS filesystem defined in /Wordpress/EFSFSID`  
Scroll to the bottom and expand `Advanced Details`  
Scroll to the bottom and find `User Data` expand the entry box as much as possible.  

After `#!/bin/bash -xe` position cursor at the end & press enter twice to add new lines
paste in this

```
EFSFSID=$(aws ssm get-parameters --region us-east-1 --names /Wordpress/EFSFSID --query Parameters[0].Value)
EFSFSID=`echo $EFSFSID | sed -e 's/^"//' -e 's/"$//'`

```

Find the line which says `dnf install wget php-mysqlnd httpd php-fpm php-mysqli php-json php php-devel stress -y`
after `stress` add a space and paste in `amazon-efs-utils`  
it should now look like `dnf install wget php-mysqlnd httpd php-fpm php-mysqli php-json php php-devel stress amazon-efs-utils -y`  

locate `systemctl start httpd` position cursor at the end & press enter twice to add new lines  

paste in the following

```
mkdir -p /var/www/html/wp-content
chown -R ec2-user:apache /var/www/
echo -e "$EFSFSID:/ /var/www/html/wp-content efs _netdev,tls,iam 0 0" >> /etc/fstab
mount -a -t efs defaults
```

Scroll down and click `Create template version`  
Click `View Launch Template`  
Select the template again (dont click)
Click `Actions` and select `Set Default Version`  
Under `Template version` select `3`  
Click `Set as default version`  

# STAGE 5A - Create the load balancer

Move to the EC2 console https://console.aws.amazon.com/ec2/v2/home?region=us-east-1#Home:  
Click `Load Balancers` under `Load Balancing`  
Click `Create Load Balancer`  
Click `Create` under `Application Load Balancer`  
Under name enter `WORDPRESSALB`  
Ensure `internet-facing` is selected  
ensure `ipv4` selected for `IP Address type`  

Under `Network Mapping` select `VPC` in the `VPC Dropdown`  
Check the boxes next to `us-east-1a` `us-east-1b` and `us-east-1c`  
Select `sn-pub-A`, `sn-pub-B` and `sn-pub-C` for each.  

Scroll down and under `Security Groups` remove `default` and select the `VPC-SGLoadBalancer` from the dropdown.  

Under `Listener and Routing` 
Ensure `Protocol` is set to `HTTP` and `Port` is set to `80`.  
Click `Create target group` which will open a new tab  
for `Target Type` choose `Instances` 
for Target group name choose `WORDPRESSALBTG`  
For `Protocol` choose `HTTP`  
For `Port` choose `80`  
Make sure the `VPC` is set to `VPC`  
Check that `Protocol Version` is set to `HTTP1`  

Under `Health checks`
for `Protocol` choose `HTTP`
and for `Path` choose `/`  
Click `Next`  
We wont register any right now so click `Create target Group`  
Go back to the previous tab where you are creating the Load Balancer
Click the `Refresh Icon` and select the `WORDPRESSALBTG` item in the dropdown.  
Scroll down to the bottom and click `Create load balancer`  
Click `View Load Balancer` and select the load balancer you are creating.  
Scroll down and copy the `DNS Name` into your clipboard  

# STAGE 5B - Create a new Parameter store value with the ELB DNS name

Move to the systems manager console https://console.aws.amazon.com/systems-manager/home?region=us-east-1#  
Click `Parameter Store`  
Click `Create Parameter`  
Under `Name` enter `/Wordpress/ALBDNSNAME` 
Under `Description` enter `DNS Name of the Application Load Balancer for wordpress`  
for `Tier` set `Standard`  
For `Type` set `String`  
for `Data Type` set `text`  
for `Value` set the DNS name of the load balancer you copied into your clipboard
Click `Create Parameter` 

# STAGE 5C - Update the Launch template to wordpress is updated with the ELB DNS as its home

Go to the EC2 console https://console.aws.amazon.com/ec2/v2/home?region=us-east-1#Home:  
CLick `Launch Templates`  
Check the box next to the `Wordpress` launch template, click `Actions` and click `Modify Template (Create New Version)`  
for `Template version description` enter `App only, uses EFS filesystem defined in /Wordpress/EFSFSID, ALB home added to WP Database`  
Scroll to the bottom and expand `Advanced Details`  
Scroll to the bottom and find `User Data` expand the entry box as much as possible.  

After `#!/bin/bash -xe` position cursor at the end & press enter twice to add new lines
paste in this

```
ALBDNSNAME=$(aws ssm get-parameters --region us-east-1 --names /Wordpress/ALBDNSNAME --query Parameters[0].Value)
ALBDNSNAME=`echo $ALBDNSNAME | sed -e 's/^"//' -e 's/"$//'`

```

Move all the way to the bottom of the `User Data` and paste in this block

```
cat >> /home/ec2-user/update_wp_ip.sh<< 'EOF'
#!/bin/bash
source <(php -r 'require("/var/www/html/wp-config.php"); echo("DB_NAME=".DB_NAME."; DB_USER=".DB_USER."; DB_PASSWORD=".DB_PASSWORD."; DB_HOST=".DB_HOST); ')
SQL_COMMAND="mysql -u $DB_USER -h $DB_HOST -p$DB_PASSWORD $DB_NAME -e"
OLD_URL=$(mysql -u $DB_USER -h $DB_HOST -p$DB_PASSWORD $DB_NAME -e 'select option_value from wp_options where option_id = 1;' | grep http)

ALBDNSNAME=$(aws ssm get-parameters --region us-east-1 --names /Wordpress/ALBDNSNAME --query Parameters[0].Value)
ALBDNSNAME=`echo $ALBDNSNAME | sed -e 's/^"//' -e 's/"$//'`

$SQL_COMMAND "UPDATE wp_options SET option_value = replace(option_value, '$OLD_URL', 'http://$ALBDNSNAME') WHERE option_name = 'home' OR option_name = 'siteurl';"
$SQL_COMMAND "UPDATE wp_posts SET guid = replace(guid, '$OLD_URL','http://$ALBDNSNAME');"
$SQL_COMMAND "UPDATE wp_posts SET post_content = replace(post_content, '$OLD_URL', 'http://$ALBDNSNAME');"
$SQL_COMMAND "UPDATE wp_postmeta SET meta_value = replace(meta_value,'$OLD_URL','http://$ALBDNSNAME');"
EOF

chmod 755 /home/ec2-user/update_wp_ip.sh
echo "/home/ec2-user/update_wp_ip.sh" >> /etc/rc.local
/home/ec2-user/update_wp_ip.sh
```

Scroll down and click `Create template version`  
Click `View Launch Template`  
Select the template again (dont click)
Click `Actions` and select `Set Default Version`  
Under `Template version` select `4`  
Click `Set as default version`  


# STAGE 5D - Create an auto scaling group (no scaling yet)

Move to the EC2 console  
under `Auto Scaling`  
click `Auto Scaling Groups`  
Click `Create an Auto Scaling Group`  
For `Auto Scaling group name` enter `WORDPRESSASG`  
Under `Launch Template` select `Wordpress`  
Under `Version` select `Latest`  
Scroll down and click `Next`  
For `Network` `VPC` select `VPC`  
For `Subnets` select `sn-Pub-A`, `sn-pub-B` and `sn-pub-C`  
Click `next`  

# STAGE 5E - Integrate ASG and ALB

Its here where we integrate the ASG with the Load Balancer. Load balancers actually work (for EC2) with static instance registrations. What ASG does, it link with a target group, any instances provisioned by the ASG are added to the target group, anything terminated is removed.  

Check the `Attach to an existing Load balancer` box  
Ensure `Choose from your load balancer target groups` is selected.  
for `existing load balancer targer groups` select `WORDPRESSALBTG`  
don't make any changes to `VPC Lattice integration options`  

Under `health Checks` check `Turn on Elastic Load Balancing health checks`  
Under `Additional Settings` check `Enable group metrics collection within CloudWatch`  


Scroll down and click `Next`  

For now leave `Desired` `Mininum` and `Maximum` at `1`   
For `Scaling policies - optional` leave it on `None`  
Make sure `Enable instance scale-in protection` is **NOT** checked  
Click `Next`  
We wont be adding notifications so click `Next` Again  
Click `Add Tag`  
for `Key` enter `Name` and for `Value` enter `Wordpress-ASG` 
make sure `Tag New instances` is checked
Click `Next` 
Click `Create Auto Scaling Group`  

Right click on instances and open in a new tab  
Right click Wordpress-LT, `Terminate Instance` and confirm.   
This removes the old manually created wordpress instance
Click `Refresh` and you should see a new instance being created... `Wordpress-ASG` this is the one created automatically by the ASG using the launch template - this is because the desired capacity is set to `1` and we currently have `0`  


# STAGE 5F - Add scaling

Move to the AWS Console https://console.aws.amazon.com/ec2/autoscaling/home?region=us-east-1#AutoScalingGroups:  
Click `Auto Scaling Groups`  
Click the `WORDPRESSASG` ASG  
Click the `Automatic SCaling` Tab  

We're going to add two policies, scale in and scale out.

## SCALEOUT when CPU usage on average is above 40%

Click `Create dynamic Scaling Policy`  
For policy `type` select `Simple scaling`  
for `Scaling Policy name` enter `HIGHCPU`  
Click `Create a CloudWatch Alarm`  
Click `Select Metric`  
Click `EC2`  
Click `By Auto Scaling Group` (if this doesn't show in your list, wait a while and refresh)  
Check `WORDPRESSASG CPU Utilization`  (if this doesn't show in your list, wait a while and refresh)  
Click `Select Metric`  
Scroll Down... select `Threashhold style` `static`, select `Greater` and enter `40` in the `than` box and click `Next`
Click `Remove` next to notification if you see anything listed here
Click `Next`
Enter `WordpressHIGHCPU` in `Alarm Name`  
Click `Next`  
Click `Create Alarm`  
Go back to the AutoScalingGroup tab and click the `Refresh SYmbol` next to Cloudwatch Alarm  
Click the dropdown and select `WordpressHIGHCPU`  
For `Take the action` choose `Add` `1` Capacity units  
Click `Create`


## SCALEIN when CPU usage on average ie below 40%

Click `Create Dynamic Scaling Policy`  
For policy `type` select `Simple scaling`  
for `Scaling Policy name` enter `LOWCPU`  
Click `Create a CloudWatch Alarm`  
Click `Select Metric`  
Click `EC2`  
Click `By Auto Scaling Group`
Check `WORDPRESSASG CPU Utilization`  
Click `Select Metric`  
Scroll Down... select `Static`, `Lower` and enter `40` in the `than` box and click `next`
Click `Remove` next to notification
Click `Next`
Enter `WordpressLOWCPU` in `Alarm Name`  
Click `Next`  
Click `Create Alarm`  
Go back to the AutoScalingGroup tab and click the `Refresh Symbol` next to Cloudwatch Alarm  
Click the dropdown and select `WordpressLOWCPU`  
For `Take the action` choose `Remove` `1` Capacity units  
Click `Create`

## ADJUST ASG Values

Click `Details Tab`  
Under `Group Details` click `Edit`  
Set `Desired 1`, Minimum `1` and Maximum `3`  
Click `Update`  

# STAGE 5G - Test Scaling & Self Healing

Open Auto Scaling Groups in a new tab https://console.aws.amazon.com/ec2autoscaling/home?region=us-east-1#/details  
Open that Auto scaling group in that tab and click on `Activity` tab
Go to running instances in the EC2 Console https://console.aws.amazon.com/ec2/v2/home?region=us-east-1#Instances:sort=desc:tag:Name  in a new tab  

Simulate some load on the wordpress instance  

Select the/one running `Wordpress-ASG` instance, right click, `Connect`, Select `Session Manager` and click `Connect`  
type `sudo bash` and press enter   
type `cd` and press enter  
type `clear` and press enter  

run `stress -c 2 -v -t 3000`

this stresses the CPU on the instance, while running go to the ASG tag, and refresh the activities tab. it might take a few minutes, but the ASG will detect high CPU load and begin provisioning a new EC2 instance. 
if you want to see the monitoring stats, change to the monitoring tag on the ASG Console
Check `enable` next to Auto Scaling Group Metrics Collection

At some point another instance will be added. This will be auto built based on the launch template, connect to the RDS instance and EFS file system and add another instance of capacity to the platform.
Try terminating one of the EC2 instances ...
Watch what happens in the activity tab of the auto scaling group console.
This is an example of self-healing, a new instance is provisioned to take the old ones place.

# STAGE 6A - Delete Load Balancer & Target Groups

First, got to the EC2 console and click on `Load Balancers` under `Load Balancing` 
Select the `WORDPRESSALB`  click `Actions` and select `Delete` and then click `yes, Delete`  
Click `Target Groups`, select the target group you created earlier and click `Actions` and then `Delete`, then click `Yes, Delete`  

# STAGE 6B - Delete Auto Scaling Group

Click `Auto Scaling Groups` under `Auto Scaling`  
Select the `WORDPRESSASG` you created earlier and click `Delete`, type `delete` and click `delete` to confirm.  
this will take a while because the EC2 instances will be terminated as part of this.  

# STAGE 6C - Delete the EFS File system

Click `Services` and type `EFS` then move to the EFS console.  
Select the `-WORDPRESS-CONTENT` File system, and click `Delete`. Confirm by typing in the file system ID and then clicking `Confirm`  

# STAGE 6D - Delete RDS

Click on `Services`, type `RDS` and move to the RDS Console.  
Click `Databases`  
Select `wordpress`, click `Actions` and `Delete`  
Uncheck `Create Final Snapshot`, Uncheck `Retain Automated Backups`, check the `Acknowledge` box, type `delete me` and click `Delete`  

# STAGE 6E - Check Progress

Check that EFS and all EC2 instances, and ASG have finished deleting - hold here until they have both been removed.

# STAGE 6F - Delete Launch Template

Move to EC2 Console, click on `Launch Templates`  
Select the `Wordpress` Template, Click `Actions` and then `Delete Template`. Type `Delete` and Click `Delete`  

# Stage 6G - Paramater Store
Move to the Parameter Store Console
Delete all of the parameters that you created while doing this demo, anything `/Wordpress/*`  

# STAGE 6H - Wait for RDS

Move to the RDS console, click on `Databases` & wait for all RDS instances to be removed before continuing.  

# STAGE 6I - Delete DB Subnet Group

in the RDS console click `Subnet Groups`, select `wordpressrdssubnetgroup`, clikck `Delete` and `Delete` again.
 
# STAGE 6J - Cloud Formation

Move to the cloudformation console.  
Select the `VPC` stack, click `Delete` and confirm by clicking on `Delete Stack`

# STAGE 6 - FINISH

Credits: acantril

