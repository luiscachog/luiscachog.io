---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "SFTP Jailed"
url: "/sftp-jailed"
subtitle: "How to configure a FTP Secured and Jailed"
summary: "How to configure a FTP Secured and Jailed"
authors: [ luis ]
tags: [ SSH, SFTP ]
categories: [ Linux, SysAdmin]
keywords: [ SSH, SFTP, Linux, Configuration, Jailed ]
date: 2015-03-31
publishDate: 2015-03-31
lastmod: 2019-11-12
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

To configure your server to use a jailed user on SFTP you should do:

1. Edit the sshd_config file
  
We need to comment the following line:

```shell
Subsystem sftp /usr/libexec/openssh/sftp-server
```

And add the uncomment line, your modification will be same as:

```shell
# Subsystem sftp /usr/libexec/openssh/sftp-server
Subsystem sftp internal-sftp
```

Also, at the end of the file we should to add the next lines:

```shell
Match Group sftponly
ChrootDirectory %h
X11Forwarding no
AllowTCPForwarding no
ForceCommand internal-sftp
```

After save all the changes, we must restart the sshd daemon

```shell
service sshd restart
```

2. Add **sftponly** group

```shell
groupadd sftponly
```

3. Add jailed user and add to **sftponly** group

```shell
useradd -m USERNAME
passwd USERNAME
usermod -aG sftponly,apache USERNAME
```

4. **IMPORTANT**: Create directory and establish correct permissions

```shell
chown root:root /home/USERNAME
chmod 755 /home/USERNAME
mkdir /home/USERNAME/TEST.DOMAIN.COM
chown apache:apache /home/USERNAME/TEST.DOMAIN.COM
chmod 775 /home/USERNAME/TEST.DOMAIN.COM
mkdir /var/www/vhost/TEST.DOMAIN.COM
chown apache:apache /var/www/vhost/TEST.DOMAIN.COM
chmod 775 /var/www/vhost/TEST.DOMAIN.COM
```

*If you have any connection problem please double check the permissions on the folders and check the logs on /var/log/secure*

```shell
tail -f /var/log/secure
```

5. Mount DocumentRoot path on jailed user home directory

```shell
mount -o bind,noatime /var/www/vhost/TEST.DOMAIN.COM/ /home/USERNAME/TEST.DOMAIN.COM
```

6. Make the mount point permanent, editing the fstab file:

```shell
vi /etc/fstab</pre>
```

Add the mount point at the end of the file:

```shell
/var/www/vhost/TEST.DOMAIN.COM/ /home/USERNAME/TEST.DOMAIN.COM none bind,noatime 0 0
```

Save and exit

7. Test connection:

```shell
sftp SERVERIP
```