---
layout: post
read_time: true
show_date: true
title: "CTFs and Gitlab within CDK"
date: 2023-03-21
img: posts/20230322/fotis-fotopoulos-6sAl6aQ4OWI-unsplash.jpg
tags: [AWS CDK,Python,DevOps, gitlab]
category: AWS CDK
author: Joshua Martin
description: "JM1: Explaining CDK"
---
If you are starting your CDK path see these great resources
- [CDK Bootstrapping](https://docs.aws.amazon.com/cdk/v2/guide/bootstrapping.html)
- [CDK API Refrence](https://docs.aws.amazon.com/cdk/api/v2/python/index.html)
- [CDK Context](https://docs.aws.amazon.com/cdk/v2/guide/context.html)
  
# Introduction

Lets start here, how do we replicate and keep the same or similar environments in creating an similar experience for attendees ? Solution:
AWS CDK 
CDK Orchestrates and allows us to use a programming language for CloudFormation.

## CICDo’h 

A Play on words for CI/CD and from the Simpsons Do’h, Combining the learning of best practices and what security issues can arise with bad password hygiene and too many IAM Privileges to a Runner.
The Game Master will need to deploy 3 Stacks, 
- Stack 1 Contains The VPC and Files needed to deploy Software and the Story to the Instances from S3
- Stack 2 Contains Parameters for Flags and the Database
- Stack 3 Contains RDS Instance and Data needed to Update tables with flags based on Teams or Players 

## Deployment

Stack 1 Deploys basic VPC Resources and Some Critical infomation about the Size of the teams and how Many Players
```shell
cdk deploy CTF-CICDoh-Prereqs -c Size=(S,M,L,XL) -c env=(dev,test,sandpit)
```
Using Json data we can specify config changes 
```json   
    "Dev_Account": "1234567890",
    "Sandpit1" : "1234567890",
    "Sandpit3" : "1234567890",
    "ShirtSize" : ["SP","S","M", "L", "XL"],
    "S": {
      "TeamSize" : "1",
      "Teams" : "2",
      "Runner" : {
        "Gitlab_token" : ["/cicdoh/runner/token", "/cicdoh/runner/token2"],
        "vpc_name" : "default",
        "used_isolated_zone": false,
        "instance_vol_size": "20",
        "base_ami_name": "Ubuntu/latest"
      },
 ```

 ## Diagram
Everything is in a public zone to allow for access to the internet and to show that this isn’t best practice but some setups look like this and we need to identify how to also improve on this solution.

<center><img src='./assets/img/posts/20230322/CICDoh-Diagram.drawio.png' width="540"></center><br>

## Flag Generation
Using uuid module we are able to generate flags for `x` amount of teams and capture points 

```Python

from ast import Num
import os
import uuid

from env_config import COUNT_TEAMS

TEAMS = []
UUIDS = []
...
## Flag Generation FLAG1
for amount_teams in range(1,COUNT_TEAMS+1):
    UUIDS.append(uuid.uuid4())
    TEAMS.append(f'TEAM {amount_teams}')

FLAG_1_DICT = dict(zip(TEAMS, UUIDS))
print(f"{bcolors.OKGREEN}",'FLAG EVENT 1: ', FLAG_1_DICT, f"{bcolors.ENDC}")

## Flag Generation FLAG2
for amount_teams in TEAMS:
    UUID_TEMP = uuid.uuid4()
    UUIDS.append(f'{UUID_TEMP}')

FLAG_2_DICT = dict(zip(TEAMS, UUIDS))
print(f"{bcolors.OKBLUE}",'FLAG EVENT 2: ', FLAG_2_DICT, f"{bcolors.ENDC}")

```