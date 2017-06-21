# Installation

There are two methods of installing wallabag. The recommended method is to simply type two commands inside your terminal. The other way is useful for hosting providers which don't allow terminal access to their servers.

## On a dedicated web server (recommended method)

To install wallabag itself, you must run the following commands:

```bash
git clone https://github.com/wallabag/wallabag.git
cd wallabag && make install
```

Now, read the following documentation to create your virtual host, then
access your wallabag.

> **[info] Information**
>
> To define parameters with environment variables, you have to set these
> variables with `SYMFONY__` prefix. For example,
> `SYMFONY__DATABASE_DRIVER`. You can have a look at [Symfony
> documentation](http://symfony.com/doc/current/cookbook/configuration/external_parameters.html).

> **[info] Information**
>
> If you want to use SQLite to store your data, please put `%kernel.root_dir%/../data/db/wallabag.sqlite` for the `database_path` parameter during installation.

To start PHP's build-in server and test if everything did install correctly, you can do:

```bash
make run
```

And access wallabag at <http://yourserverip:8000>.

{% hint style='danger' %}
PHP's build-in server is here just to check that the installation works. It has real performance issues in regard to traditionnal webservers such as Nginx or Apache, and uses only one thread, therefore preventing tasks like generating PDFs with pictures. **Therefore, it really shouldn't be used in production**
{% endhint %}

To define parameters with environment variables, you have to set these variables with `SYMFONY__` prefix. For example, `SYMFONY__DATABASE_DRIVER`. You can have a look at [Symfony documentation](http://symfony.com/doc/current/cookbook/configuration/external_parameters.html).

## On shared hosting

{% hint style='info' %}
If you have the possibility to use the dedicated web server method of installation, we stronly advise you to use it instead of the following method.
{% endhint %}

{% hint style='danger' %}
This method of installation uses SQLite as a default database system. However, we don't support SQLite anymore since version 2.3. [Read more](faq.md)
{% endhint %}

We provide a package with all dependencies inside. The default configuration uses SQLite for the database. If you want to change these settings, please edit `app/config/parameters.yml`.

We already created a default user: login and password are `wallabag`. Please change these as soon as wallabag is installed and running.

{% hint style='info' %}
With this package, wallabag doesn't check for mandatory extensions used in the application (these checks are made during `composer install` when you have a dedicated web server, see above).
{% endhint %}

Execute this command to download and extract the latest package:

```bash
wget https://wllbg.org/latest-v2-package && tar xvf latest-v2-package
```

{% hint style='info' %}
You will find the [md5 hash of the latest package on our website](https://static.wallabag.org/releases/).
{% endhint %}

Now, read [the following documentation](virtualhosts.md) to create your virtual host, then access your wallabag. If you changed the database configuration to use MySQL or PostgreSQL, you need to create a user via this command `php bin/console wallabag:install --env=prod`.
You will find the [md5 hash of the latest package on our
website](https://wallabag.org/en#download).

To create a new user, please use the register form. Then, in order to have admin
permissions, please run this query in your favorite DMBS (by replacing `1` with
the id for this new user):

```sql
UPDATE wallabag_user SET roles = 'a:2:{i:0;s:9:"ROLE_USER";i:1;s:16:"ROLE_SUPER_ADMIN";}' where id = 1;
```


## Usage of wallabag.it

[wallabag.it](https://wallabag.it) is a paid service to use wallabag without installing it on a web server.

This service always ships the latest release of wallabag. [You can create your account here](https://app.wallabag.it/). Try it for free: you'll get a 14-day free trial with no limitation (no credit card information required).


## Installation with Docker

We provide you a Docker image to install wallabag easily. Have a look at our repository on [Docker Hub](https://hub.docker.com/r/wallabag/wallabag/) for more information.

### Command to launch container

```bash
docker pull wallabag/wallabag
```

## Installation on YunoHost

YunoHost is a server operating system aiming to make self-hosting accessible to everyone. It is based on Debian GNU/Linux and is fully compatible with it.

The official wallabag app on [YunoHost](https://yunohost.org) is still providing wallabag version 1.x, but there's a wallabag 2.x version installable from here : https://github.com/YunoHost-Apps/wallabag2_ynh

## Installation on Cloudron

Cloudron provides an easy way to install webapps on your server with a focus on sysadmin automation and keeping apps updated. wallabag is packaged as a Cloudron app and available to install directly from the store.

[Install wallabag on your Cloudron](https://cloudron.io/store/org.wallabag.cloudronapp.html)

### Installation on YunoHost

YunoHost provides an easy way to install webapps on your server with a
focus on sysadmin automation and keeping apps updated. wallabag is
packaged as an official YunoHost app and is available to install directly from the
official repository.

[![Install wallabag with YunoHost](https://install-app.yunohost.org/install-with-yunohost.png)](https://install-app.yunohost.org/?app=wallabag2)

