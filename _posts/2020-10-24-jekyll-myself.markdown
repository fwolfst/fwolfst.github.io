---
layout: post
title:  "Welcome to Jekyll II"
date:   2020-10-23 17:44:02 +0200
categories: jekyll ruby
toc: true
---

A while ago I wanted to revamp my blog a little, add some more flavor for professionals and
open it up to put some smaller tipps ("snacks") there.

The blog is realized with [jekyll](jekyllrb.com) and after browsing some
pre-made "themes" I stumbled upon Cotes Chungs [chirpy](https://github.com/cotes2020/jekyll-theme-chirpy). Looked nice, fancy features, active dev. However, it is full of client-side rendering and phoning-home stuff (all assets come via CDNs etc).

I throwed some patches at it and went on with other projects. Now, a couple of
month later I come back to Jekyll and my blog. Initially I wanted to go all
Chirpy but realized that dev there is too fast and it wouldnt be easy to create
and keep up a fork of my taste.

> So, let's start from scratch.

## Learning goals

I had several itches:

  * playing with "modern CSS" (flex and grid)
  * playing with "modern JavaScript" (nobloat, stimulus style)
  * how to use jekyll more as a knowledgebase

**The rest of this post will likely become more a list of notes along the process
than a well-written instructional recap.**

A repository to follow along is at [](https://github.com    /commits)

## What I learned

Getting a naked jekyll thing is easy, just remove all references to the theme.
Is that a good idea? Probably not.

> I might put key insights into quote-styles in this post.

Like this:

> `bundle exec jekyll s --livereload` saves you a few keystrokes

## Flex and Grid

Very smooth experience, good summaries regarding the approaches:

  * [Guide to Grid](https://css-tricks.com/snippets/css/complete-guide-grid/)
  * [Guide to Flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)

## Fork Awesome

At a certain point my browser was showing the [Fork Awesome](https://forkaweso.me/Fork-Awesome/whats-new/) page. Never heard of it before, and was I was reading sounded fine. It has a mastodon icon which I wanted, so it was an easy choice.

> [Fork Awesome](https://forkaweso.me) works for me.

## Categories and/or tags

I want my posts to be organized in categories. Jekyll itself will not create a
new category page for every found category and there are multiple approaches and
plugins to tackle this issue.

> From jekylls perspective there is no difference between tags and
> categories

The quickest solution is to put all available data on a single categories page.
As long as there isnt too much content and you can ignore SEO-stuff this will
work just fine.

> There are many ways, data, collections, scripts to populate pages or
> collections ...

## Snacks and resources

Idea was: __snacks__ are very short articles (what I learned, or something sweet
that I discovered).

Rather then having them as separate collection, I just added a category and
filter on the snack page.

__Resources__ is basically a link list, with tags added to each link. I modelled
these as data.

However, tagging works differently for these resources, and for now I reverted
to to use heaps of loops (https://stackoverflow.com/questions/64524362/how-to-get-the-unique-values-of-nested-lists-in-jekyll-data).

## SEO

Nothing hot, just using the `jekyll-seo-tag` plugin.

## Licensing

Finally, I also need to choose a license to put the code and content under. Code
(which there is none yet anyway) is easy, I default to (A)GPLv3+.

Content shall be CC-BY-NC-SA .

## Deployment

## Very much open questions

* Do I really want to add some stimulus and turbolinks sprinkles?
* How to achieve [reuse compliance]([https://reuse.software)?
* How to make the grid responsive, e.g. with sweet SCSS mixins?
* How to make tags hierarchical (web:css, web:assets, ...) and group resources
  by it?
* Where to indicate license more clearly?

