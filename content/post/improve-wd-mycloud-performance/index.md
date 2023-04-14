---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Improve WD MyCloud performance"
url: "/improve-wd-mycloud-performance"
subtitle: "How to improve performance on a WD MyCloud EX2"
summary: "How to improve performance on a WD MyCloud EX2"
authors: [ luiscachog ]
tags: [ Network, Storage, SysAdmin, NAS ]
categories: [ SysAdmin, Linux, Storage ]
keywords: [WD MyCloud, NAS, Storage, Linux ]
date: 2018-02-16
publishDate: 2018-02-16
lastmod: 2021-05-01
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

I've just noticed that my NAS a [Western Digital My Cloud EX2](https://www.westerndigital.com/products/network-attached-storage/wd-my-cloud-expert-series-ex2-ultra#WDBVBZ0040JCH-NESN) is going slower,
so I decided to investigate about what can I do to improve its performance.

I assume that you already configure ssh on your NAS device.
If is not configured you can follow the next instructions: [https://support-en.wd.com/app/answers/detailweb/a_id/12861](https://support-en.wd.com/app/answers/detailweb/a_id/12861)

## Stop Indexing Services

```shell
/etc/init.d/wdmcserver stop
/etc/init.d/wdphotodbmerger stop
```

To do it forever, you should create the cronjob as:

```shell
crontab -e
```

And add the following lines:

```shell
@reboot /bin/sh /etc/init.d/wdmcserverd stop
@reboot /bin/sh /etc/init.d/wdphotodbmerger stop
```

**References:**

- Western Digital knowledge base link[^1]

[^1]: [How to enable SSH (Secure Shell) on a My Cloud EX2 device](https://support-en.wd.com/app/answers/detailweb/a_id/12861)
