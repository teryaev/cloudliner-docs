# Cloudliner docs

## Table of Contents

- [Introduction](#introduction)
- [Why do I need Cloudliner](#why-do-i-need-cloudliner)
- [How to connect AWS account to Cloudliner](#how-to-connect-aws-account-to-cloudliner)
- [Cloudliner environment](#cloudliner-environment)
- [FAQ](#faq)
  - [My environment status is 'Failed'. What should I do?](#my-environment-status-is-failed-what-should-i-do)
  - [How to connect to EC2 instance?](#how-to-connect-to-ec2-instance)
  - [How to connect to my database or Redis instance?](#how-to-connect-to-my-database-or-redis-instance)
  - [How to deploy and expose my web-service on EC2 instance?](#how-to-deploy-and-expose-my-web-service-on-ec2-instance)
  - [How to deploy my static website / frontend application?](#how-to-deploy-my-static-website--frontend-application)
  - [Where do I get TLS/SSL certificate ARN?](#where-do-i-get-tlsssl-certificate-arn)
  - [How to configure custom domain for static website (Cloudfront)?](#how-to-configure-custom-domain-for-static-website-cloudfront)
  - [How to configure custom domain for web-service running on EC2?](#how-to-configure-custom-domain-for-web-service-running-on-ec2)
  - [Can Cloudliner create DNS records for me?](#can-cloudliner-create-dns-records-for-me)

## Introduction
Cloudliner is a tool that manages your AWS infrastructure for you. Cloudliner bootstraps your infrastructure baseline configuration for you.


## Why do I need Cloudliner?
Cloudliner would be useful for you if you don't want to spend hours and days learning AWS to get things working. Cloudliner takes minutes creating a production-ready environment that is built following infrastructure and security best-practices. Even if you are an experienced engineer, Cloudliner would save you some time bootstrapping the baseline configuration for you.


## How to connect AWS account to Cloudliner

In order to start with Cloudliner you need to have an AWS account. Cloudliner uses an IAM role in order to access and configure your AWS account. You need to create an IAM role in your AWS account and allow Cloudliner to assume it. When you create an account in Cloudliner, there's a link in the dialog that leads you to AWS console 'Create IAM role' dialog. It prepopulates the configuration values for you. You only need to set the role name and click 'Create'. Then copy the role ARN (AWS resource name) and paste it in Cloudliner 'Create account' dialog. You can delete the role any time and Cloudliner won't be able to access your AWS account.


## Cloudliner environment

Environment is a Cloudliner entity that represents a set of AWS resources to be created in your AWS account. Some of them are included by default (VPC, subnets, route tables, IAM resources, etc), some of them are optional and it's your call whether Cloudliner creates them or not (EC2 instance, RDS, Redis, etc). When you create or update an environment, Cloudliner takes a few minutes to apply the desired configuration to your AWS account. The waiting time depends on resources you need. Shouldn't take more than 15 minutes though.

## FAQ

### My environment status is 'Failed'. What should I do?
We are notified if any environment status goes 'Failed' and manually looking into an issue. You'll be notified when it's resolved. ETA is up to 24 hours.


### How to connect to EC2 instance?
Your EC2 instance doesn't expose any ports to the Internet. It's a security measure. Cloudliner deploys an EC2 instance with AWS SSM Session Manager installed which is a secure way to access your machine. [How to connect](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/session-manager-to-linux.html){:target="_blank"}


### How to connect to my database or Redis instance?
Your RDS/Elasticache/DocumentDB resources live in a private subnet. They cannot be access from the Internet. However, you can access them from the EC2 instance created by Cloudliner. First you need to get into your EC2 instance, [How to connect to EC2](#how-to-connect-to-ec2-instance). Run the following command on EC2 instance to retrieve the command that pulls your services credentials and put them into your environment variables (replace cloudliner with your environment name if differs):
```shell
chamber env cloudliner
```
if you want to apply the command and set the environment variables, run this
```
$(chamber env cloudliner)
```


### How to deploy and expose my web-service on EC2 instance?
Connect to your EC2 instance ([How to connect to EC2](#how-to-connect-to-ec2-instance)), run your service there. Note that Cloudliner configures your instance to expose port 80 to an Application Load Balancer, that is also automatically deployed by Cloudliner. Make sure that your service listens to port 80. You can get your Application Load Balancer auto-generated URL in AWS console (the link is available on Cloudliner dashboard).


### How to deploy my static website / frontend application?
Cloudliner uses S3 for static website / FE application hosting. Cloudliner create an S3 bucket for you, just upload your content there. Note that there should be an index.html file. S3 website only supports HTTP protocol. We recommend using Cloudfront along with S3. Cloudfront does support HTTPS and delivers your traffic faster. Cloudliner also provides you with an option to automatically create a CDN (Cloudfront) distribution for your S3 website. You can get an auto-generated URL to your S3 bucket or CDN distriubtion in AWS console (the link is available on Cloudliner dashboard).


### Where do I get TLS/SSL certificate ARN?
Cloudliner allows you to use your custom domains for your website, therefore, you need to provide Cloudliner with an SSL certificate to make connection to your resources, which are running with Cloudliner, secure. Note that you are expected to already own the DNS domain. First, you need to add a certificate to your AWS account. **We recommend using a wildcard certificate so that you can use it for both S3 and EC2 based endpoints.** In order to add the certificate to the accounr, you can either [Import a certificate](https://docs.aws.amazon.com/acm/latest/userguide/import-certificate.html){:target="_blank"} or [Request a certificate](https://docs.aws.amazon.com/acm/latest/userguide/gs-acm-request-public.html){:target="_blank"}. Then just copy the ARN from a certificate added to your AWS account and set it in your Cloudliner environment.


### How to configure custom domain for static website (Cloudfront)?
You should have an SSL/TLS certificate added to your environment ([How to configure SSL/TLS certificate](#where-do-i-get-tlsssl-certificate-arn)). You also need to set your custom domain name in your environment configuration in Cloudliner. Note that Cloudliner does not manage DNS records for you. You specify that domain name here so that AWS Cloudfront can properly route your request to your distribution. Then you need to create a CNAME DNS record pointing to your distribution. The distribution URL is available in AWS console.


### How to configure custom domain for web-service running on EC2?
You should have an SSL/TLS certificate added to your environment ([How to configure SSL/TLS certificate](#where-do-i-get-tlsssl-certificate-arn)). Then just create a CNAME DNS record pointing to your Application Load Balancer URL which you can get in AWS console.


### Can Cloudliner create DNS records for me?
Not yet :)

