---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Yosemite stuck on boot process"
url: "/yosemite-stuck-boot-process"
subtitle: "How to avoid that your MacOS Yosemite stuck on the boot process"
summary: "How to avoid that your MacOS Yosemite stuck on the boot process"
authors: [ luiscachog ]
tags: [ MacOS ]
categories: [ SysAdmin ]
keywords: [ MacOS, Yosemite, Stuck, Boot ]
date: 2015-01-08
publishDate: 2015-01-08
lastmod: 2019-11-08
featured: false
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

Sometimes, I'm having problems with my Mac, when it's sleep (hibernate) and I tried to "wake up" the Mac doesn't start, and it shows me a Black Screen. So, I've rebooted and after that it is stuck on the boot process.
  
So, I've found these solution to avoid that Yosemite stuck on the boot process:

A. Enter to Single-user or verbose mode
  
  1. Shutdown the Mac
  
  2. Press the power button to start the computer
  
  3. Immediately press and hold the Command Key and either of the following

    * the "s" key for single-user mode (Command-S)
    * the "v" key for verbose mode (Command-V)

B. When you login on the Mac, you should run the following commands:

```shell
/sbin/mount -uw /
rm -rf /System/Library/Caches/*
rm /private/var/db/BootCache.playlsit
reboot
```

After the reboot, your Mac will boot as always. 

🙂