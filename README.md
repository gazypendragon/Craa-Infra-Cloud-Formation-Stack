# Craa-Infra-Cloud-Formation-Stack

AWS CloudFormation Template


This AWS CloudFormation template is designed to create a Virtual Private Cloud (VPC) along with associated resources, including Network Address Translation (NAT) gateways, security groups, and a Relational Database Service (RDS) instance. The infrastructure is set up to support web servers in private subnets, a public subnet for an Application Load Balancer (ALB), and a private subnet for the RDS database.
Parameters
VPC Parameters

    VpcCIDR
        Description: Enter the IP range (CIDR notation) for the VPC.
        Default: 10.0.0.0/16

    PublicSubnet1CIDR, PublicSubnet2CIDR, PrivateSubnet1CIDR, PrivateSubnet2CIDR, PrivateSubnet3CIDR, PrivateSubnet4CIDR
        Description: Enter the IP ranges (CIDR notation) for the respective subnets.
        Defaults are provided for each.

    SSHLocation
        Description: The IP address range that can be used to access the web server using SSH.
        Default: 72.xxx.xxx.xx/32
        Constraints: Must be a valid IP CIDR range.

RDS Parameters

    DatabaseInstanceIdentifier
        Description: RDS instance identifier.
        Default: dev-rds-db

    DatabaseName
        Description: MySQL database name.
        Default: applicationdb

    DatabaseUser
        Description: Username for MySQL database access.
        Default: craaappuser

    DatabasePassword
        Description: Password for MySQL database access.
        Default: craaappuser123

    DatabaseBackupRetentionPeriod
        Description: Number of days for which automatic DB snapshots are retained.
        Default: 0 (no retention)
        Constraints: Must be between 1 and 35 days.

    DatabaseAllocatedStorage
        Description: Size of the database (in GB).
        Default: 5
        Constraints: Must be between 5 and 1024 GB.

    DatabaseInstanceClass
        Description: The database instance type.
        Default: db.t2.micro
        Constraints: Must be a valid database instance type.

    MultiAZDatabase
        Description: Create a Multi-AZ MySQL Amazon RDS database instance.
        Default: false

Conditions

    CreateReadReplica
        Creates a read replica if MultiAZDatabase is set to true.

Resources
VPC Resources

    VPC
        Type: AWS::EC2::VPC
        Creates the Virtual Private Cloud.

    InternetGateway
        Type: AWS::EC2::InternetGateway
        Creates the Internet Gateway for the VPC.

    InternetGatewayAttachment
        Type: AWS::EC2::VPCGatewayAttachment
        Attaches the Internet Gateway to the VPC.

    PublicSubnetAZ1, PublicSubnetAZ2, PrivateAppSubnetAZ1, PrivateAppSubnetAZ2, PrivateDataSubnetAZ1, PrivateDataSubnetAZ2
        Type: AWS::EC2::Subnet
        Creates subnets for various purposes.

NAT Gateway Resources

    NatGatewayAZ1EIP, NatGatewayAZ1
        Type: AWS::EC2::EIP, AWS::EC2::NatGateway
        Creates a NAT gateway for the public subnets.

    PublicRouteTable, PublicRoute, PublicSubnetAZ1RouteTableAssociation, PublicSubnetAZ2RouteTableAssociation
        Type: AWS::EC2::RouteTable, AWS::EC2::Route, AWS::EC2::SubnetRouteTableAssociation
        Configures routing for the public subnets.

    PrivateRouteTableAZ1, PrivateRouteAZ1, PrivateAppSubnetAZ1RouteTableAssociation, PrivateDataSubnetAZ1RouteTableAssociation, PrivateAppSubnetAZ2RouteTableAssociation, PrivateDataSubnetAZ2RouteTableAssociation
        Type: AWS::EC2::RouteTable, AWS::EC2::Route, AWS::EC2::SubnetRouteTableAssociation
        Configures routing for the private subnets.

EC2 Instance Connect Endpoint Resources

    EC2InstanceConnectEndpoint
        Type: AWS::EC2::InstanceConnectEndpoint
        Creates an EC2 Instance Connect Endpoint.

Security Groups Resources

    EICESecurityGroup, SSHSecurityGroup, DataBaseSecurityGroup, ALBSecurityGroup, WebServerSecurityGroup
        Type: AWS::EC2::SecurityGroup
        Creates security groups for various components.

RDS Resources

    DatabaseSubnetGroup
        Type: AWS::RDS::DBSubnetGroup
        Creates a subnet group for the RDS database.

    DatabaseInstance
        Type: AWS::RDS::DBInstance
        Creates the RDS database instance.

Notes

    The template is structured to support a scalable and secure VPC architecture with public and private subnets.