# F5 Beacon SRE Demo


[![license](https://img.shields.io/github/license/:merps/:f5-ts-sumo.svg)](LICENSE)
[![standard-readme compliant](https://img.shields.io/badge/readme%20style-standard-brightgreen.svg?style=flat-square)](https://github.com/RichardLitt/standard-readme)

This document covers the initial setup and configuration of the SRE Demo as demostrated on the most recent webinar.


## Table of Contents

- [Security](#security)
- [Background](#background)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
    - [Beacon](#beacon)
    - [DataDog](#datadog)
    - [cBIG-IP](#cbig-ip)
    - [NGINX+](#ngnix)
- [Configuration](#configuration)
- [Usage](#usage)
- [API](#api)
- [Contributing](#contributing)
- [License](#license)


## Security

F5 Telemetry Streaming services (TS) operate over HTTPS using OAuth2 authorisation.


## Background

This is a How To Guide to replicate, with references to the underlying infrastructure of additional testing would 
like to be explored, this will cover just the steps required to complete configuration for NGINX+, cBIG-IP & F5aaS Beacon.

## Prerequisites

To support this deployment pattern the following components are required:

* F5 BIP-IP (physical or VE)
* NGINX+ Instance
* [DataDog](https://www.datadoghq.com/free-datadog-trial/) Account.
* F5 Toolchain Components:
    * [F5 Application Services v3 (AS3)](https://clouddocs.f5.com/products/extensions/f5-appsvcs-extension/latest/)
    * [F5 Telemetry Streaming (TS)](https://clouddocs.f5.com/products/extensions/f5-telemetry-streaming/latest/)
* [Postman](https://www.postman.com/)
* [F5 CloudServices Portal](https://portal.cloudservices.f5.com/) Account

***Optional***

If there is the desire to install the full demo stack the addtional tools/accounts are suggested:

* [Terraform CLI](https://www.terraform.io/docs/cli-index.html)
* [git](https://git-scm.com/)
* [AWS CLI](https://aws.amazon.com/cli/) access.
* [AWS Access Credentials](https://docs.aws.amazon.com/general/latest/gr/aws-security-credentials.html)


## Installation 

This section covers the simple configuration for the deployment of the Beacon demo application. 


### *Beacon*

To see how easy it is to sign up for both a F5 CloudServices Account and subscribe to Beacon refer to this [PDF](files/Beacon_Sub.pdf)

### *DataDog*

To deploy 
### *cBIG-IP*

The deployment environment used for development is coovered in detail [F5 SRE Demo](https://github.com/merps/f5-sre-demo),
this is a AWS Deployment example of AutoScaling AWAF. For simplicity, steps replicate this deployment are as follows;

***a)***    First, clone the repo:
```
git clone https://github.com/merps/f5-sre-demo.git
```

***b)***    Second, create a [tfvars](https://www.terraform.io/docs/configuration/variables.html) file in the following format to deploy the environment;

#### Inputs
Name | Description | Type | Default | Required
---|---|---|---|---
cidr | CIDR Range for VPC | String | *NA* | **Yes**
region | AWS Deployment Region | String | *NA* | **Yes**
azs | AWS Avaliability Zones | List | *NA* | **Yes** 
secops-profile | SecurityOperations AWS Profile | String | `default` | **Yes**
customer | Customer/Client Short name used for AWS Tag/Naming | String | `customer` | No
environment | Environment Shortname name used for AWS Tag/Naming | String | `demo` | No
project | Project Shortname name used for AWS Tag/Naming | String | `project` | No
ec2_key_name | EC2 KeyPair for Instance Creation | String | *NA* | **Yes**


***c)***    Third, intialise and plan the terraform deployment as follows:
```
cd f5-sre-demo/
terraform init
terraform plan --vars-file ../variables.tfvars
```

this will produce and display the deployment plan using the previously created `varibles.tfvars` file.


***d)***    Then finally to deploy the successfuly plan;
```
terraform apply --vars-file ../variables.tfvars
```

> **_NOTE:_**  This architecture deploys two c4.2xlage PAYG BIG-IP Marketplace instances, it is 
recommended to perform a `terraform destroy` to not incur excessive usage costs outside of free tier.


This deployment also covers the provisioning of the additional F5 prerequeset components so required for 
deployment example covered in the [F5 SRE Demo](https://github.com/merps/f5-sre-demo)


### *NGINX*


##### TODO - NGINX



## Configuration


### Beacon


##### TODO - Beacon

### F5 cBIG-IP

As with DataDog, detailed instructions for the deployment and configuration for TS is located at 
[F5 Telemetry Streaming](https://clouddocs.f5.com/products/extensions/f5-telemetry-streaming/latest/).

As previously, steps to configure;

##### TODO - DataDog

1. Update TS declations as this example example;

```
{
    "class": "Telemetry",
    "TS_System": {
        "class": "Telemetry_System",
        "systemPoller": {
            "interval": 60,
            "enable": true,
            "trace": false,
            "actions": [
                {
                    "setTag": {
                        "tenant": "`T`",
                        "application": "`A`"
                    },
                    "enable": true
                }
            ]
        },
        "enable": true,
        "trace": false,
        "host": "localhost",
        "port": 8100,
        "protocol": "http"
    },
    "TS_Listener": {
        "class": "Telemetry_Listener",
        "port": 6514,
        "enable": true,
        "trace": false,
        "match": "",
        "actions": [
            {
                "setTag": {
                    "tenant": "`T`",
                    "application": "`A`"
                },
                "enable": true
            }
        ]
    },
    "Poller":{ 
       "class":"Telemetry_System_Poller",
       "interval":60,
       "enable":true,
       "trace":false,
       "allowSelfSignedCert":false,
       "host":"localhost",
       "port":8100,
       "protocol":"http"
    },
    "Beacon_Consumer": {
        "class": "Telemetry_Consumer",
        "type": "Sumo_Logic",
        "host": "collectors.au.sumologic.com",
        "protocol": "https",
        "port": 443,
        "enable": true,
        "trace": false,
        "path": "/receiver/v1/http/",
        "passphrase": {
            "cipherText": "this is a secret"
        }
    },
    "schemaVersion": "1.6.0"    
}
```
3. Push updated TS declaration to cBIG-IP.


# Usage

### As per TODO 

## API

### As per TODo

## TODO

List of task to make the process my automated;

- [ ] Create workflow for Jenkins/GitLab to deploy dashboard
- [ ] Workflow improvements for DO/AS3/TS
- [ ] Usage Instructions(?)

## Contributing

See [the contributing file](CONTRIBUTING.md)!

PRs accepted.

### Any optional sections

## License

[MIT © merps.](../LICENSE)
