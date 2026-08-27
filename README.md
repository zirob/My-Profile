# My Profile

Personal portfolio website showcasing my work experience, projects, technical skills, and hobbies.

## Project Goal

The goal of this project is to build my personal portfolio while gaining hands-on experience with AWS, Linux, networking, Git, and DevOps practices.

The idea is building and configuring the infrastructure step by step.

## AWS Infrastructure and Deploytment

### Amazon EC2

* Created an Amazon EC2 instance to host the web server.
* Configured SSH access to the EC2 instance.
* Created and configured an EC2 key pair for authentication.
* Secured the private `.pem` key with appropriate file permissions.
* Connected to the instance using SSH.

Example:

```bash
chmod 400 my-web-server-key.pem
ssh -i my-web-server-key.pem ec2-user@<EC2-IP>
```

### Elastic IP

* Allocated an Elastic IP address.
* Associated the Elastic IP with the EC2 instance.
* Configured my local SSH client to use the Elastic IP.

## EC2 Access with AWS Systems Manager Session Manager

Initially, I accessed the EC2 instance directly through SSH using port 22 and a Security Group rule restricted to my public IP address.

Because my ISP assigns a dynamic public IP, SSH access stopped working whenever my public IP changed.

To avoid depending on a changing public IP and to remove inbound SSH access, I migrated to AWS Systems Manager Session Manager.

### Access Flow

```
Mac
 ↓
AWS CLI
 ↓
aws login
 ↓
AWS Systems Manager Session Manager
 ↓
EC2 instance
 ↓
ssm-user
```

### Configuration
* Attached an IAM role to the EC2 instance with the AmazonSSMManagedInstanceCore policy.
* Verified that Session Manager worked through the AWS Console.
* Installed the Session Manager plugin on macOS.
* Updated AWS CLI to a version that supports aws login.
* Authenticated the AWS CLI using temporary credentials:
```
aws login
```
* Verified the authenticated IAM identity:
```
aws sts get-caller-identity
```
* Connected to the EC2 instance:
```
aws ssm start-session \
  --target <INSTANCE_ID> \
  --region us-east-2
```

### Nginx

* boot of Nginx in EC2 inctance. OK
* Checking of Access by http. OK

## Security Improvement

After confirming Session Manager access, I removed the inbound SSH rule for port 22 from the EC2 Security Group.

The instance can now be administered without exposing SSH directly to the internet.

## Current Progress

The AWS infrastructure is currently under development.

Next steps will include configuring the web server, deploying the portfolio website, improving security, and adding additional AWS services as the project evolves.

## Technologies

* AWS
* Amazon EC2
* Linux
* SSH
* Git
* GitHub
* HTML
* CSS
* JavaScript

## Development

