# Requirements

## Memory

Memory requirements for running an wallabag server are greatly variable, depending on the numbers of users and entries, and volume of server activity. wallabag needs a minimum of 128MB RAM, and we recommend a minimum of 512MB.

## Recommended Setup for Running wallabag
For best performance, stability, support, and full functionality we recommend:

* Red Hat Enterprise Linux 7 / Ubuntu 16.04 LTS / Debian 8
* MySQL/MariaDB
* PHP 7.0 +
* Nginx

## Supported Platforms

* Server: Only Linux (Debian 7, SUSE, RHEL/CentOS, Ubuntu 16.04 LTS and newer). Use \*BSD at your own risk.
* Web server: Apache 2 (mod_php, php-fpm) or Nginx (php-fpm). Lighttpd is reported to work.
* Databases: MySQL/MariaDB 5.5+ and PostgreSQL
* PHP 5.6 + required
* Web browser: Microsoft Edge, Firefox 52+, Chrome 50+, Safari 9+

### Apps
 * Desktop: Windows 8+
 * Mobile: Android 4.4+, iOS 10.0+, Windows Mobile 8+
 * Browser extension: Firefox 52+, Chrome, Opera

## Databases

wallabag uses the library ORM Doctrine to connect to databases, limiting support for SQLite, MySQL and PostgreSQL.

{% hint style='danger' %}
Starting with wallabag 2.3, SQLite is no longer the default database selected and will not be supported anymore in the future.
{% endhint %}

Choosing between MySQL and PostgreSQL is more a matter of:
* If one of them is already installed
* If you're already familiar with one or another

## PHP
wallabag is compatible with PHP versions starting from **PHP >= 5.6**, including PHP 7.0 and PHP 7.1.

> **[info] Information**
>
> To install wallabag easily, we provide a `Makefile`, so you need to have the `make` tool installed.

wallabag uses a large number of PHP libraries and extensions in order to function.
These libraries must be installed with a tool called Composer. You need to install it if you have not already done so and be sure to use the 1.2 version (if you already have Composer, run a `composer selfupdate`).

Install Composer:

    curl -s https://getcomposer.org/installer | php

You can find specific instructions
[here](https://getcomposer.org/doc/00-intro.md).

You'll also need the following extensions for wallabag to work. Some of
these may already activated in your version of PHP, so you may not have
to install all corresponding packages.

-   php-session
-   php-ctype
-   php-dom
-   php-hash
-   php-simplexml
-   php-json
-   php-gd
-   php-mbstring
-   php-xml
-   php-tidy
-   php-iconv
-   php-curl
-   php-gettext
-   php-tokenizer
-   php-bcmath

wallabag uses PDO to connect to the database, so you'll need one of the
following depending on which database you choose earlier:

-   pdo_mysql
-   pdo_sqlite
-   pdo_pgsql

and its corresponding database server.

## Disk

Size of the database shouldn't be an issue (Framabag's PostgreSQL database, which has over 1.3 million articles, is around 1GB), but if you choose to locally [download the pictures](../configuration/internal_settings.md#Download images locally), you need to plan to have some free storage ahead.

For speed we recommend to have wallabag and it's database installed on a SSD disk, while the pictures folder can be mounted on a traditionnal hard drive.

## Asynchronous tasks

If you plan to have multiple user importing large files (or Pocket imports) into wallabag, you'll need some tools to process these files asynchronously. We support Redis and RabbitMQ.
