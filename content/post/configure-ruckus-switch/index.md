---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Configure SSH on a Ruckus Switch"
url: "/configure-ruckus-switch"
subtitle: "Steps to configure ssh and webaccess on a Ruckus Switch"
summary: "Steps to configure ssh and webaccess on a Ruckus Switch"
authors: [ luiscachog ]
tags: [ Ruckus, DevOps, SysAdmin, Network ]
categories: [ SysAdmin, DevOps, Network ]
keywords: [Ruckus, DevOps, Network, Networking]
date: 2018-11-20
publishDate: 2018-11-20
lastmod: 2021-04-30
featured: true
draft: false


# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---

I just have a Ruckus ICX 7150 Switch on my home and I'm trying to get access under ssh and web, to easy configuration and security instead of use telnet.
So, I logged in using telnet and then run the following commands to configure a username/password and begin to receive petitions over port 22(ssh) and port 443(https).
Let's begin!

1. We will connect via telnet to the switch.

    ```shell
    telnet SWITCH_IP
    ```

1. Once we are on the Switch CLI as a optional step, we can configure an IP on the switch.

    ```shell
    device> enable
    device# configure terminal
    device(config)# ip address IP_ADDRESS/CIDR
    device(config)# ip default-gateway IP_GATEWAY
    ```

1. Now, the next steps are for generate a SSL certificate, a username/password, activate password to login and enable thw web access and ssh access.

    ```shell
    device(config)# crypto-ssl certificate generate
    device(config)# username USERNAME password PASSWORD
    device(config)# aaa authentication login default local
    device(config)# aaa authentication web-server default local
    ```

1. It may take several minutes to generate the certificate key. After that, save the configuration.

    ```shell
    device(config)# write memory
    ```

Now you are able to login on your switch using ssh or web.

**References:**

- Blog with ruckus commands [^1]

[^1]: [Ruckus ICX7150-C12P – Initial Configuration](https://docs.ruckuswireless.com/fastiron/08.0.50/fastiron-08050-commandref/GUID-E60DB55B-5E68-40EC-A999-73C6C071EFF7.html)
