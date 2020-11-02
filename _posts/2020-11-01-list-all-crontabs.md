---
layout: post
title:  "list all crontabs"
date:   2020-11-01 11:01:01 +0200
categories: linux snack
toc: true
---

Often enough, we search a cron entry and are not sure which user is issuing it,
or we doubt if there is a crontab entry present (or missing).

In this case, outputting all manually registered crontabs can be handy:

```bash
for user in $(cut -f1 -d: /etc/passwd); do crontab -u $user -l; done
```

(you need extended permissions for that command).
