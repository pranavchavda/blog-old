+++
title= "Static Hugo site hosted on s3 with https using apache"
dat= 2020-02-05T07:03:34+05:30
draft= false
author= "Pranav Chavda"
cover=  "/img/ssg.png"
tags= ["blog, first"]
keywords= ["first blog post"]
description= "use an linode with apache to pass https to a static site hosted on s3"
showFullContent= false
+++

![https][https://upload.wikimedia.org/wikipedia/commons/thumb/d/da/Internet2.jpg/220px-Internet2.jpg]

[Linode](https://www.linode.com/) came up with their version of Amazon S3 recently. Object storage is a great way to host a static site. You don't need a server and sites are super easy to deploy. A problem with hosting with object storage though, is that https is not natively supported. 

AWS users have the option of using cloudfront. This was an option for me, but I didn't want to use two separate providers for one website. What I eneded up doing was kind of ironic. I created a linode with apache and used it as a reverse proxy to point to my object storage bucket.

## Here's what I did

1. Followed the guide at https://www.linode.com/docs/platform/object-storage/host-static-site-object-storage/ to set up my object storage bucket as a website and deploy my Hugo site there.

2. Fired up a new Linode instance - the lowest config option available so I don't spend more than $5/month on it. 

3. Installed Apache
```
$ apt-get install apache2
```
4. Install certbot
```
$ apt-get install certbot python-certbot-apache
```
I didn't have to mess with PPAs, etc - this was already on a debian repo

5. created a new file under /etc/apache2/sites-available called superauto.conf and added the following to it

```
<VirtualHost *:80>
 ServerName superautomatic.in
 ProxyPreserveHost Off 
 DocumentRoot /var/www/html
 ProxyPass / http://location of my bucket/
 ProxyPassReverse / http://location of my bucket/
</VirtualHost>
```
6. On the server, ran certbot to install the certificate via let's encrypt
```
$ certbot --apache
```
7. Entered my email and restarted apache after answering a couple of questions for the certificate

8. https://superautomatic.in is SSL encrypted and is hosted on an object stored bucket.

Now as I mentioned above, it is kind of counter-intuitive to first host your static site on an s3 bucket and then use a server to give it SSL. there are other ways to do it, this is quick and easy. Especially if you are going to use a server for other websites or purposes or have one lying around, a proxy pass entry in apache for a domain won't consume any resources on your server, it will just encrypt and pass the traffic over to the actual host - your s3 bucket.