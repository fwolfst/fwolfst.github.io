---
layout: post
title:  "Installing mailtrain with dokku"
date:   2021-03-07 09:30:00 +0200
categories: devops dokku
toc: true
---

## Aim

* install [mailtrain](https://mailtrain.org) on a [dokku](dokku.com)

### Requirements

* reasonably fresh dokku host

## Why

Although an old craft, sending and receiving e-mail is not necessarily an easy
task. Many different clients shall render the result nicely and sending itself
needs some special care to not end up in spam folders.

There are multiple open source solutions to tackle the design/layout and
receipients management problems. One of them is
[mailtrain](https://mailtrain.org) which includes some relatively slick editor
tools (mosaico and grapejs) that allow users to achieve good results without
understanding much of HTML and CSS. The state of the solution itself is pretty
old, with a version 2 in beta since ever.

As [dokku](https://dokku.com) is a cool deployment solution, I'll outline the
process to install mailtrain on a dokku host here.

### Prepare the deployable code

#### Checkout the mailtrain code

```bash
git clone https://github.com/Mailtrain-org/mailtrain
cd mailtrain
```

#### Prepare the configuration file

```bash
cp config/default.toml config/production.toml
git add -f config/production.toml # attention, this file should never see the
                                  # public!
git commit -m "cp config/default.toml config/production.toml"

# Adjust basic values if you want
vim config/production.toml
git add config/production.toml
git commit -m "basic configuration"
```

### Prepare the dokku host

#### Create skeleton app

```bash
dokku apps:create mailtrain
```

#### MySQL

##### Plugin installation

If you have not yet install the dokku plugin, do so

```bash
dokku plugin:install https://github.com/dokku/dokku-mysql.git mysql
```

##### Create and link a database

```bash
dokku mysql:create mailtrain
dokku mysql:link mailtrain mailtrain
```

#### Redis

##### Plugin installation

If you have not yet install the dokku plugin, do so

```bash
dokku plugin:install https://github.com/dokku/dokku-redis.git redis
```

##### Create and link the redis database

```bash
dokku redis:create mailtrain
dokku redis:link mailtrain mailtrain
```

### Adjust the mailtrain configuration

Both the `redis:create/link` and the `mysql:create/link` commands executed
earlier create output containing host, name and credentials for the service.

If you cannot access the output anymore, `dokku redis:info mailtrain` (and
`dokku mysql:info mailtrain`) will provide it again (look for the `dsn` line).

Edit the mailtrain configuration accordingly:

```bash
vim config/production.toml
# [mysql] host,user,password,database
# [redis] host,password(uncomment)
git add
git commit -m "configure dokku redis and mysql databases"
```

### Add volumes

Create folders on host to mount in guest.

```bash
mkdir -p /var/lib/dokku/data/storage/mailtrain/grapejs_uploads/
mkdir -p /var/lib/dokku/data/storage/mailtrain/mosaico_uploads/
mkdir -p /var/lib/dokku/data/storage/mailtrain/reports/
```

```bash
dokku storage:mount mailtrain /var/lib/dokku/data/storage/mailtrain/reports:/app/protected/reports
dokku storage:mount mailtrain /var/lib/dokku/data/storage/mailtrain/grapejs_uploads:/app/public/grapejs/uploads
dokku storage:mount mailtrain /var/lib/dokku/data/storage/mailtrain/mosaico_uploads:/app/public/mosaico/uploads

# docker deploy needs containers permissions
chown -R dokku:dokku /var/lib/dokku/data/storage/mailtrain/
```

### Deploy

Now it is time to push your mailtrain instance for deployment:

```bash
# in mailtrain source
git remote add online dokku@yourdokkuhost:mailtrain
git push online master
```

#### Add a domain

If you want an additional domain, add it

```bash
dokku domains:add mailtrain newsletter.mydokku.host
```

#### letsencrypt it

The letsencrypt plugin needs [special treatment](https://github.com/dokku/dokku-letsencrypt#dockerfile-deploys) for Dockerfile deploys.

Following the readme, we map the ports for certificate fetching

```bash
# install, if you have not yet:
# dokku plugin:install https://github.com/dokku/dokku-letsencrypt.git

dokku proxy:ports-add mailtrain http:80:5555
dokku lentsencrypt mailtrain
dokku proxy:ports-add mailtrain https:443:3000
dokku proxy:ports-remove mailtrain http:5555:5555
```
<!--
Port mappings for mailtrain
scheme             host port                 container port                                     
http                      3000                      3000                                               
http                      80                        5555                                               
https                     3000                      3000                                               
https                     443                       3000                                               
https                     443                       5555 
-->

## Enjoy!

Your mailtrain instance should be up and running. Follow the [documentation](https://github.com/Mailtrain-org/mailtrain) to
configure it. The language can be changed in the `config/production.toml` file
only and the language code has to match the files in `languages`. Commit and
push to update the configuration.

## Final Thoughts

dokku will attempt a deployment based on the `Dockerfile` in the code
repository. This is fine, although the idea of even more standardized buildpacks
charms me more and leads to less hazzle (e.g. with letsencrypt setup).
However, I couldnt get the default buildpacks run mailtrain nicely. I would
blame the still very busy nodejs ecosystem (grunt, nodeversion, blabla).

#### Having a better idea?

Awesome!  Get in contact with me!
