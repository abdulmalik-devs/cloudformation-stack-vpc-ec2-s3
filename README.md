# CloudFormation Stack - S3 Bucket, VPC, EC2 Instance with Jenkins, and Security Group

## Solution Overview

This CloudFormation stack creates an AWS infrastructure that includes an S3 bucket, VPC, an EC2 instance bootstrapped with Jenkins, and a security group. The stack provides an easy and automated way to set up a development environment for continuous integration and delivery.

## Prerequisites

Before launching the CloudFormation stack, ensure that you have the following:

- An AWS account with appropriate permissions to create the required resources.
- Basic knowledge of AWS services, including VPC, EC2, S3, and CloudFormation.
- Familiarity with YAML syntax.

## Stack Configuration

The CloudFormation stack accept input from the user using the following parameters:

- **BucketName**: The name of the S3 bucket to be created. It must be globally unique.
- **EncryptionType**: The encryption type for the S3 bucket. Options are "SSE-S3" or "KMS" (default: SSE-S3).
- **KMSKeyID**: The KMS Key ID for the S3 bucket (only used if EncryptionType is "KMS").
- **InstanceType**: The EC2 instance type for the Jenkins instance (default: t2.micro).
- **InstanceName**: The name for the EC2 instance (default: JenkinsInstance).
- **KeyName**: The key pair for SSH access.

## Resources

The CloudFormation stack creates the following resources:

### S3 Bucket

An S3 bucket is created with the specified name (`BucketName` parameter) and encryption configuration based on the `EncryptionType` parameter.

### VPC

A Virtual Private Cloud (VPC) is created with a CIDR block of `10.0.0.0/16`. The VPC enables DNS support and hostnames. **Internet Gateway** - An internet gateway is created, which allows the VPC to communicate with the internet. **VPC Gateway Attachment** - The VPC gateway attachment associates the VPC with the internet gateway. **Public Subnet** - A public subnet is created within the VPC with a CIDR block of `10.0.0.0/24`. The subnet is associated with the first availability zone in the specified AWS region. **Private Subnet** - A private subnet is created within the VPC with a CIDR block of `10.0.1.0/24`. The subnet is associated with the first availability zone in the specified AWS region. **Public Route Table** - A public route table is created and associated with the VPC. **Public Route** - A route is added to the public route table, allowing traffic to the internet gateway. **Public Subnet Route Table Association** - The public subnet is associated with the public route table. **Private Route Table** - A private route table is created and associated with the VPC. **Private Subnet Route Table Association** - The private subnet is associated with the private route table.

### EC2 Instance (Jenkins)

An EC2 instance is launched with the specified instance type (`InstanceType` parameter), AMI ID based on the AWS region, key pair for SSH access (`KeyName` parameter), and Jenkins user data. The user data script installs OpenJDK 11 and Jenkins.

### Security Group

A security group is created for the EC2 instance to allow SSH access (port 22) and Jenkins access (port 8080) from anywhere (0.0.0.0/0).

## Outputs

The CloudFormation stack provides the following outputs:

- **InstanceId**: The ID of the created EC2 instance.
- **PublicDns**: The public DNS of the created EC2 instance.
- **PublicIP**: The public IP of the created EC2 instance.
- **SecurityGroupId**: The ID of the created security group.
- **S3Bucket**: The name of the S3 bucket created using this template.

## Launching the Stack

- **To Validate your Template Stack**

```shell
aws cloudformation validate-template --template-body file://s3.yaml
```

- **To launch the CloudFormation stack:**

Make sure you have the AWS CLI installed and configured with valid credentials before running this command. Replace the parameter values (ParameterValue) with the desired values for your stack.

To create a CloudFormation stack using the AWS CLI, run the following command:

```shell
aws cloudformation create-stack \
--stack-name DevSec \
--template-body file://ec2-s3.yaml \
--parameters ParameterKey=BucketName,ParameterValue=proton-buck \
ParameterKey=EncryptionType,ParameterValue=SSE-S3 \
ParameterKey=KMSKeyID,ParameterValue=cloudsecID \
ParameterKey=InstanceType,ParameterValue=t2.micro \
ParameterKey=InstanceName,ParameterValue=CloudSec-Instance \
ParameterKey=KeyName,ParameterValue=word
``` 

**To execute the command, follow these steps:**

1. Open a terminal or command prompt.
2. Ensure that you have the AWS CLI installed. If not, you can install it by following the AWS CLI Installation Guide.
3. Configure the AWS CLI with your AWS credentials using the aws configure command. If you haven't done this before, refer to the AWS CLI    Configuration Guide.
4. Copy the command shown above and paste it into your terminal or command prompt.
5. Modify the parameter values (ParameterValue) according to your requirements. For example, you can change the BucketName, EncryptionType, KMSKeyID, InstanceType, InstanceName, and KeyName.
6. Press Enter to execute the command and create the CloudFormation stack.
7. Ensure that you have proper permissions to create CloudFormation stacks and access the specified AWS resources.

## Cleaning Up

To delete the CloudFormation stack and associated resources:

```shell
aws cloudformation delete-stack --stack-name DevSec
```

## Conclusion

This CloudFormation stack simplifies the setup of an S3 bucket, an EC2 instance with Jenkins, and a security group, providing an automated and reproducible way to create the required infrastructure for continuous integration and delivery. Feel free to customize the parameters and resources according to your specific needs.
