## Upgrading from 2.1.x to 2.2.x

### Upgrade on a dedicated web server

**From 2.1.x:**

```bash
make update
php bin/console doctrine:migrations:migrate --no-interaction -e=prod
```

#### Explanations about database migrations

During the update, we execute database migrations.

All the database migrations are stored in `app/DoctrineMigrations`. You
can execute each migration individually:
`bin/console doctrine:migrations:execute 20161001072726 --env=prod`.

You can also cancel each migration individually:
`bin/console doctrine:migrations:execute 20161001072726 --down --env=prod`.

Here is the migrations list for 2.1.x to 2.2.0 release:

-   `20161001072726`: added foreign keys for account resetting
-   `20161022134138`: converted database to `utf8mb4` encoding (for
    MySQL only)
-   `20161024212538`: added `user_id` column on `oauth2_clients` to
    prevent users to delete API clients from other users
-   `20161031132655`: added the internal setting to enable/disable
    downloading pictures
-   `20161104073720`: added `created_at` index on `entry` table
-   `20161106113822`: added `action_mark_as_read` field on `config`
    table
-   `20161117071626`: added the internal setting to share articles to
    unmark.it
-   `20161118134328`: added `http_status` field on `entry` table
-   `20161122144743`: added the internal setting to enable/disable
    fetching articles with paywall
-   `20161122203647`: dropped `expired` and `credentials_expired` fields
    on `user` table
-   `20161128084725`: added `list_mode` field on `config` table
-   `20161128131503`: dropped `locked`, `credentials_expire_at` and
    `expires_at` fields on `user` table
-   `20161214094402`: renamed `uuid` to `uid` on `entry` table
-   `20161214094403`: added `uid` index on `entry` table
-   `20170127093841`: added `is_starred` and `is_archived` indexes on
    `entry` table

### Upgrade on a shared hosting

Backup your `app/config/parameters.yml` file.

Download the last release of wallabag:

```bash
wget https://wllbg.org/latest-v2-package && tar xvf latest-v2-package
```

You will find the [md5 hash of the latest package on our
website](https://static.wallabag.org/releases/).

Extract the archive in your wallabag folder and replace
`app/config/parameters.yml` with yours.

Please check that your `app/config/parameters.yml` contains all the
required parameters. You can find [here a documentation about
parameters](./parameters.md).

If you use SQLite, you must also copy your `data/` folder inside the new
installation.

Empty `var/cache` folder.

You must run some SQL queries to upgrade your database. We assume that the table prefix is `wallabag_`. Don't forgete to backup your database before migrating.

You may encounter issues with indexes names: if so, please change queries with the correct index name.

[You can find all the queries here](query-upgrade-21-22.md).
