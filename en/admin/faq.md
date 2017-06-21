# Frequently asked questions

## General
### Why was SQLite depreciated ?

SQLite has the advantage of being lite and easy to install (as data are directly stored through files), but is really unpleasant to deal with when doing database structure changes, therefore we decided to drop it's support starting with version 2.3. Most of wallabag should still work with it though.

### There are users that I didn't know of on my server !

The `fosuser_registration` setting in `app/config/parameters.yml` allows users to register on the login page. Set it to `false` if you don't want more users to register. [See more](confirmation/readme.md) on the settings page. You can safely remove their accounts in the users management section.

### I want to create an account for someone, without allowing registration

You can create an account through CLI with the following command `bin/console fos:user:create`. See the [Console commands](console_commands.md) page.

## Installation
### During the installation, I got the error `Error Output: sh: 1: @post-cmd: not found`

It seems you have a problem with your `composer` installation. Try to uninstall and reinstall it.

[Read the documentation about composer to know how to install it](https://getcomposer.org/doc/00-intro.md).

### I've got the `failed to load external entity` error when I try to install wallabag

As described [here](https://github.com/wallabag/wallabag/issues/2529), please edit your `web/app.php` file and add this line: `libxml_disable_entity_loader(false);` on line 5.

This is a Doctrine / PHP bug, nothing we can do about it.

### Users don't receive the confirmation emails

Please ensure that you have installed and properly configured a mail transfer agent. Be sure to include a firewall rule for SMTP. E.g., if using firewalld:

    firewall-cmd --permanent --add-service=smtp
    firewall-cmd --reload

Lastly, if you have SELinux enabled, set the following rule:

`setsebool -P httpd_can_sendmail 1`

### Articles page show up not fully secured
This is because the article you've saved was on a not secured HTTP page, and the embeed pictures are showing up directly from the website unsecured inside wallabag.
There are two solutions to solve this :

* Download the pictures locally
* Use [Camo](https://github.com/atmos/camo). It's an https proxy for external unsecured content, used on both app.wallabag.it and Framabag. A tutorial should soon be available.

## Configuration

### Can I use Fail2Ban ?

Yes ! See [the dedicated page](configuration/fail2ban.md)

## Upgrade

### Upgrade from 2.1.x to 2.2.x fails !

Please refer to [this gist](https://gist.github.com/nicosomb/7c537995f2b845a30b4d6cdf23c1e22c) to fix it.

## Site config & parsing

### If I have issues with a site config, can I edit it ?

Yes! The files you're looking for are named website.tld.txt inside `vendor/j0k3r/graby-site-config`. Please keep in mind that these files **will most probably be overritten at each update**, so please submit your changes to https://github.com/fivefilters/ftr-site-config afterwards so that we can integrate them.

### Parsing an article works properly on others instances and on f43.me, but not on my own

Please check the PHP tidy extension `php-tidy` is installed.

### Article import fails on smileys

If you're using MySQL or MariaDB, you should be using a database with UTF8-mb4 character set. Be sure to also fill this setting inside the parameters file.
