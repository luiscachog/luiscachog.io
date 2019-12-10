---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Spamassassin Error: cannot create user preferences file //.spamassassin/user_prefs: Permission denied on VestaCP - CentOS"
url: "/spamassassin-error-on-vestacp-centos"
subtitle: "How to fix an Spamassasin Error on a VestaCP"
summary: "How to fix an Spamassasin Error on a VestaCP"
authors: [ luis ]
tags: [ Email, Exim, Spamassassin, VestaCP ]
categories: [ Linux, SysAdmin ]
keywords: [Email, Exim, Spamassassin, VestaCP, Linux ]
date: 2015-04-08
publishDate: 2015-04-08
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

When you configure spamassassin on VestaCP (CentOS) sometimes you might have some problems with the autolearn feature and also with the bayes plugin of spamassassin.
  
The error looks like:

```shell
more /var/log/maillog

Apr  5 00:31:00 vestaserver01 spamd[1353]: spamd: connection from localhost [127.0.0.1] at port 37022
Apr  5 00:31:00 vestaserver01 spamd[1353]: spamd: setuid to nobody succeeded
Apr  5 00:31:00 vestaserver01 spamd[1353]: spamd: creating default_prefs: //.spamassassin/user_prefs
Apr  5 00:31:00 vestaserver01 spamd[1353]: config: cannot create user preferences file //.spamassassin/user_prefs: No such file or directory
Apr  5 00:31:00 vestaserver01 spamd[1353]: spamd: failed to create readable default_prefs: //.spamassassin/user_prefs
Apr  5 00:31:00 vestaserver01 spamd[1353]: spamd: checking message &lt;5520C87B.8020009@example.com&gt; for nobody:99
Apr  5 00:31:00 vestaserver01 spamd[1353]: plugin: eval failed: bayes: (in learn) locker: safe_lock: cannot create tmp lockfile /.spamassassin/bayes.lock.vestaserver01.example.com.1353 for /.spamassassin/bayes.lock: No such file or directory
Apr  5 00:31:00 vestaserver01 spamd[1353]: spamd: clean message (-1.0/5.0) for nobody:99 in 0.2 seconds, 3138 bytes.
Apr  5 00:31:00 vestaserver01 spamd[1353]: spamd: result: . 0 - ALL_TRUSTED,HTML_MESSAGE scantime=0.2,size=3138,user=nobody,uid=999,required_score=5.0,rhost=localhost,raddr=127.0.0.1,rport=37022,mid=&lt;5520C87B.8020009@example.com&gt;,autolearn=unavailable
```

Basically the error are the permissions on: //.spamassassin/user_prefs

To fix it follow the next steps:

1. Create the user spamd, in order to avoid to run spamassassin with the user nobody:

```shell
groupadd -g 1001 spamd
useradd -u 1001 -g spamd -s /sbin/nologin -d /var/lib/spamassassin spamd
mkdir /var/lib/spamassassin
chown spamd:spamd /var/lib/spamassassin</pre>
```

2. Edit the file /etc/exim/exim.conf.

```shell
vi /etc/exim/exim.conf
```

Change the line:

```shell
spam           = nobody:true/defer_ok
```

to

```shell
spam           = spamd:true/defer_ok
```

3. Restart exim an spamassassin

```shell
/etc/init.d/exim restart 
/etc/init.d/spamassassin restart
```

4. After that verify that the files **bayes_seen, bayes_toks and user_prefs** exists on the spamd home (In this case /var/lib/spamassassin)

```shell
pwd
/var/lib/spamassassin
ls -la
total 40
drwxr-xr-x  3 spamd spamd  4096 Apr  7 17:58 .
drwxr-xr-x 36 root  root   4096 Feb 25 00:56 ..
-rw-------  1 spamd spamd 12288 Apr  2 21:34 bayes_seen
-rw-------  1 spamd spamd 12288 Apr  2 17:34 bayes_toks
-rw-r--r--  1 spamd spamd  1869 Apr  1 17:18 user_prefs
```

Done!