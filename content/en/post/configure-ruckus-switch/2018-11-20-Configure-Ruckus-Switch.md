---
author: "Luis Cacho"
title: "Configure SSH on a Ruckus Switch"
description: "Steps to configure ssh and webaccess on a Ruckus Switch"
type: "post"
url: /configure-ruckus-switch/
categories: ["Sysadmin","DevOps", "Network"]
tags: ["Ruckus","DevOps","Sysadmin","Network"]
keywords: Ruckus, Switch, Network
featured: "Ruckus-Logo.jpg"
featuredalt: ""
featuredpath: "date"
linktitle: ""
draft: false
date: 2018-11-20
lastmod: 2018-11-20
---

![Ruckus-Logo](/img/2018/11/Ruckus-Logo.jpg)

I just have a Ruckus ICX 7150 Switch on my home and I'm trying to get access under ssh and web, to easy configuration and security instead of use telnet.
So, I logged in using telnet and then run the following commands to configure a username/password and begin to receive petirions over port 22(ssh) and port 443(https). 
Let's begin!

1. We will connect via telnet to the switch.

     ```shell
     telnet SWITCH_IP
     ```

2. Once we are on the Switch CLI as a optional step, we can configure an IP on the switch.

     ```shell
     device> enable
     device# configure terminal
     device(config)# ip address IP_ADDRESS/CIDR
     device(config)# ip default-gateway IP_GATEWAY
     ```

3. Now, the next steps are for generate a SSL certificate, a username/password, activate password to login and enable thw web access and ssh access.

     ```shell
     device(config)# crypto-ssl certificate generate
     device(config)# username USERNAME password PASSWORD
     device(config)# aaa authentication login default local
     device(config)# aaa authentication web-server default local
     ```

4. It may take several minutes to generate the certificate key. After that, save the configuration.

     ```shell
     device(config)# write memory
     ```
Now you are able to login on your switch using ssh or web.

Source: https://robrobstation.com/2017/07/17/ruckus-icx7150-c12p-initial-configuration/
