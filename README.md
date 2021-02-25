# Nextflow Tower Deployment for the AWS Marketplace

This repository contains documentation for how to deploy Nextflow Tower Enterprise on cloud infrastructure using the AWS Marketplace. 

## Requirements 

* An AWS Account
* The AWS CLI tool
* An RDS MySQL instance 
* SMTP Email Service
* License and pull permissions for Nextflow Tower Enterprise

Nextflow Tower Enterprise is an application server composed mainly of a Java backend, based on 
the [Micronaut framework](https://micronaut.io/) and web frontend based on the [Angular](https://angular.io/)
web framework. 

## Availability 

Nextflow Tower is distributed to customers as a collection of Docker containers available through the Nextflow Tower 
container registry hosted on the AWS container service. These containers are also availible via the AWS Marketplace.

## Configuration 

Find in the [config](config.md) documentation the list of Tower configuration settings.

## Deployment 

Nextflow Tower can be deployed on the AWS Marketplace using ECS.

* [AWS Elastic Container Service Deployment](ecs/README.md)

## OpenID Connect authentication 

Nextflow Tower supports third-party authentication providers compliant with 
the [OpenID Connect 1.0](https://openid.net/connect/) protocol. 

Refer to the [config](config.md#openid-connect-related-variables) document for details.

## AWS Batch configuration 

Check it out [this documentation](aws-batch/README.md) for setting up the AWS Batch environment required 
to deploy Nextflow workflow executions. 

## Release notes

Check the latest release notes at [this link](release-notes-20.12.md).

## Support 

For further information contact the Seqera [support](mailto:support@seqera.io) team via email or through the channel provided as part of the Nextflow Tower Enterprise License agreement.
