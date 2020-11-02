---
layout: post
title:  "Rails 6 and vendor sass assets"
date:   2020-06-17 11:01:25
categories: rails assets snack
tags: rails css
---

## Aim

* make [bulma](bulma.io) available to and customizable via the Rails assets pipeline

### Requirements

* Rails6 setup
* tolerance: probably we will place files where they do not belong

## Why

In Bulma 0.9 there is a new "spacing feature" that I happen to need right
now. Or a least, it would feel stupid not to use bulma for that. The
bulma-rails gem still carries the version 0.8 of bulma.

## Aside: Why not using a gem?

I read quite some comments and articles argueing that gems that bundle assets
like javascript or css-libraries should be abandoned in favor of webpack / yarn
/ npm.

Letting a third party control your assets (let it be CDNs, npm or rubygems) has
benefits and downsides.  I prefer others to iron out smaller bugs and keep the
libraries up to date.  But I dislike dependance on additional "packaging
software" and infrastructure as well as potentially more involved deployment
processes.

For the [bulma css framework](bulma.io), I usually reach out to
[bulma-rails](https://github.com/joshuajansen/bulma-rails). Having gemified
assets also has the benefit of a single point where to raise issues or provide
examples that might originate from the combination of the technology (e.g. how
to integrate to sprockets, or use `dom_id`, ...).  Also, installation is often
trivial (one line in `Gemfile` and one somewhere for asset integration).

Bottom line: I am pro assets-as-a-gem. Which does not mean that
`this is the way`.

## Installing assets

So, I want to get rid of the gem for the time being (will re-enable it once 0.9
is out) and put the original SASS assets from bulma in my asset pipeline.

### Download the sass

```bash
# Let us grab the tarball from the release page:
# https://github.com/jgthms/bulma/releases
cd /tmp
wget https://github.com/jgthms/bulma/archive/0.9.0.tar.gz
tar xzf 0.9.0.tar.gz
```

### Move the sass to `app/assets/vendor/bulma-0.9.0`

Assuming your project lives in `/home/projects/project`.

```bash
BULMA_VENDOR_ASSET_DIR=/home/projects/project/app/assets/vendor/bulma-0.9.0
mkdir -p "$BULMA_VENDOR_ASSET_DIR"
cp -ar /tmp/bulma-0.9.0/bulma.sass "$BULMA_VENDOR_ASSET_DIR"
cp -ar /tmp/bulma-0.9.0/sass "$BULMA_VENDOR_ASSET_DIR"
```

### Include the SASS assets

```bash
git mv app/assets/stylesheets/application.{,s}css
```

And in that file

```scss
@import "bulma-0.9.0/bulma";
```

### That's it

I can't really believe it, but more is not to be done.

#### Having a better idea?

Awesome!  Get in contact with me!

