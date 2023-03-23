---
layout: post
read_time: true
show_date: true
title: "Deploying VPCs with CDK Python"
date: 2023-03-23
img: posts/20230323/yancy-min-842ofHC6MaI-unsplash.jpg
tags: [AWS CDK,Python,DevOps, gitlab, VPC]
category: AWS CDK
author: Joshua Martin
description: "JM3: Python VPC In CDK"
---
If you are starting your CDK path see these great resources
- [CDK Bootstrapping](https://docs.aws.amazon.com/cdk/v2/guide/bootstrapping.html)
- [CDK API Refrence](https://docs.aws.amazon.com/cdk/api/v2/python/index.html)
- [CDK Context](https://docs.aws.amazon.com/cdk/v2/guide/context.html)
- [CDK EC2 Constructs](https://docs.aws.amazon.com/cdk/api/v2/python/index.html)
  
# Introduction

We need avaliable VPC that allows us to have a secure, Application and Private subnets and following best Practices.
To Setup all the Routing and NAT Gateway or Nat Instance

## VPC Design

With VPCs and designing a CDK Template that we can reuse for multiple environments or Projects, we need to think about possible user cases.
some of the user cases may Include:
* A single point of egress for application and database workloads
* Lockdown subnets for database and applications to be within
* Web applications served to customers or the public web

With the knowledge above, its time to start to fleshout some configration around how we can define it for multiple accounts

## Configration for a Multi-Account deployment

Now with all this in mind how do we keep the stack consistant and being able to deploy it *multple ways ?*
This is were **JSON** becomes useful, we are able to define what account(s) are included in the evironment and which ones aren't.
for this example here is a snippet from our vpc_config.json file.

```json
{
"dev_account": "111111111111",
    "dev" : {
      "vpc_config": {
        "requested_subnets" : ["Public", "Private", "Application"],
        "nat_option" : "nat_gateway",
        "cidr" : "10.10.0.0/16",
        "transit_gw" : false,
        "vpn_endpoint": false,
        "site_to_site": false,
        "cidr_web": 24,
        "cidr_app": 24,
        "cidr_priv": 24,
        "avaliablity_zones" : 3,
        "NACL_PORT_START": 1024,
        "NACL_PORT_END": 65535
      },
    }
}
```

The above code we are able to use true false statements to help define what our VPC looks like, in the above example we have removed the VPN Client option, Site to Site configration, and having the use of transit gateways. This will isolate the accounts from eachother. 

Which in this user case is fine.

We are able to validate the CIDR Range also here to make sure there are no overlaps, with *jsonschema* and *ipaddress* to validate if there is any overlap or incorrect CIDR mapping within the requested Subnets.

Also validating if the IP provided are inncorrect or Internet Routable.

```python
  try:
    ip_addr = ipaddress.ip_network(args.ipaddr)
except ValueError:
  #handle bad IP
  sys.exit(f'IP CIDR Passed for VPC {self.VPC_CIDR} Contains overlap with another environment {self.env}')
```

the Public, Private and Application Subnets all have mapping within the CDK stack vpc_stack.py to understand, if they use the NAT or IGW (Internet Gateway). 

## VPC Stack

The VPC Stack iself is quite simple as pytest, ip_handler.py and Schema_check.py validate what is in the VPC Config and validate thats is going to work with the stack, using **REGEX** and more

Within the *app.py* we can validate the account IDs for which configration will be deployed with the 

```bash
  -c environment=[dev,uat,prod]
```

which calls the context method 

```python
  app = App()
  stack_env = app.node.try_get_context("environment")
```
and we are able to supply this directly to the stack(s), we also use a Synthersizer to give the bootstrap certian roles over the default bootstrap that gives what ever the user or person initilised the bootstrap.

    VpcStack(
        app,
        f"{PROJ_NAME}-{stack_env}-vpc",
        env=ENV,
        synthersizer=DefaultStackSynthesizer(qualifier=STACK_SYNTH_QUALIFIER_VPC)
    )
### Subnet Design

This is what defines how the subnets are created, and depending what was in the array they will be added to the *ec2.vpc.subnet_configration construct.

```python
class VpcStack(schemavalidate):
    def __init__(self, 
        scope: Construct, 
        id: str, 
        **kwargs) -> None:
        super().__init__(scope, id, **kwargs)
    ...
  [
            {
                "name":f"{vpc_config.publicname}",
                "subnetType": _vpc.SubnetType.PUBLIC,
                "cidrMask": self.vpc_config.cidr_web,

            },
            {
                "name":f"{self.vpc_config.applicationname}",
                "subnetType": _vpc.SubnetType.PRIVATE_WITH_NAT,
                "cidrMask": self.vpc_config.cidr_app,
            },
            {
                "name":f"{vpc_config.privatename}",
                "subnetType": _vpc.SubnetType.PRIVATE_ISOLATED,
                "cidrMask": self.vpc_config.cidr_app,
            },
        ]
```

schemavalidate is pulled from the Schema_check.py app as it builds the variables for the VPC stack to use

 ## Diagram
Diagram below shows once the stack above with the JSON Config looks like once deployed.
<center><img src='./assets/img/posts/20230323/AWS-CDK-TEMPLATE-VPC.jpg' width="540"></center><br>