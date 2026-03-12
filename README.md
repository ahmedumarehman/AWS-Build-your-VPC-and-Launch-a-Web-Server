# AWS Lab 2 Documentation

## Build Your VPC and Launch a Web Server

## Introduction

In this lab, I created my own virtual network using **Amazon Virtual
Private Cloud (VPC)** and launched a **web server using Amazon EC2**
inside that network.

The purpose of this lab was to understand how cloud networking works in
AWS and how different components like **subnets, route tables, gateways,
and security groups** work together to allow secure communication
between resources and the internet.

By the end of this lab, I successfully: - Created a VPC - Created public
and private subnets - Configured route tables - Configured an Internet
Gateway and NAT Gateway - Created a security group - Launched an EC2
instance configured as a web server

------------------------------------------------------------------------

# Step 1: Creating the VPC

First, I opened the **AWS VPC Console** and created a new VPC using the
**"VPC and more"** option.

### VPC Configuration

  Setting              Value
  -------------------- -------------
  VPC Name             lab-vpc
  IPv4 CIDR            10.0.0.0/16
  Availability Zones   1
  Public Subnets       1
  Private Subnets      1

### IP Range Explanation

The CIDR block `10.0.0.0/16` provides **65,536 IP addresses** inside the
VPC.

Range:

10.0.0.0 → 10.0.255.255

This IP range is **private** and used only inside the AWS network.

------------------------------------------------------------------------

# Step 2: Creating the First Subnets

Two subnets were created inside the VPC.

## Public Subnet

  Property   Value
  ---------- -------------------------------
  Name       lab-subnet-public1-us-east-1a
  CIDR       10.0.0.0/24

IP Range:

10.0.0.0 → 10.0.0.255

Instances inside this subnet can communicate with the **internet
directly**.

------------------------------------------------------------------------

## Private Subnet

  Property   Value
  ---------- --------------------------------
  Name       lab-subnet-private1-us-east-1a
  CIDR       10.0.1.0/24

IP Range:

10.0.1.0 → 10.0.1.255

Instances in private subnets **cannot be accessed directly from the
internet**.

Private subnets are typically used for: - Databases - Internal
services - Backend systems

------------------------------------------------------------------------

# Internet Gateway

An **Internet Gateway (IGW)** was automatically created and attached to
the VPC.

Purpose:

Allow communication between the **public subnet and the internet**.

Traffic flow:

EC2 Instance → Internet Gateway → Internet

------------------------------------------------------------------------

# NAT Gateway

A **NAT Gateway** was also created.

Purpose:

Allow **private subnet instances to access the internet without exposing
them publicly**.

Example flow:

Private EC2 → NAT Gateway → Internet

Users on the internet **cannot directly access private instances**.

------------------------------------------------------------------------

# Step 3: Creating Additional Subnets (High Availability)

To improve **availability and fault tolerance**, I created additional
subnets in another Availability Zone.

## Second Public Subnet

  Property   Value
  ---------- --------------------
  Name       lab-subnet-public2
  CIDR       10.0.2.0/24

IP Range:

10.0.2.0 → 10.0.2.255

------------------------------------------------------------------------

## Second Private Subnet

  Property   Value
  ---------- ---------------------
  Name       lab-subnet-private2
  CIDR       10.0.3.0/24

IP Range:

10.0.3.0 → 10.0.3.255

------------------------------------------------------------------------

# Step 4: Route Tables

Route tables control **how traffic moves inside the VPC**.

### Public Route Table

Route:

Destination: 0.0.0.0/0\
Target: Internet Gateway

Meaning:

All internet traffic from public subnets goes to the **Internet
Gateway**.

------------------------------------------------------------------------

### Private Route Table

Route:

Destination: 0.0.0.0/0\
Target: NAT Gateway

Meaning:

Private subnet instances can access the internet through the **NAT
Gateway**.

------------------------------------------------------------------------

# Step 5: Creating a Security Group

A **Security Group** acts as a **virtual firewall**.

### Security Group Details

  Setting       Value
  ------------- --------------------
  Name          Web Security Group
  Description   Enable HTTP access
  VPC           lab-vpc

### Inbound Rule

  Type   Port   Source
  ------ ------ ----------------------
  HTTP   80     Anywhere (0.0.0.0/0)

This allows users from anywhere on the internet to access the web
server.

------------------------------------------------------------------------

# Step 6: Launching the EC2 Web Server

Next, I launched an **EC2 instance**.

### Instance Configuration

  Setting         Value
  --------------- -------------------
  Name            Web Server 1
  AMI             Amazon Linux 2023
  Instance Type   t2.micro
  Key Pair        vockey

------------------------------------------------------------------------

## Network Configuration

  Setting          Value
  ---------------- --------------------
  VPC              lab-vpc
  Subnet           lab-subnet-public2
  Public IP        Enabled
  Security Group   Web Security Group

This allows the server to be accessible from the internet.

------------------------------------------------------------------------

# Step 7: User Data Script

A **User Data Script** was added so the server automatically installs
the web application when it launches.

Main tasks performed by the script:

1.  Install Apache Web Server
2.  Install PHP
3.  Install MariaDB
4.  Download lab web application
5.  Start Apache server

Example command:

dnf install -y httpd

Start the server:

service httpd start

------------------------------------------------------------------------

# Step 8: Accessing the Web Server

After the EC2 instance finished launching:

1.  I copied the **Public IPv4 DNS**
2.  Opened it in a web browser
3.  The webpage displayed the AWS logo and instance metadata

This confirmed that the **web server was successfully running**.

------------------------------------------------------------------------

# Final Architecture

The final infrastructure contained:

-   1 VPC
-   2 Availability Zones
-   2 Public Subnets
-   2 Private Subnets
-   Internet Gateway
-   NAT Gateway
-   Route Tables
-   Security Group
-   EC2 Web Server

Architecture flow:

Internet\
↓\
Internet Gateway\
↓\
Public Subnet\
↓\
EC2 Web Server\
↓\
Private Subnets\
↓\
NAT Gateway

------------------------------------------------------------------------

# Key Concepts Learned

### VPC

A private network inside AWS where we control IP addressing and routing.

### Public vs Private Subnets

Public subnets allow internet access.\
Private subnets protect sensitive resources.

### Route Tables

Determine how traffic flows between subnets and external networks.

### Security Groups

Act as firewalls that control inbound and outbound traffic.

### NAT Gateway

Allows private servers to access the internet securely.

### EC2

Virtual servers that run applications in the cloud.

------------------------------------------------------------------------

# Real-World Architecture Example

Typical production architecture:

Public Subnet - Load Balancer - Web Servers

Private Subnet - Application Servers - Databases

This design improves:

-   Security
-   Scalability
-   High availability
