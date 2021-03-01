# Config settings 

The following environment variables control the Nextflow Tower configuration and deployment. 

* `TOWER_APP_NAME`: Application name (default: `Nextflow Tower`).
* `TOWER_SERVER_URL`: Server URL e.g. `https://tower.your-company.com` (MANDATORY).
* `TOWER_CONTACT_EMAIL`: Sysadmin email contact e.g. `tower@your-company.com` (MANDATORY).
* `TOWER_DB_URL`: Database JDBC connection URL e.g. `jdbc:mysql://localhost:3307/tower` (MANDATORY).
* `TOWER_DB_DRIVER`: Database JDBC driver class name (default: `com.mysql.cj.jdbc.Driver`).
* `TOWER_DB_DIALECT`: Database SQL Hibernate dialect (default: `io.seqera.util.MySQL55DialectCollateBin`).
* `TOWER_DB_USER`: Database user name (MANDATORY).
* `TOWER_DB_PASSWORD`: Database user password (MANDATORY).
* `TOWER_DB_MIN_POOL_SIZE`: Database min connections pool size e.g. 5 (default: 5).
* `TOWER_DB_MAX_POOL_SIZE`: Database max connections pool size e.g. 20 (default: 10).
* `TOWER_DB_MAX_LIFETIME`: Database max lifespan of connections in milliseconds (default: 1800000)
* `TOWER_SMTP_HOST`: SMTP server host name e.g. `email-smtp.eu-west-1.amazonaws.com` (MANDATORY).
* `TOWER_SMTP_PORT`: SMTP server port e.g. `587` (default: 587).
* `TOWER_SMTP_AUTH`: SMTP server authentication e.g. `true` (default: `true`).
* `TOWER_SMTP_USER`: SMTP server user name (MANDATORY).
* `TOWER_SMTP_PASSWORD`: SMTP server user password (MANDATORY).
* `TOWER_JWT_SECRET`: Secret used to generate the login JWT token. Use a long random string (35 chars or more) (MANDATORY).
* `TOWER_CRYPTO_SECRETKEY`: Secret key used to encrypt user credentials. Use a long random string. **NOTE**: do not change across restarts (MANDATORY).
* `TOWER_LAUNCH_CONTAINER`: Container image to run Nextflow execution
* `MICRONAUT_PORT`: Backend connection port (default: 8080).
* `MICRONAUT_ENVIRONMENTS`: Enable specific configuration profile for the Micronaut backend service.

## Compute environments

Tower allows the deployment of Nextflow pipelines across a large number of different execution platform.

Choose the platforms to be made available to Tower user adding one or more of the following platform IDs to the current value of the `MICRONAUT_ENVIRONMENTS` 
variable in your configuration, separating multiple values with a comma character.

The following IDs options are available: 

 - `awsbatch-platform`: AWS Batch cloud compute service
 - `gls-platform`: Google LifeSciences cloud compute service
 - `lsf-platform`: IBM LSF batch scheduler 
 - `slurm-platform`: Slurm batch scheduler
 - `k8s-platform`: Kubernetes compute platform
 - `eks-platform`: AWS EKS compute platform
 - `gke-platform`: Google GKE compute platform


## MySQL configuration settings

* `TOWER_DB_URL=jdbc:mysql://YOUR-DB-HOST:3306/tower`
* `TOWER_DB_DRIVER=com.mysql.cj.jdbc.Driver`
* `TOWER_DB_DIALECT=io.seqera.util.MySQL55DialectCollateBin`
* `TOWER_DB_USER=tower`
* `TOWER_DB_PASSWORD=tower`
* `FLYWAY_LOCATIONS=classpath:db-schema/mysql`

## MariaDB configuration settings 

* `TOWER_DB_URL=jdbc:mariadb://YOUR-DB-HOST:3306/tower`
* `TOWER_DB_DRIVER=org.mariadb.jdbc.Driver`
* `TOWER_DB_DIALECT=io.seqera.util.MariaDB10DialectCollateBin`
* `TOWER_DB_USER=tower`
* `TOWER_DB_PASSWORD=tower`
* `FLYWAY_LOCATIONS=classpath:db-schema/mariadb`

## OpenID Connect related variables 

* `TOWER_OIDC_CLIENT`: The client ID provided by your authentication service.
* `TOWER_OIDC_SECRET`: The client secret provided by your authentication service.
* `TOWER_OIDC_ISSUER`: The authentication service URL to which Tower connects to authenticate the sign-in request e.g. `https://dev-886323.okta.com/oauth2/default`. 
* `MICRONAUT_ENVIRONMENTS`: This variable needs to include the `auth-oidc` value. **NOTE**: specify it appending the `,auth-oidc` string to the current setting for your deployment without removing
the current value(s). Also, the `MICRONAUT_ENVIRONMENTS` variable should be defined independently for the `cron` and `backend` containers.

In your OpenID provider setting specify the following URL as callback address or authorised redirect: 

```
https://<YOUR HOST OR IP>/oauth/callback/oidc
```

## GitHub authentication provider 

To use GitHub as SSO provider for Nextflow Tower, register your Tower instance as a GitHub OAuth App
in your organization settings page eg. https://github.com/organizations/{YOUR-ORGANIZATION}/settings/applications.

When creating the OAuth App specify the following path as callback URL: `https://{your-deployment-domain-name}/oauth/callback/github` (replacing the `{your-deployment-domain-name}` placeholder with the domain name of your deployment).

Finally include the following variable in the backend environment configuration: 

* `TOWER_GITHUB_CLIENT`: The client id provided by GitHub when register the new OAuth App.
* `TOWER_GITHUB_SECRET`: The client secret provided by GitHub when register the new OAuth App.
* `MICRONAUT_ENVIRONMENTS`: This variable needs to include the `auth-github` value. **NOTE**: specify it appending the `,auth-github` string to the current setting for your deployment without removing
the current value(s). Also, the `MICRONAUT_ENVIRONMENTS` variable should be defined independently for the `cron` and `backend` containers.

## Google authentication provider 

To use Google as SSO provider for Nextflow Tower: 

- Visit https://console.developers.google.com and create a new project
- From the sidebar, click the *Credentials* tab
- Click *Create credentials* and choose *OAuth client* ID from the dropdown
- On the next page, select *Web Application* type
- enter the redirect URL: `https://{your-deployment-domain-name}/oauth/callback/google` (replacing the `{your-deployment-domain-name}` placeholder with the domain name of your deployment).
- Confirms the operation. You will then receive a *Client ID* and *secret ID*. 


Finally include the *Client ID* and *secret ID* in following variables in the Tower backend environment configuration: 

* `TOWER_GOOGLE_CLIENT`: The client id provided by Google in the above steps.
* `TOWER_GOOGLE_SECRET`: The client secret provided by Google in the above steps.
* `MICRONAUT_ENVIRONMENTS`: This variable needs to include the `auth-google` value. **NOTE**: specify it appending 
the `,auth-google` string to the current setting for your deployment without removing the current value(s).
Also, the `MICRONAUT_ENVIRONMENTS` variable should be defined independently for the `cron` and `backend` containers.

### Configure user access allow list

When using a public authentication provider such as Google or GitHub you may need 
to restrict the access only to specific user emails address or domains. 

Add the following snippet in the file `/tower.yml`: 

```
tower:
  auth:
    <PROVIDER>:
      allow-list:
        - "*@foo.com"
        - "me@bar.com"
```

Replace the `<PROVIDER>` placeholder either with `github`, `google` or `oidc` (you will need to define twice if you are using both of them). 

The allow list entries are case-insensitive.

## Custom navigation menu 

To modify the Tower top navigation menu and add custom menu items, add a configuration snippet similar 
the one shown below in the `/tower.yml` configuration file:

```
tower:
  navbar:
    menus:
      - label: "My Community"
        url: "https://host.com/foo"
      - label: "My Pipelines"
        url: "https://other.com/bar"
```

## Private Git host services

Nextflow has built-in support for public and private private Git repositories and services 
such as Bitbucket, Github and Gitlab. To access private repositories it is required to provide 
the security credentials either using the Tower frontend or in a centralized manner including 
the following snippet in the `/tower.yml` file: 

```
tower:
  scm:
    providers:
      github:
        user: <YOUR GITHUB USER NAME>
        password: <YOUR GITHUB ACCESS TOKEN OR PASSWORD>
      gitlab:
        user: <YOUR GITLAB USER NAME>
        password: <YOUR GITLAB PASSWORD>
        token: <YOUR GITLAB TOKEN>
      bitbucket:
        user: <YOUR BITBUCKET USER NAME>
        password: <YOUR BITBUCKET TOKEN OR PASSWORD>
```         


The `tower.yml` has to be made available in the backend (container or pod) root directory:

Check the respective provider documentation to learn how to create security access tokens: 
* https://confluence.atlassian.com/bitbucketserver/personal-access-tokens-939515499.html
* https://gitlab.com/profile/personal_access_tokens
* https://help.github.com/en/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line

## Custom AWS Batch launch container 

Tower automatically register a AWS Batch Job definition to launch pipelines with the required 
Nextflow runtime pulling the container image on demand. 

If you need to manage this manually, create a Job definition in your AWS Batch environment, 
using the following settings:

* name: any of your choice
* image: the nf-luancher image reported in the *Availability* section in the [README](README.md) file.
* vcpus: 1
* memory: 1000
* command: `true`

Once the job definition is registered, set in the Tower enviornment configuration the following variable: 

```
TOWER_LAUNCH_CONTAINER=job-definition://YOUR_JOB_DEF_NAME
```

Replacing the `YOUR_JOB_DEF_NAME` placeholder with the name or your Batch Job definition.
