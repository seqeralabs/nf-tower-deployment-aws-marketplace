# Release notes for version 20.12 

Nextflow Tower 20.12.x introduces support for:
- Add support for Kubernetes clusters 
- Add support for AWS EKS clusters 
- Add support for Google Cloud GKE clusters 
- Add support for Launch stub-run feature
- Add AWS Batch Fusion mounts 
- Enhanced security, API uses HTTP bearer auth token 
- System security improvements 
- Upgrade Java runtime to version 11 
- Upgrade Micronaut runtime to version 2.1
- Upgrade Nextflow launcher to version 20.12.0-edge

NOTE: If you are using the Tower API, this version Tower supports 
HTTP Bearer authentication. This means newly generated user access  
tokens should use the `Authentication: Bearer` header. 
HTTP Basic authentication is still supported for backwards compatibility
with existing tokens, even though we suggest upgrading to the new authentication scheme. Find more details 
in the [Tower documentation](https://help.tower.nf/docs/api/overview/#authentication).


## Updating Tower deployment from version 20.10.x to 20.12.x

### Compute environments

It is now required to define the Compute environments that are to be available 
to users in the Tower configuration.

The following ids options are available: 

 - `awsbatch-platform`: AWS Batch cloud compute service
 - `gls-platform`: Google LifeSciences cloud compute service
 - `lsf-platform`: IBM LSF batch scheduler 
 - `slurm-platform`: Slurm batch scheduler
 - `k8s-platform`: Kubernetes compute platform
 - `eks-platform`: AWS EKS compute platform
 - `gke-platform`: Google GKE compute platform

Choose one or more of these platform ids and *append* to your current `MICRONAUT_ENVIRONMENTS` 
variable, separating them via a comma.

### Database schema

This Tower version requires a database schema update. Follow these steps 
to update your DB instance and the Tower installation. 

#### Kubernetes based deployment 

1. Update the Tower container images in the Kubernetes manifest yaml files to: 

* 195996028523.dkr.ecr.eu-west-1.amazonaws.com/nf-tower-enterprise/backend:v20.12.1
* 195996028523.dkr.ecr.eu-west-1.amazonaws.com/nf-tower-enterprise/frontend:v20.12.1

If you are using AWS Batch with a custom launcher job definition you need to update it to use the following container image (see the [config document](config.md#custom-aws-batch-launch-container) for details): 

* public.ecr.aws/e2x4u7r1/tower/nf-launcher:20.12.0-edge


Refer to the manifests included in the [k8s](k8s/) folder for details. 

2. Update the Tower *cron* service using the following: 

```
kubectl apply -f tower-cron.yml
```

Note: This task will automatically run the Tower database schema update tool. 

3. Update the Tower backend and frontend services using the following command: 

```
kubectl apply -f tower-svc.yml
```

#### Custom deployment script
  
1. Pull or update the Tower container images references in your 
deployment script(s) to:
 
* 195996028523.dkr.ecr.eu-west-1.amazonaws.com/nf-tower-enterprise/backend:v20.12.1
* 195996028523.dkr.ecr.eu-west-1.amazonaws.com/nf-tower-enterprise/frontend:v20.12.1

If you are using AWS Batch with a custom launcher job definition you need to update it to use the following container image (see the [config document](config.md#custom-aws-batch-launch-container) for details): 

* public.ecr.aws/e2x4u7r1/tower/nf-launcher:20.12.0-edge

 
2. Update the Tower database schema by running the `/migrate-db.sh` provided in the 
 backend container. NOTE: Make sure to include the identical environment as used in the
 normal backend execution. 
 
3. Once the schema update completes, deploy Tower following your usual procedure. 
