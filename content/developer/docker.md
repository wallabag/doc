---
title: Docker
weight: 2
---

Run wallabag in docker-compose
==============================

This document describes how to use Docker for wallabag development
purposes. For running wallabag in production, please use the
[official docker-compose configuration
provided](https://github.com/wallabag/docker).

To run your own development instance of wallabag, you can use the
pre-configured docker compose files provided in the wallabag repository.

Requirements
------------

Make sure to have
[Docker](https://docs.docker.com/installation/ubuntulinux/) and [Docker
Compose](https://docs.docker.com/compose/install/) available on your
system and up to date.

Switch DBMS
-----------

By default, wallabag starts with a SQLite database. However, wallabag
also supports PostgreSQL and MySQL. Docker containers are available for both.

In `compose.yaml` (or `docker-compose.yml` for wallabag < 2.7), for the chosen DBMS uncomment:

-   the container definition (`postgres` or `mariadb` root level block)
-   the container link in the `php` container
-   the container env file in the `php` container

In order to keep running Symfony commands on your host (such as
`wallabag:install`), you also should:

-   source the proper env files on your command line, so variables like
    `SYMFONY__ENV__DATABASE_HOST` will exist.
-   create a `127.0.0.1 rdbms` on your system `hosts` file

Run wallabag
------------

1.  Fork and clone the project
2.  Edit `app/config/parameters.yml` to replace `database_*` properties
    with commented ones (with values prefixed by `env.`)
3.  `composer install` the project dependencies
4.  `php bin/console wallabag:install` to create the schema
5.  `docker compose up` to run the containers (or `docker-compose up` for wallabag < 2.7)
6.  Finally, browse to <http://localhost:8080/> to find your freshly
    installed wallabag.

At various step, you'll probably run into UNIX permission problems, bad
paths in generated cache, etc… Operations like removing cache files or
changing files owners might be frequently required, so don't be afraid!
