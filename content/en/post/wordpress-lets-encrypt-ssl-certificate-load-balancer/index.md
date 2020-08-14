---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "WordPress with Let's Encrypt SSL Certificate on a Load Balancer"
url: "/wordpress-lets-encrypt-ssl-certificate-load-balancer"
subtitle: "How to configure Wordpress with a Let's Encrypt SSL Certificate on a Load Balancer"
summary: "How to configure Wordpress with a Let's Encrypt SSL Certificate on a Load Balancer"
authors: [ luiscachog ]
tags: [ Wordpress, SSL, Let's Encrypt, LoadBalancer, Rackspace Public Cloud, DevOps]
categories: [ DevOps, Linux, SysAdmin, Rackspace]
keywords: [ Wordpress, SSL, Let's Encrypt, LoadBalancer, Rackspace, Cloud, DevOps]
date: 2017-09-03
publishDate: 2017-09-03
lastmod: 2019-11-27
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

Hi again,

As many of you know a lot of "Production" applications need to be configured to provide High Availability. With that in mind, a best practice architecture to your application is to add a Load Balancer as a front end who distribute your traffic between your application nodes, as you can appreciate on the next image:

![Load Balancer HA](/img/posts/wordpress-lets-encrypt-ssl-certificate-load-balancer/LoadBalancerHA.png)


# SSL Offloading

In this case, my "Production" application is my blog, and I will install a SSL Certificate on the Cloud Load Balancer(CLB) to offloading the encryption/decryption to the CLB instead of doing it on the webserver. That way your webservers uses port 80 (HTTP), as always, and you serve your content trought port 443(HTTPS).

![SSL-Offloading](/img/posts/wordpress-lets-encrypt-ssl-certificate-load-balancer/SSL-Offloading.jpg)

Here are the what I use to configure my WordPress with SSL Certificate:

  - SSL Certificate issued using Let's Encrypt
  - A Client of Let's Encrypt called acme
  - A Cloud Load Balancer
  - A WordPress installation

## Step 1: Install acme.sh client

There is a lot of [ACME clients](https://letsencrypt.org/docs/client-options/) supported by Let's Encrypt, the most popular is [Certbot.](https://certbot.eff.org) However, I prefer to use [acme.sh](https://github.com/Neilpang/acme.sh).

Let's install it:

```shell
git clone https://github.com/Neilpang/acme.sh.git
cd acme.sh
# Create a data home directory
sudo mkdir -p /opt/acme/data
# Actual command to install it
bash acme.sh --install --home /opt/acme --config-home /opt/acme/data --certhome /opt/acme/data/ssl-certs --accountemail your@email.com
```

## Step 2: Issue SSL Certificate

Once acme.sh is installed, we proceed to issue our first SSL Certificate:

```shell
/opt/acme/acme.sh --issue -d example.com -w /var/www/vhosts/example.com/public_html
[Mon Aug 25 06:04:07 UTC 2017] Creating domain key
[Mon Aug 25 06:04:07 UTC 2017] The domain key is here: /opt/acme/data/ssl-certs/example.com/example.com.key
[Mon Aug 25 06:04:07 UTC 2017] Single domain='example.com'
[Mon Aug 25 06:04:07 UTC 2017] Getting domain auth token for each domain
[Mon Aug 25 06:04:07 UTC 2017] Getting webroot for domain='example.com'
[Mon Aug 25 06:04:07 UTC 2017] Getting new-authz for domain='example.com'
[Mon Aug 25 06:04:08 UTC 2017] The new-authz request is ok.
[Mon Aug 25 06:04:08 UTC 2017] Verifying:example.com
[Mon Aug 25 06:04:11 UTC 2017] Success
[Mon Aug 25 06:04:11 UTC 2017] Verify finished, start to sign.
[Mon Aug 25 06:04:11 UTC 2017] Cert success.
-----BEGIN CERTIFICATE-----
MIIE/zCCA+egAwIBAgISA2AIs/G8gWjkRkNOUb7zmqh1MA0GCSqGSIb3DQEBCwUA
MEoxCzAJBgNVBAYTAlVTMRYwFAYDVQQKEw1MZXQncyBFbmNyeXB0MSMwIQYDVQQD
ExpMZXQncyBFbmNyeXB0IEF1dGhvcml0eSBYMzAeFw0xNzA4MjgwNTA0MDBaFw0x
NzExMjYwNTA0MDBaMBkxFzAVBgNVBAMTDmNvb2tpZWxhYnMubmV0MIIBIjANBgkq
hkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAo8/4fXH0dOHcSlyXpsBoULhwQYkz4m0J
MegRHU2mhyy/jfKWM6KHDxHpFWUFajLJ/ORE4uncvjmRYeSVBxgv2R2cYoZyKd6v
txT+Cdj3jD9fBfDerfdfsdfsd6Y6mlr6Im61afKsFXIgLsprBpK22JU6HOz+0Fdo
lan09aaF8zLPtVzdfJw9MU55K7nzerxO8j4ro2lve0PHExkMIBCrXey50wcuqQRY
hwkbbXsm+wTES7TCn3tooSzFq6ore3JrSckxhFQ96EOea0s9CgYnw4d9rU/b3jyK
bFCILEJK64vgFHx0qvd0hBJFJG/HUtAXAVrFQjjlZlCmCMbnee1UTQIDAQABo4IC
DjCCAgowDgYDVR0pasoasoasogWgMB0GA1UdJQQWMBQGCCsGAQUFBwMBBggrBgEF
BQcDAjAMBgNVHRMBAf8EAjAAMB0GA1UdDgQWBBR2KRpXKKgTorwfXpo44wgKyFUl
QzAfBgNVHSMEGDAWgBSoSmpjBH3duubRObemRWXv86jsoTBvBggrBgEFBQcBAQRj
MGEwLgYIKwYBBQUHMAASdTdddHA6Ly9vY3NwLmludC14My5sZXRzZW5jcnlwdC5v
cmcwLwYIKwYBBQUHMAKGI2h0dHA6Ly9jZXJ0LmludC14My5sZXRzZW5jcnlwdC5v
cmcvMBkGA1UdEQQSMBCCDmNvb2tpZWxhYnMubmV0MIH+BgNVHSAEgfYwgfMwCAYG
Z4EMAQIBMIHmBgsrBgEEAYLfEwEBATCB1jAmBggrBgEFBQcCARYaaHR0cDovL2Nw
cy5sZXRzZW5jcnlwdC5vcmcwgasGCCsGAQUFBwICMIGeDIGbVGhpcyBDZXJ0aWZp
Y2F0ZSBtYXkgb25seSBiZSByZWxpZWQgdXBvbiBieSBSZWx5aW5nIFBhcnRpZXMg
YW5kIG9ubHkgaW4gYWNjb3JkYW5jZSB3aXRoIHRoZSBDZXJ0aWZpY2F0ZSBQb2xp
Y3kgZm91bmQgYXQgaHR0cHM6Ly9sZXRzZW5jcnlwdC5vcmcvcmVwb3NpdG9yeS8w
DQYJKoZIhvcNAQELBQADggEBAFVGs82tzyVER6U0x7p/Q+6xplDFd6ap/dVX9G6i
eRPf4ayGykPSH9J3ewu398LOQd3DE93oWbqc7PfEC40Z5HqvCEY3fl9auep99/IF
rwhf36J7PXvEsPrUB6pxNFSBw9WX366Z1MP8qoIzm3XYEpp2D/SPniWY5+eQ42Pj
WNxxVksA4kFUF9wgKcrsCNTm0X8GZj5HUXC1OwtlopY2w42QrAMGwz1jM4nxv5Mc
Jim+nT0zmJUhAdQi8ocDjAl2PvcfdgfmkMr9IWH3al/GJSKy3a9Cq+BaIsIUYi6E
8M8Mj+00ONNn1folm9aVn+FW5fVCaxYN32ir8PnoTWkOXK8=
-----END CERTIFICATE-----
[Mon Aug 25 06:04:11 UTC 2017] Your cert is in  /opt/acme/data/ssl-certs/example.com/example.com.cer 
[Mon Aug 25 06:04:11 UTC 2017] Your cert key is in  /opt/acme/data/ssl-certs/example.com/example.com.key 
[Mon Aug 25 06:04:11 UTC 2017] The intermediate CA cert is in  /opt/acme/data/ssl-certs/example.com/ca.cer 
[Mon Aug 25 06:04:11 UTC 2017] And the full chain certs is there:  /opt/acme/data/ssl-certs/example.com/fullchain.cer 
```

Where the explained options are:
  
-issue: Issue a new certificate
  
-d (-domain) : Specifies a domain, used to issue, renew or revoke, etc.
  
-w (-webroot) : Specifies the web root folder for web root mode. This is the DocumentRoot where your site is hosted and it is necessary to verify it by Let's Encrypt.

## Step 3: Install SSL Certificate on Cloud Load Balancer

So, at this moment we have our SSL Certificate, Private Key, and Intermediate CA Certificate ready to install on our Cloud Load Balancer (CLB)

```shell
Your cert is in /opt/acme/data/ssl-certs/example.com/example.com.cer
Your cert key is in /opt/acme/data/ssl-certs/example.com/example.com.key
The intermediate CA cert is in /opt/acme/data/ssl-certs/example.com/ca.cer
```

So we should go to https://mycloud.rackspace.com -> Rackspace Cloud -> Networking -> Cloud Load Balancers:

![Cloud Load Balancer](/img/posts/wordpress-lets-encrypt-ssl-certificate-load-balancer/CLB01.png)


Then, to Optional Features and Enable/Configure on "Secure Traffic SSL"

![Cloud Load Balancer](/img/posts/wordpress-lets-encrypt-ssl-certificate-load-balancer/CLB02.png)

Finally, we add our SSL Certificate, Private Key, and Intermediate CA Certificate to the CLB and save the configuration:

![Cloud Load Balancer](/img/posts/wordpress-lets-encrypt-ssl-certificate-load-balancer/CLB03.png)


## Step 4: Configure WordPress

We are almost done, at this time we already have configured our SSL on the CLB to provide WordPress over HTTPS, however, WordPress is still with HTTP, so we need to reconfigure our WordPress with SSL.

### Database queries

First of all, we should update the links from http to https; we are going to do it directly on the database doing the following queries:

**Warning:** Change all instances of `example.com` to your own. If you have the `www` as part of your WordPress Address(URL) in the WordPress Settings, add the 'www'.
  
Also, if you have a custom table prefix in the WordPress database, something other than the default 'wp_', then you must change all the instances of 'wp_' to your own table prefix.

1. Update any embedded attachments/img that use http:This one updates the src attributes that use double quotes: 

```sql
UPDATE `wp_posts` SET post_content = REPLACE(post_content, 'src=\"http://example.com', \ 
'src=\"https://example.com') WHERE post_content LIKE '%src=\"http://example.com%';
```

    This one takes care of any src attributes that use single quotes:

    ```sql
    UPDATE `wp_posts` SET post_content = REPLACE(post_content, 'src=\'http://example.com', \ 
    'src=\'https://example.com') WHERE post_content LIKE '%src=\'http://example.com%';
```

2. Update any hard-coded URLs for links.This one updates the URL for href attributes that use double quotes: 

```sql
UPDATE `wp_posts` SET post_content = REPLACE(post_content, 'href=\"http://example.com', \
'href=\"https://example.com') WHERE post_content LIKE '%href=\"http://example.com%';
```

    This one updates the URL for href attributes that use single quotes:

    ```sql
    UPDATE `wp_posts` SET post_content = REPLACE(post_content, 'href=\'http://example.com', \
    'href=\'https://example.com') WHERE post_content LIKE '%href=\'http://example.com%';
    ```

3. Update any "pinged" links:

```sql
UPDATE `wp_posts` SET pinged = REPLACE(pinged, 'http://example.com', \
'https://example.com') WHERE pinged LIKE '%http://example.com%';
```

4. This step is just a confirmation step to make sure that there are no remaining http URLs for your site in the wp_posts table, except the GUID URLs.
  
    You must replace WP\_DB\_NAME, near the beginning of the query, with the name of your database.
  
    This will confirm that nowhere in the wp_posts table is there a remaining http URL, outside of the GUID column. This ignores URLs in the GUID column.
  
    This query only searches; it does not replace anything, nor make any changes. So, this is safe to run. It’s a safe and quick way to check the wp_posts table while ignoring the guid column.
  
    This SQL query should return an empty set. That would mean that it found no http URLs for your site. (This is all just 1 query. It’s 1 very, very long line.)
  
    **Warning: **Remember to replace WP\_DB\_NAME, near the beginning of the query, with the name of your database.

    ```sql
    SELECT *  FROM `WP_DB_NAME`.`wp_posts` WHERE (CONVERT(`ID` USING utf8) LIKE \
    '%%http://example.com%%' OR CONVERT(`post_author` USING utf8) LIKE '%%http://example.com%%' \ 
    OR CONVERT(`post_date` USING utf8) LIKE '%%http://example.com%%' \ 
    OR CONVERT(`post_date_gmt` USING utf8) LIKE '%%http://example.com%%' \
    OR CONVERT(`post_content` USING utf8) LIKE '%%http://example.com%%' \
    OR CONVERT(`post_title` USING utf8) LIKE '%%http://example.com%%' \ 
    OR CONVERT(`post_excerpt` USING utf8) LIKE '%%http://example.com%%' \
    OR CONVERT(`post_status` USING utf8) LIKE '%%http://example.com%%' \
    OR CONVERT(`comment_status` USING utf8) LIKE '%%http://example.com%%' \
    OR CONVERT(`ping_status` USING utf8) LIKE '%%http://example.com%%' \
    OR CONVERT(`post_password` USING utf8) LIKE '%%http://example.com%%' \
    OR CONVERT(`post_name` USING utf8) LIKE '%%http://example.com%%' \
    OR CONVERT(`to_ping` USING utf8) LIKE '%%http://example.com%%' \
    OR CONVERT(`pinged` USING utf8) LIKE '%%http://example.com%%' \
    OR CONVERT(`post_modified` USING utf8) LIKE '%%http://example.com%%' \
    OR CONVERT(`post_modified_gmt` USING utf8) LIKE '%%http://example.com%%' \
    OR CONVERT(`post_content_filtered` USING utf8) LIKE '%%http://example.com%%' \
    OR CONVERT(`post_parent` USING utf8) LIKE '%%http://example.com%%' \
    OR CONVERT(`menu_order` USING utf8) LIKE '%%http://example.com%%' \
    OR CONVERT(`post_type` USING utf8) LIKE '%%http://example.com%%' \
    OR CONVERT(`post_mime_type` USING utf8) LIKE '%%http://example.com%%' \
    OR CONVERT(`comment_count` USING utf8) LIKE '%%http://example.com%%');
    ```

5. Now, we move to the wp_comments table. This changes any comment author URLs that point to the http version of your site. This is in case you’ve ever replied to a comment while your URL was pointing to http. 

```sql
UPDATE `wp_comments` SET comment_author_url = REPLACE(comment_author_url, \ 
'http://example.com', 'https://example.com') WHERE comment_author_url \ 
LIKE '%http://example.com%';
```

6. This updates the content of the comments on your site. If there are any links in the comments that are linking to an http URL on your site, they will be updated to https. 

```sql
UPDATE `wp_comments` SET comment_content = REPLACE(comment_content, 'http://example.com', \ 
'https://example.com') WHERE comment_content LIKE '%http://example.com%';
```

7. Now we move to the wp_postmeta table. This takes care of any custom post meta that points to the http version of your site. 

```sql
UPDATE `wp_postmeta` SET `meta_value` = REPLACE(meta_value, 'http://example.com', \ 
'https://example.com') WHERE meta_value LIKE '%http://example.com%';
```

8. Now we move to the wp_options table. Update the “WordPress Address (URL)” and “Site Address (URL)”.

    For the WordPress Address URL, you may have to modify example.com. If you have WordPress installed in some other directory, then modify this according to your own WordPress URL. For example, some people have WordPress installed in a subdirectory named “blog”, and so their WordPress Address would be https://example.com/blog.

    ```sql
    UPDATE `wp_options` SET `option_value` = "https://example.com" \ 
    WHERE `wp_options`.`option_name` = 'siteurl';
    ```

    This one will update the Site Address URL (this is the home page of your site):

    ```sql
    UPDATE `wp_options` SET `option_value` = "https://example.com" \
    WHERE `wp_options`.`option_name` = 'home';
  ```

### WordPress Control Panel

Besides, with run the queries directly on the database, we can update, or verify,  the blog URLs, by going to Settings > General
  
And updating your WordPress Address (URL) and Site Address (URL) address fields.

![Updating URLs](/img/posts/wordpress-lets-encrypt-ssl-certificate-load-balancer/WP-ChangeURL.png)

### WordPress Config File

Finally, we should add the following line to our **wp_config.php** file

```shell
$_SERVER['HTTPS']='on';
```

Now, you have configured WordPress with Let's Encrypt SSL Certificate on a Load Balancer.
