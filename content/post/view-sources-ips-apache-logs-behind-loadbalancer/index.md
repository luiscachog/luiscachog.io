---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "View sources IP's in Apache Logs behind a Load Balancer"
url: "/view-sources-ips-apache-logs-behind-loadbalancer"
subtitle: "How to configure Apache Logs to view the source IP's when it is behind a Load Balancer"
summary: "How to configure Apache Logs to view the source IP's when it is behind a Load Balancer"
authors: [ luiscachog ]
tags: [ DevOps, Apache, LoadBalancer, Rackspace, Cloud Provider ]
categories: [ Linux, SysAdmin, Rackspace, Cloud Provider ]
keywords: [ Apache, Logs, LoadBalancer, Rackspace ]
date: 2015-02-13
publishDate: 2015-02-12
lastmod: 2021-05-02
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
When you use the Rackspace Cloud Load Balancers, it is common that the IP logged in Apache is the Private IP (ServiceNet) from the Cloud Load Balancer, however, we can fix that.

We can view sources IP's in Apache Logs doing some changes on Apache configuration file and also on the vhosts configuration files.

On your Apache configuration file, you should to find the line:

```shell
LogFormat "%h %l %u %t \"%r\" %&gt;s %b \"%{Referer}i\" \"%{User-Agent}i\"" combined
```

Modified to:

```shell
LogFormat "%{X-Forwarded-For}i %h %l %u %t \"%r\" %&gt;s %O \"%{Referer}i\" \"%{User-Agent}i\"" combined
```

And also, on your vhosts configuration files you should to change the "combined" LogFormat definition will then be called in a "CustomLog" entry specific to your VirtualHost configuration.
Here is an example VirtualHost definition to show you what I'm referring to:

```shell
ServerAdmin webmaster@example.com
DocumentRoot /var/www/html/example.com
ServerName example.com
ErrorLog logs/example.com-error_log
CustomLog logs/example.com-access_log combined
```

After adding the X-Forwarded-For definition to the LogFormat definition, you can restart Apache and view the logs to notice the difference.
If all is done properly, you will see an actual public IP in the first field of your logs instead of the Cloud Load Balancer IP.
