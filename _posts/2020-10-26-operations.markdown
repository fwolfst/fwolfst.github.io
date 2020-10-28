---
layout: post
title:  "Operations: runbook"
date:   2020-10-26 10:00:00 +0200
categories: snacks
toc: false
---

From the KISS (keep it simple, stupid) universe come stubbed Semi-automation,
README-based development and plain text data storage.

Braintree released a framework called `runbook`. You can start e.g. from a
ToDo-list and gradually improve until you end up with script that does whatever it should, but fully automatically.

I had fun with it. While it especially scratches some DevOps itches (with
support to work on multiple remote servers via ssh and stuff) it can also be used
for any other task that your computer should be able to do.

Look into the gem itself at [https://github.com/braintree/runbook/](https://github.com/braintree/runbook/).

The introductory post is a good read, at [https://medium.com/braintree-product-technology/https-medium-com-braintree-product-technology-runbook-be6f072cfc0d](https://medium.com/braintree-product-technology/https-medium-com-braintree-product-technology-runbook-be6f072cfc0d).

> The philosophy of Runbook is heavily aligned with [Dan Slimmon’s Do-nothing scripting](https://blog.danslimmon.com/2019/07/15/do-nothing-scripting-the-key-to-gradual-automation/) and [Atul Gawande’s The Checklist Manifesto](http://atulgawande.com/book/the-checklist-manifesto/). It is designed to minimize [Toil](https://landing.google.com/sre/sre-book/chapters/eliminating-toil/).

For some experiments, see
[ecovillage/operations](https://github.com/ecovillage/operations). Included is
an auto-documentation (-> markdown) feature.
