---
layout: post
read_time: true
show_date: true
title: "Honey? Pots? and Intrusion Detection?"
date: 2023-03-25
img: posts/20230410/art-rachen-Asj5DFw8UAw-unsplash.jpg
tags: [DevSecOps,Security,AWS,Cloud]
category: DevSecOps
author: Joshua Martin
description: "JM5: HoneyPots"
---
If you are starting your CDK path see these great resources
- [CDK Bootstrapping](https://docs.aws.amazon.com/cdk/v2/guide/bootstrapping.html)
- [CDK API Refrence](https://docs.aws.amazon.com/cdk/api/v2/python/index.html)
- [CDK Context](https://docs.aws.amazon.com/cdk/v2/guide/context.html)
- [CDK EC2 Constructs](https://docs.aws.amazon.com/cdk/api/v2/python/index.html)
  
# Honey? Pots? and Intrusion Detection ?

So what does Honey, Pots and Intrusion Detection all have in common ?
The answer can be simple, Honeypots the term used by Security teams, DevOps and more.

Which means, that we can use simular websites, Databases and Infrastructure on that makeup or production Instance or workloads.
To think an attacker is on the infrastructure, but infact hes on a increadbly woven web that can hear, see and know what hes executing and searching.

## How can we use honey pots ?

We can see honeypots being used, to straighten what we have currently developing, so we can further expand or security settings.
They are also used as a trap spot for an attacker to enter and think they are on the main production network and have access to critical Infrastructure. 

We have also seen Honeypots become ways to protect against Spam emails as the term **Spam Traps** Indicating a simular looking mailbox to a person but its to detect spam IP and Domains, these are also seen in HoneyPots for any input fields or adding a email on a contact page.

Another simular and came from HoneyPots are **Wi-Fi Pineapples** Which can contain legitamate users but have Honeypot resources to detect Intrusions on these networks and Probes.

Personally I have 3 Networks and most of the devices are on a hidden network, some sit on they Pineapple SSID which has Five Rasberry Pi's acting as IoT Devices and Cisco Routing Devices to detect irregular activity from my devices. Across all networks.

This is all used via ubuntu and Open Canary
- [Ubuntu Pi](https://ubuntu.com/download/raspberry-pi)
- [OpenCanary GitHub](https://github.com/thinkst/opencanary)

You can also achive this with Kali's ARM distro

### But what about the Cloud ? 

As we move more and more workloads into the cloud we now have the best optunity to optimise and transform 