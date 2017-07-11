# Installation

## On a dedicated web server (recommended way)

To install wallabag itself, you must run the following commands:

```bash
git clone https://github.com/wallabag/wallabag.git
cd wallabag && make install
```

If you want to use LDAP, replace last command with
```bash
cd wallabag && make install LDAP_ENABLED=true
```
See below for specific LDAP configuration.

To start PHP's build-in server and test if everything did install
correctly, you can do:

```bash
make run
```

And access wallabag at <http://yourserverip:8000>

To define parameters with environment variables, you have to set these
variables with `SYMFONY__` prefix. For example,
`SYMFONY__DATABASE_DRIVER`. You can have a look at [Symfony
documentation](http://symfony.com/doc/current/cookbook/configuration/external_parameters.html).

## On shared hosting

We provide a package with all dependencies inside. The default
configuration uses SQLite for the database. If you want to change these
settings, please edit `app/config/parameters.yml`.

We already created a user: login and password are `wallabag`.

With this package, wallabag doesn't check for mandatory extensions used
in the application (theses checks are made during `composer install`
when you have a dedicated web server, see above).

Execute this command to download and extract the latest package:

```bash
wget https://wllbg.org/latest-v2-package && tar xvf latest-v2-package
```

You will find the [md5 hash of the latest package on our
website](https://static.wallabag.org/releases/).

Now, read the following documentation to create your virtual host, then
access your wallabag. If you changed the database configuration to use
MySQL or PostgreSQL, you need to create a user via this command
`php bin/console wallabag:install --env=prod`.

## Installation with Docker

We provide you a Docker image to install wallabag easily. Have a look at
our repository on [Docker
Hub](https://hub.docker.com/r/wallabag/wallabag/) for more information.

### Command to launch container

```bash
docker pull wallabag/wallabag
```

## Installation on Cloudron

Cloudron provides an easy way to install webapps on your server with a
focus on sysadmin automation and keeping apps updated. wallabag is
packaged as a Cloudron app and available to install directly from the
store.

[Install wallabag on your
Cloudron](https://cloudron.io/store/org.wallabag.cloudronapp.html)

## Installation with LDAP

Wallabag comes with LDAP integration. To enable LDAP, you need to add the
component at install time, and edit `app/config/parameters.yml` with the
configuration:
 - `ldap_enabled`: boolean, `true` to enable LDAP authentication
 - `ldap_host`: LDAP host to contact
 - `ldap_port`: LDAP port to use
 - `ldap_tls`: booelan, Use TLS (cannot be enabled together with SSL)
 - `ldap_ssl`: boolean, Use SSL (cannot be enabled together with TLS)
 - `ldap_bind_requires_dn`: boolean, `true` to use DN to bind to LDAP
 - `ldap_manager_dn`: DN of the Wallabag instance who can search for users
 - `ldap_manager_pw`: Password of the Wallabag instance who can search for users
 - `ldap_filter`: Filter to search for the wallabag users
 - `ldap_username_attribute`: LDAP attribute to use as username
 - `ldap_email_attribute`: LDAP attribute to use as e-mail
 - `ldap_name_attribute`: LDAP attribute to use as name
 - `ldap_enabled_attribute`: LDAP attribute to check if user is enabled
   (optional, default `null` doesn’t check if user is enabled)
 - `ldap_admin_filter`: LDAP filter to check that user has admin rights. %s is
   replaced by the login entered by the user.
