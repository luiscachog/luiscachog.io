---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Set a hugo blog on Kubernetes"
url: "/blog-hugo-docker-k8s-quay"
subtitle: "Here are some steps to containerize a blog using hugo + docker + kubernetes + quay"
summary: "Here are some steps to containerize a blog using hugo + docker + kubernetes + quay"
authors: [ luiscachog ]
tags: [ Kubernetes ,DevOps, SysAdmin, Hugo, Docker , Git, Quay, Cloud Native]
categories: [ SysAdmin , DevOps, Open Source, Cloud Native]
keywords: [ Kubernetes, Quay, Docker, Cloud Native, Open Source, DevOps, SRE, Hugo ]
date: 2018-06-18
publishDate: 2018-06-18
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

{{% toc %}}

## Overview

Since last year I been trying to become an SRE (Site Reliability Engineer), so I been involved with some emerging technologies, like ansible, docker and on this time with kubernetes.

This time, I will try to explain how I containerized my blog using:

- [Hugo](https://gohugo.io/)
- [Docker](https://www.docker.com/)
- [Kubernetes](https://kubernetes.io/)
- [Quay](https://quay.io/)
- [Git](https://github.com)

### Architecture

So, I take some ideas from [here](https://danrl.com/blog/2017/my-blog-on-kubernetes/) and I modify them and adapt the architecture described to my options.

The principal changes that I made are:

- My Kubernetes cluster is running on 2 cloud server on Rackspace Public Cloud
- The container registry that I'm using is Quay
- Rackspace Public Cloud does not support a Kubernetes LoadBalancer service automatically,
  so I simulate that behavior adding a Cloud Load Balancer manually after the Kubernetes service provide me the port.

{{< figure src="posts/blog-hugo-docker-k8s-quay/architecture.png" caption="Architecture" id="blog-hugo-architecture" theme="ligth">}}

## Containerized

I use [Hugo](https://gohugo.io/) to deploy my blog, I used to do it as mentioned on [this](https://luiscachog.io/deployment-hugo-site-git-hooks/) previous post (In Spanish).

Now, as a part of containerize the blog it make sense to me to create two stages as described [here](https://danrl.com/blog/2017/my-blog-on-kubernetes/):

- The first stage is a defined build environment containing all required build tools (hugo, pygments) and the source of the website (Git repository).
- The second stage is the build artifact (HTML and assets), from the first stage and a webserver to serve the artifact over HTTP.

### Dockerfile

Here is the Dockerfile that containerize the blog:

```shell
FROM ubuntu:latest as STAGEONE

# install hugo
ENV HUGO_VERSION=0.41
ADD https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_${HUGO_VERSION}_Linux-64bit.tar.gz /tmp/
RUN tar -xf /tmp/hugo_${HUGO_VERSION}_Linux-64bit.tar.gz -C /usr/local/bin/

# install syntax highlighting
RUN apt-get update
RUN apt-get install -y python3-pygments

# build site
COPY source /source
RUN hugo --source=/source/ --destination=/public/

FROM nginx:stable-alpine
RUN apk --update add curl bash
RUN rm /etc/nginx/conf.d/default.conf
COPY modules/nginx.luiscachog.io.conf /etc/nginx/conf.d/luiscachog.io.conf
COPY --from=STAGEONE /public/ /usr/share/nginx/html/
EXPOSE 80 443

MAINTAINER Luis Cacho <luiscachog@gmail.com>
```

#### First Stage

- Fetch the lastest Ubuntu image and name as **STAGEONE**
- Install the last available **hugo** version from source.
- Install **pygments** library to use it for highlighting.
- Build the site with **hugo** and the output is set on **/public** as a build artifact.

#### Second Stage

- Fetch the lastest stable nginx alpine image.
- Update the image and install some utilities.
- Delete the **default** nginx configuration file.
- Copy the configuration files needed from the repository root directory.
- Copy the build artifact **/public** from the previous stage (**STAGEONE**)
