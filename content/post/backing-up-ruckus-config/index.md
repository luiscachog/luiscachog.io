---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/
title: "Backing Up a Ruckus Switch Config"
url: "/backing-up-ruckus-config"
subtitle: "Commands to create a backup for my Ruckus ICX 7150 Switch"
summary: "I show how to create a backup before upgrade the Switch Firmware"
authors: [ luiscachog ]
tags: [ Ruckus, DevOps, SysAdmin, Network ]
categories: [ SysAdmin, DevOps, Network ]
keywords: [Ruckus, DevOps, Network, Networking, Backup, TFTP ]
date: 2020-11-21
publishDate: 2020-11-21
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

I want to do some changes on my home network to improve the performance, so I will implement VLANs on my network.
But before I do that I want to document how to perform a backup of my Ruckus ICX 7150 Switch.

In a [past post]({{< relref "/configure-ruckus-switch" >}}) I mentioned how to enable ssh and web cofiguration on the Ruckus switch,
so my first attemtp was to download the configuration file from the web interface but unfortunately it is not possible to do it, there is not an option for that.
What I did is go to the [documentation](http://docs.ruckuswireless.com/fastiron/hardware/icx7150-installguide/GUID-25306120-376C-44B2-BAE7-3D969EC889A3.html)
and found the `copy` command but I need a [TFTP server](https://en.wikipedia.org/wiki/Trivial_File_Transfer_Protocol) to be able to download the backup file.

Let's start!

1. Install a TFTP server - This is easy will depend on your Operative System, for my is an ArchLinux laptop.

    ```shell
    yay -Sy atftp
    ```

    The configuration file for atftp is `/etc/conf.d/atftpd`

1. Next step, is login on your Ruckus switch and perform the copy command:

    ```shell
    ssh <USER>@<SWITCH-IP>
    copy running-config tftp <TFTP-SERVER-IP> myconfig.cfg

    #In my case is:
    ssh ruckus@192.168.50.5
    copy running-config tftp 192.168.50.4 myconfig.cfg
    ```

1. Verify that the file is on your TFTP server, by default, the configured directory for atftp is `/srv/atftp/` so you should go that location and verify that the generated file is created.

    ```shell
    cd /srv/atftp
    ls -la
    ```

That's all, you can restore your switch configuration if needed.

Bye!
