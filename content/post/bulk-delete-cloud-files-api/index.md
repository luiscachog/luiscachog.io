---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Bulk Delete Rackspace Cloud Files data via API"
url: "/bulk-delete-cloud-files-api"
subtitle: "How to delete the Data and Cloud Files Containers using Rackspace Cloud Files API, cURL and Turbolift"
summary: "How to delete the Data and Cloud Files Containers using Rackspace Cloud Files API, cURL and Turbolift"
authors: [ luiscachog ]
tags: [ Openstack, DevOps, SysAdmin, Turbolift, Open Source, Rackspace, Cloud Files ]
categories: [ SysAdmin , DevOps, Open Source, Rackspace ]
keywords: [ Openstack, DevOps, SysAdmin, Turbolift, Open Source, Cloud Files ]
date: 2019-02-13
publishDate: 2019-02-13
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

Sometimes it is necessary to delete all the content of the Cloud Files containers, however, the API does not have a proper method to delete the data and the containers on the same API call.
Also, accoring to the documentation, you can only delete **empty** containers.

So, in cases where you need to delete the **data and the containers** at the same time, you should follow the next steps:

1. Download [Turbolift](https://github.com/cloudnull/turbolift), I know it is an old tool.

    ```shell
    git clone https://github.com/cloudnull/turbolift
    cd turbolift
    ```

1. In order to get and isolated installation, we are going to create a Python Virtual Environment (virtualenv)

    ```shell
    mkvirtualenv turbolift
    workon turbolift
    ```

1. Install the tool

    ```shell
    pip install turbolift
    ```

1. Now, prior to start to play with the API calls, we need to grab some data to authenticate with the API:

    | Variable | Definition |
    |---|---|
    | USERNAME | This is the Rackspace Public Cloud username |
    | APIKEY | This is your API-KEY |
    | REGION | This is the Region where the Cloud Files are located (dfw, ord, iad, lon, hkg) |
    | TOKEN | The TOKEN is generated after you get authenticated |
    | ENDPOINT | This ENDPOINT is given also after you get authenticated  |

1. Next step, we are going to use [cURL](https://curl.haxx.se/), to perform all the API calls:

    * First of all, get the TOKEN:

    ```shell
    USERNAME=YOUR-USERNAME
    APIKEY=YOUR-APIKEY
    TOKEN=$(curl -s -XPOST https://identity.api.rackspacecloud.com/v2.0/tokens \
          -d'{"auth":{"RAX-KSKEY:apiKeyCredentials":{"username":"'$USERNAME'","apiKey":"'$APIKEY'"}}}' \
          -H"Content-type:application/json" | jq '.access.token.id' | tr -d "\"")
    ```

    * Next step, get the ENDPOINT:

    ```shell
    ENDPOINT=$(curl -s -XPOST https://identity.api.rackspacecloud.com/v2.0/tokens \
            -d'{"auth":{"RAX-KSKEY:apiKeyCredentials":{"username":"'$CL_USERNAME'","apiKey":"'$APIKEY'"}}}' \
            -H"Content-type:application/json" | jq '.access.serviceCatalog[] | select((.name=="cloudFiles") or (.name=="cloudFilesCDN")) | {name} + .
            endpoints[] | .publicURL' | tr -d "\"" | grep -v cdn | grep -i $REGION)
    ```

    In this case we are skipping all te CDN endpoints, but you can add them if is necessary.

1. With all the collected data, next step is use turbolift to delete the Cloud Files container and their data. To do it, I use a for-loop:

    ```shell
    for i in $(curl -s -H "X-Auth-Token: $TOKEN" $ENDPOINT); do turbolift -u $USERNAME -a $APIKEY --os-rax-auth $REGION delete -c $i ; done
    ```

Now, you have all the Data and Cloud Files containers deleted on one region.

:smile:
