---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "MySQL without password"
url: /mysql-without-password
subtitle: ""
summary: "How to configure a file to access mysql without password"
authors: [ luis ]
tags: [ MySQL ]
categories: [ DevOps, Linux, SysAdmin ]
keywords: [ MySQL, Configuration, Linux ]
date: 2014-04-15
publishDate: 2014-04-15
lastmod: 2019-11-27
featured: true
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: "Smart"
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []

---
<br/>
<br/>


The common form to log in to MySQL server, is running a mysql command with your login credentials and server's IP address as arguments. For example:

```shell
mysql -u $MYSQL_ROOT -p$MYSQL_PASS
```

However, besides the inconvenience of typing extra arguments, using plain-text login credentials in a command line like above is really not a secure way to access a MySQL server.

MySQL offers a way for you to log in to MySQL server without password, by using an external MySQL configuration file. In Linux, there are two different kinds of MySQL configuration files:  

1. /etc/my.cnf and
2. ~/.my.conf  

While any system-wide MySQL configuration is defined in /etc/my.cnf, any user-specific MySQL configuration is stored in ~/.my.cnf. You can leverage ~/.my.cnf, to define your MySQL login credential in the file.

```shell
vim ~/.my.cnf
```

We put our MySQL user in the configuration file:

```shell
[client]
user=root
password=$PASSWORD_ROOT
```

Make sure to have the configuration file readable to you only.

```shell
chmod 0600 ~/.my.cnf
```

Once ~/.my.cnf is created, simply typing mysql command will let you log in to the MySQL server as root, and you no longer need to provide login password separately.

```shell
mysql

Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 14787
Server version: 5.1.73 Source distribution

Copyright (c) 2000, 2013, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>

```