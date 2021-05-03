---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "MySQL reset root password"
url: "/mysql-reset-root-password"
subtitle: "How to reset a root MySQL password"
summary: "How to reset a root MySQL password"
authors: [ luiscachog ]
tags: [ MySQL ]
categories: [ Linux, SysAdmin ]
keywords: [ MySQL, Configuration, Linux, Reset ]
date: 2014-07-30
publishDate: 2014-07-30
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

Hello,

This time I share with you the faster and more secure method to reset the root password of MySQL.

This method is faster because the downtime is between 1 or 2 seconds (MySQL restart time) and it is more secure because the mysqld is not started without grants on the tables.

The steps are:

1. Create text file /var/lib/mysql/mysql-init with the sintaxis to reset the password for user root:

    ```shell
    vim /var/lib/mysql/mysql-init
    ```

    ```sql
    SET PASSWORD FOR 'root'@'localhost' = PASSWORD('new_password');
    ```

1. Add under the [mysqld] stanza on the file /etc/my.cnf:

    ```shell
    init-file=/var/lib/mysql/mysql-init
    ```

1. Restart the mysqld service:

    ```shell
    service mysqld restart
    ```

1. Remove the init-file line from /etc/my.cnf

1. Remove /var/lib/mysql/mysql-init

    ```shell
    rm /var/lib/mysql/mysql-init
    ```

And after that, you can access again to your mysql instance.

:smile:
