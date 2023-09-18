# Installation

## On a dedicated web server

### Step 1: choose an URL for wallabag

The wallabag developers recommend installing wallabag at the root of a
domain (or subdomain).  Installation in a subdirectory can work, but is 
not officially supported (and is not described in this documentation).

In order to install wallabag at the root of a domain (or subdomain),
you will need to choose, configure, and publish that domain (or
subdomain) via your DNS server.

- (Unsupported alternative #1:  You could dedicate a whole IP address (or 
port) to wallabag, but that may be more complicated.)
- (Unsupported alternative #2:  You could install wallabag in a
subdirectory.  But doing so is not documented here.)

### Step 2: Choose the installation directory

This documentation describes installing wallabag in
`/var/www/wallabag`.  (Adjust this instruction as you fit to
attempt to install wallabag in a different directory.)

The root directory of the wallabag source code contains a subdirectory
named `web`.  This `web` subdirectory MUST be exposed to the document
root of your webserver.  (Typically this is done via the webserver's
configuration files.)  However, the source code's root directory itself
MUST NOT be exposed to the webserver's document root.

For example:

- `/var/www/wallabag/web` MUST be exposed to your webserver's document
root.
- `/var/www/walabag` MUST NOT be exposed to your webserver's document root.

Unfortunately, at least at present, the `web` subdirectory MUST be an
immediate child of the root directory of wallabag's source code.  (I.e.
you MUST NOT move the `web` subdirectory relative to the rest of the
source code.)

These constraints may complicate the choice of an installation
directory.

### Step 3: Choose the installation user

Decide which user will own the wallabag source code.

Note that some (but not all) subdirectories inside the source code will
need to be writeable by the webserver.

See Step 7 (below) for additional details.

### Step 4: Download and install the wallabag source code

As the user you choose in Step 3, run the following commands (or
similar commands, as you see fit):

    $ sudo mkdir /var/www/tmp
    $ sudo chown $user:$group /var/www/tmp
    $ cd /var/www/tmp
    $ git clone https://github.com/wallabag/wallabag.git
    $ sudo mv wallabag /var/www/wallabag
    $ cd /var/www/walabag
    $ sudo rmdir /var/www/tmp

Alternatively, instead of using `git clone`, you could download and
extract the latest release tarball of wallabag.

### Step 5: Edit the `parameters.yml` file

Edit the file `/var/www/wallabag/app/config/parameters.yml`.

Edit the `database_*` parameters as needed.

To use SQLite, set:

```yaml
  database_driver: pdo_sqlite
  database_path: '%kernel.root_dir%/../data/db/wallabag.sqlite'
```

Edit `domain_name:` to be the URL you chose in Step 1.

Edit `secret:`.

Review the other parameters in the file and make any other changes as
you wish.

### Step 6: Run `make install`

As the installation user, run the following commands:

    $ cd /var/www/wallabag
    $ make clean
    $ make install

When you run `make install`, one of two things will happen:

If some dependencies are missing (such as PHP modules or Composer),
then various error messages will be printed and `make install` will
exit.  (At this point, you may try to resolve these errors by
installing the missing PHP modules and/or installing Composer, and then
re-running `make install`.)

Alternatively, if all dependencies are present, then numerous non-fatal
error messages will still be printed.  However, these non-fatal error 
messages will followed by the below prompt:

    Step 2 of 4: Setting up database.
    ---------------------------------

     It appears that your database already exists. Would you like to reset it? (yes/no) [no]:

Answering `yes` will reset (i.e. erase) the entire contents of the
database and then create a fresh, empty set of database tables.  If
this is your first install of wallabag, you probably want to answer
`yes`, in order to erase the current database and build a fresh
database.

Next, you will be prompted to create a new admin user.

### Step 7: Change ownership of certain subdirectories.

As mentioned above in Step 3, wallabag (i.e. the webserver
and/or PHP) needs write access to certain subdirectories inside
`/var/www/wallabag`.

The simplest way to give wallabag write access to these directories
may be to change the ownership of these directories.

For example, on Ubuntu, you might change the ownership by running the
following commands:

    $ cd /var/www/wallabag
    $ sudo chown -R www-data:www-data data var web/assets web/uploads

Alternatively, instead of changing ownership, you could instead try
changing the permissions of these files.  (I have not tested this.)

    $ cd /var/www/wallabag
    $ chmod -R a+rwX data var web/assets web/uploads

Note:  If, later on, you encounter errors when running wallabag, there
may be additional directories not listed above that also need to be
writeable.

### Step 8: Configure your webserver

See the [VirtualHosts](/en/admin/installation/virtualhosts) page for instructions on configuring
common webservers.

### Step 9: Load wallabag in your web browser

After configuring your webserver (as described above in Step 8),
open your web browser and navigate to the URL you chose in Step 1.

If everything worked, you should see the wallabag login page.  You
should be able to login with the username and password that you created
in Step 6.

### Additional notes:

{% hint style="info" %}
To define parameters with environment variables, you have to set these variables with `SYMFONY__` prefix, for example, `SYMFONY__DATABASE_DRIVER`.
You can have a look at [Symfony documentation](http://symfony.com/doc/current/cookbook/configuration/external_parameters.html).
{% endhint %}

{% hint style="info" %}
If you're installing wallabag behind Squid as a reverse proxy, make sure to update your `squid.conf` configuration to include `login=PASS` in the `cache_peer` line. This is necessary for API calls to work properly.
{% endhint %}

## On shared hosting

We provide a package with all dependencies inside. The default
configuration uses MySQL for the database. To add the setting for your database, please edit `app/config/parameters.yml`. Beware that passwords must be surrounded by single quotes (').

We have already created a user: the login and password are `wallabag`.

With this package, wallabag doesn't check for mandatory extensions used
in the application (theses checks are made during `composer install`
when you have a dedicated web server, see above).

Execute this command to download and extract the latest package:

```bash
wget https://wllbg.org/latest-v2-package && tar xvf latest-v2-package
```

You will find the [md5 hash of the latest package on our website](https://wallabag.org/en#download).

The static package requires each command to be appended by `--env=prod` as the static package is only usable as a prod environment (dev environment is not supported and won't work at all).

Now, read the next step to create your virtual host.

You must create your first user by using the command `php bin/console wallabag:install --env=prod`
If an error occurs at this step due to bad settings, you must clear the cache with `php bin/console cache:clear --env=prod` before you try again the previous command.

Then you can access your wallabag.

## Usage of wallabag.it

[wallabag.it](https://wallabag.it) is a paid service to use wallabag without installing it on a web server.

This service always ships the latest release of wallabag. [You can create your account here](https://app.wallabag.it/). Try it for free: you'll get a 14-day free trial with no limitation (no credit card information required).

## Installation with Docker

We provide you a Docker image to install wallabag easily. Have a look at
our repository on [Docker Hub](https://hub.docker.com/r/wallabag/wallabag/) for more information.

### Command to launch container

```bash
docker pull wallabag/wallabag
```

## Installation on Cloudron

Cloudron provides an easy way to install webapps on your server with a
focus on sysadmin automation and keeping apps updated. wallabag is
packaged as a Cloudron app and available to install directly from the
store.

[Install wallabag on your Cloudron](https://cloudron.io/store/org.wallabag.cloudronapp2.html)

## Installation on YunoHost

YunoHost provides an easy way to install webapps on your server with a
focus on sysadmin automation and keeping apps updated. wallabag is
packaged as an official YunoHost app and is available to install directly from the
official repository.

[![Install wallabag with YunoHost](https://install-app.yunohost.org/install-with-yunohost.png)](https://install-app.yunohost.org/?app=wallabag2)

## Installation on alwaysdata

alwaysdata's Marketplace allows to easily install wallabag (and many other
applications) on a Public or Private Cloud.

[Install wallabag on alwaysdata](https://www.alwaysdata.com/en/marketplace/wallabag/)

## Installation on Synology

The SynoCommunity provides a package to install wallabag on your Synology NAS.

[Install wallabag with Synology](https://synocommunity.com/package/wallabag)
