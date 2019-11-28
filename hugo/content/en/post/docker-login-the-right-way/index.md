---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Docker Login the Right Way"
subtitle: "How to configure a docker-credential-helpers to keep docker credentials safe on Linux"
summary: "How to configure a docker-credential-helpers to keep docker credentials safe on Linux"
url: "/docker-login-the-right-way"
authors: [ luis ]
tags: ["DevOps","SysAdmin","Docker", "Open Source", "Linux", "Security", Cloud Native]
categories: ["SysAdmin","DevOps", "Open Source","Docker", "Linux","Security", "Cloud Native"]
date: 2019-05-15T13:58:42-06:00
publishDate: 2019-05-15T13:58:42-06:00
lastmod: 2019-11-27T13:58:42-06:00
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

{{% toc %}}

# Docker Login the right Way

Hi again!

It is been a while since I wrote something here, as always, there is no much time for a hobby.

I've been working for a while with docker, not a production level, but for some applications that I use at work. 
And since the [Docker Hub Data breach](https://www.theinquirer.net/inquirer/news/3074793/docker-hub-breach) I put more atention on the security of my data/credentials, so I investigate a little about and found this official repository [https://github.com/docker/docker-credential-helpers/](https://github.com/docker/docker-credential-helpers/) from Docker where are the supported credential helpers.

## Credential Store

Docker keeps our credentials saved on a JSON file located on ```~/.docker/config.json```, but unfortunatelly credentials are just encrypted on base64, here is an [articule/video](https://fosdem.org/2019/schedule/event/base64_not_encryption/) where there is an explanation for the why it is a bad idea to just use base64 encryption.  

The following is a diagram on how a plain text storage works:

![Plain Text Storage](/img/posts/docker-login-the-right-way/DockerPlainTextCredentials.png)

Here is an exampleon how ```~/.docker/config.json``` looks like when is using plain text credentials:

```shell
cat ~/.docker/config.json
{
	"auths": {
		"https://index.docker.io/v1/": {
      "auth": "azRjaDA6c3VwZXJzZWNyZXRwYXNzd29yZAo="
    },
    "quay.io": {
      "auth": "azRjaDA6c3VwZXJzZWNyZXRwYXNzd29yZAo="
    }
	},
	"HttpHeaders": {
		"User-Agent": "Docker-Client/18.09.6 (linux)"
	}
} 
```

After a successful ```docker login``` command, Docker stores a base64 encoded string from the concatenation of the username, a colon, and the password and associates this string to the registry the user is logging into:

```shell
$ echo azRjaDA6c3VwZXJzZWNyZXRwYXNzd29yZAo= | base64 -d -
k4ch0:supersecretpassword
```

A ```docker logout``` command removes the entry from the JSON file for the given registry:

```shell
$ docker logout quay.io
Remove login credentials for quay.io

$ cat ~/.docker/config.json
{
	"auths": {
		"https://index.docker.io/v1/": {
      "auth": "azRjaDA6c3VwZXJzZWNyZXRwYXNzd29yZAo="
    }
	},
	"HttpHeaders": {
		"User-Agent": "Docker-Client/18.09.6 (linux)"
	}
}
```

## Docker Credential Helpers

Since docker version 1.11 implements support from an external credential store for registry authentication. That means we can use a native keychain of the OS. Using an external store is more secure than storing on a "plain text" Docker configuration file.  



![Secure Storage](/img/posts/docker-login-the-right-way/DockerSecureCredentials.png)

In order to use a external credential store, we need a program to interact with.

The actual list of "official" Docker Credential Helper is:

1. docker-credential-osxkeychain: Provides a helper to use the OS X keychain as credentials store.
2. docker-credential-secretservice: Provides a helper to use the D-Bus secret service as credentials store.
3. docker-credential-wincred: Provides a helper to use Windows credentials manager as store.
4. docker-credential-pass: Provides a helper to use pass as credentials store.

## docker-credential-secretservice 

On this post we will explore the docker-credential-secretservice and how to configure it.

1. We need to download and install the helper. 
You can find the lastest release on  [https://github.com/docker/docker-credential-helpers/releases](https://github.com/docker/docker-credential-helpers/releases).  
Download it, extract it and make it executable.

```shell
wget https://github.com/docker/docker-credential-helpers/releases/download/v0.6.2/docker-credential-secretservice-v0.6.2-amd64.tar.gz
tar -xf docker-credential-secretservice-v0.6.2-amd64.tar.gz
chmod +x docker-credential-secretservice
sudo mv docker-credential-secretservice /usr/local/bin/
```

2. Then, we need to specify the credential store in the file ```~/.docker/config.json``` to tell docker to use it.  
The value must be the one after the prefix ```docker-credential-```. In this case:

```json
{
	"credsStore": "secretservice"
}
```
To facilite the configuration and do not make mistakes, you can run:

```shell
sed -i '0,/{/s/{/{\n\t"credsStore": "secretservice",/' ~/.docker/config.json
```

From now we are uning an external store, so if you are currently logged in, you must run ```docker logout``` to remove the credentials from the file and run ```docker login``` tostart using the new ones.

Let me know how this works for you.

References:
[https://github.com/docker/docker-credential-helpers](https://github.com/docker/docker-credential-helpers)  
[https://docs.docker.com/engine/reference/commandline/login/#credentials-store](https://docs.docker.com/engine/reference/commandline/login/#credentials-store)  
[https://www.slideshare.net/DavidYeung22/can-we-stop-saving-docker-credentials-in-plain-text-now](https://www.slideshare.net/DavidYeung22/can-we-stop-saving-docker-credentials-in-plain-text-now)