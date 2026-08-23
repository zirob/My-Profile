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

### SSH Configuration

Configured `~/.ssh/config` on my local machine to simplify connections to the EC2 instance.

Instead of:

```bash
ssh -i ~/.ssh/my-web-server-key.pem ec2-user@<EC2-IP>
```

I can connect using:

```bash
ssh my-web-server
```
### Nginx

* boot of Nginx in EC2 inctance. OK
* Checking of Access by http. OK

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

