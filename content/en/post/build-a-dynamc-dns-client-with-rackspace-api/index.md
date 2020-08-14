---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: " Build a Dynamic DNS Client with Rackspace API"
url: "/build-a-dynamc-dns-client-with-rackspace-api"
subtitle: "How to Build a Dynamic DNS via API"
summary: "How to Build a Dynamic DNS via API"
authors: [ luiscachog ]
tags: [ API, DevOps, DNS, Rackspace Public Cloud]
categories: [ DevOps, Rackspace, SysAdmin ]
keywords: [ API, DevOps, Rackspace, SysAdmin, DNS ]
date: 2016-04-11
publishDate: 2016-04-11
lastmod: 2019-11-13
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

This time I've want to create a homemade Server with my Raspberry Pi2 and publish it using my own sub-domain, the main problem is that the ISP provide me an dynamic IP and we should ensure that if my IP address change the sub-domain record should point to the new IP.

The instructions assume that you:
  
- Have a domain
  
- Have already changed your NS records to point to dns1.stabletransit.com and dns2.stabletransit.com.

1. You should download the latest version of rsdns from github

```shell
cd ~/bin/
git clone https://github.com/linickx/rsdns.git
```

2. Go to your Rackspace portal (https://mycloud.rackspace.com/) and grab your Username & API key (It's under "Your Account" -> "Account Settings" -> "API Key")

3. Create a configuration file for rsdns (~/.rsdns_config) with your settings.

```shell
#!/bin/bash
RSUSER=lcacho 
RSAPIKEY=1234567890
RSPATH=~/bin/rsdns/
```

4. You need your domain created on Rackspace(It's under "Networking" -> "Cloud DNS" -> "Create Domain") if you don't have your domain created you are able to created using rsdns:

```shell
./rsdns-domain.sh -d www.luiscachog.io -e lcacho@luisachog.io
```

5. Once you have a domain setup you need to create an A record. To create the A record you going to need an IP address, you can use http://icanhazip.com to get your actual current IP. Again to create a record you are able to do it from Rackspace panel (It's under "Networking" -> "Cloud DNS" -> YOUR_DOMAIN -> "Add Record") or you can use rsdns:

```shell
./rsdns-a.sh -n dynamic-host.luiscachog.io -i 123.123.123.123 -t 3600
```

In the above the TTL is set to 1hr (3600 secs), this is so that DNS caches do not keep the record too long. That's all the pre-work done, now lets get your dynamic host setup!

6. The script to update your a record is rsdns-dc.sh, and you run it like this:

```shell
./rsdns-dc.sh -n dynamic-host.luiscachog.io
```

The script uses icanhazip to get your current IP, it then update the A record with it.

I never switch off my router so I have create a created a cronjob to run that script every 2 hours, plus the 1hr TTL should mean that the record is roughly in sync with my IP without making unnecessary requests

7.- I use CentOS, so I can simply drop the following file called rsdns-dc into /etc/cron.d/ with this...

```shell
vim /etc/cron.d/rsdns-dc
* */2  * * *     lcacho /home/lcacho/bin/rsdns/rsdns-dc.sh -n dynamic-host.luiscachog.io &>/dev/null
```

Now we are done! Private Dynamic DNS on your own zone using the Rackspace API.