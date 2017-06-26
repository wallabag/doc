## Upgrade from 2.0.x to 2.1.1

Before this migration, if you configured the Pocket import by adding your consumer key in Internal settings, please do a backup of it: you'll have to add it into the Config page after the upgrade.

### Upgrade on a dedicated web server

```bash
rm -rf var/cache/*
git fetch origin
git fetch --tags
git checkout 2.1.1 --force
SYMFONY_ENV=prod composer install --no-dev -o --prefer-dist
php bin/console doctrine:migrations:migrate --env=prod
php bin/console cache:clear --env=prod
```

### Upgrade on a shared hosting

Backup your `app/config/parameters.yml` file.

Download the 2.1.1 release of wallabag:

```bash
wget http://framabag.org/wallabag-release-2.1.1.tar.gz && tar xvf wallabag-release-2.1.1.tar.gz
```

(md5 hash of the 2.1.1 package: `9584a3b60a2b2a4de87f536548caac93`)

Extract the archive in your wallabag folder and replace
`app/config/parameters.yml` with yours.

Please check that your `app/config/parameters.yml` contains all the
required parameters. You can find [here a documentation about
parameters](http://doc.wallabag.org/en/master/user/parameters.html).

If you use SQLite, you must also copy your `data/` folder inside the new
installation.

Empty `var/cache` folder.

You must run some SQL queries to upgrade your database. We assume that
the table prefix is `wallabag_` and the database server is a MySQL one:

```sql
ALTER TABLE `wallabag_entry` ADD `uuid` LONGTEXT DEFAULT NULL;
INSERT INTO `wallabag_craue_config_setting` (`name`, `value`, `section`) VALUES ('share_public', '1', 'entry');
ALTER TABLE `wallabag_oauth2_clients` ADD name longtext COLLATE 'utf8_unicode_ci' DEFAULT NULL;
INSERT INTO `wallabag_craue_config_setting` (`name`, `value`, `section`) VALUES ('import_with_redis', '0', 'import');
INSERT INTO `wallabag_craue_config_setting` (`name`, `value`, `section`) VALUES ('import_with_rabbitmq', '0', 'import');
ALTER TABLE `wallabag_config` ADD `pocket_consumer_key` VARCHAR(255) DEFAULT NULL;
DELETE FROM `wallabag_craue_config_setting` WHERE `name` = 'pocket_consumer_key';
```
