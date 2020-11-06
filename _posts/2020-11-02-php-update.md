---
layout: post
title:  "updating php for nextcloud on xenial"
date:   2020-11-02 16:04:00
categories: php linux
toc: true
---

## Notes during an update process

### World views

[Nextcloud](https://nextcloud.com) has a [speedy release cycle](https://github.com/nextcloud/server/wiki/Maintenance-and-Release-Schedule) (and there is [controversy about that](https://help.nextcloud.com/t/review-nextcloud-release-cycle/95501)), putting quite some pressure on the admins, but sparking joy with users, as the feature development has quite some momentum.

[Ubuntu](https://ubuntu.com) follows a regular LTS release strategy since years.
Wait for april in even years (and then wait a bit for added stability and
experience).

[php](https://php.net) is different, I believe. The most important thing seems
to be that the upcoming release of 8.0 (scheduled this month, november 2020)
will render trilliards of jokes invalid.


```php
0 == "I am 42"

# -> evaluates to true @ php <= 8.0
```

At least thats proposed:
[https://wiki.php.net/rfc/string_to_number_comparison](https://wiki.php.net/rfc/string_to_number_comparison).

php is absolutely not _"my"_ language, but [this list of changes for 8.0](https://stitcher.io/blog/new-in-php-8) seems really reasonable. I do not follow phps development, but looks like a release with really powerful changes to me.

### Legacy

But back to the problem. The setup is as follows: Nextcloud 19.0.4 (very recent, but not latest) on a Ubuntu 16.04 (not
so recent) and php7.2 (-fpm, aged) from ondrej on an apache. Nextcloud 20 is out and 21 will only work with
php7.4+ . Most likely until then the VM will need a dist-upgrade to Ubuntu 20.04 - or be replaced.

#### Configuration files

Performance is tuned/configuration is done in following locations (in my setup):

```
/etc/php/7.2/apache/php.ini
/etc/php/7.2/cli/php.ini
/etc/php/7.2/fpm/php-fpm.conf
/etc/php/7.2/fpm/php.ini
/etc/php/7.2/fpm/pool.d/www.conf

/etc/apache2/conf-enabled/php7.2-fpm.conf
/etc/apache2/sites-enabled/nextcloud.conf
```

### Install php7.4 (and maybe missing modules)

`apt update && apt install php7.4 php7.4-fpm` and check whether any of the modules (e.g.
`php7.2-gd`) can receive updates, too.

Also 
`apt install libapache2-mod-php7.4` and `a2enmod php7.4`.

### Enable fpm in apache

```bash
a2enmod proxy_fcgi setenvif
a2enconf php7.4-fpm
a2dismod php7.2-fpm
```

### Move over/diff the configuration files

Check above mentioned files, e.g.

```bash
vimdiff /etc/php/7.{2,4}/cli/php.ini
vimdiff /etc/php/7.{2,4}/apache2/php.ini
vimdiff /etc/php/7.{2,4}/fpm/php.ini
vimdiff /etc/php/7.{2,4}/fpm/php-fpm.conf
vimdiff /etc/php/7.{2,4}/fpm/pool.d/www.conf
```

### Restart apache and fpm service

```bash
service apach2 restart
service php7.4-fpm restart
```

### update-alternatives

Now, make php7.4 the default.

```bash
sudo update-alternatives --set php /usr/bin/php7.4
```

Verify

```bash
php -v
```

Check if the workers are up
```bash
systemctl status php7.2-fpm
```

### Remove php7.2

After some while, get rid of 7.2
```bash
apt purge php7.2
```

## When things go wrong

Oh, they will. Keep on going. Like, you will forget to install php7.4-mysql or
something.




