# Backup wallabag

Because sometimes you may do a mistake with your wallabag and lose data or in case you need to move your wallabag to another server you want to backup your data. This articles describes what you need to backup. See [this page](restore.md) to restore your data.

## What's to backup?

### Settings
wallabag stores some basic parameters (like the SMTP server or the database backend settings and credentials), and some really important, like the database salt in the file `app/config/parameters.yml`. **This file is needed to use the database after restoration!**

### Database
The data itself is stored in the database. See below for methods to backup.

### Downloaded pictures

If the option is active, the pictures retrieved by wallabag are stored under web/assets/images (the images storage was implemented in wallabag 2.2).

## Backup everything

```bash
rsync -Aax wallabag/ wallabag-dirbkp_`date +"%Y%m%d"`/
```

## Backup Database

As wallabag supports different kinds of databases, the way to perform the backup depends on the database you use.

### MySQL/MariaDB

```bash
mysqldump --single-transaction -h [server] -u [username] -p[password] [db_name] > wallabag-sqlbkp_`date +"%Y%m%d"`.bak
```

See http://dev.mysql.com/doc/refman/5.7/en/backup-methods.html for more details.

### PostgreSQL

```bash
PGPASSWORD="password" pg_dump [db_name] -h [server] -U [username] -f wallabag-sqlbkp_`date +"%Y%m%d"`.bak
```

See https://www.postgresql.org/docs/current/static/backup.html for more details.

### SQLite

To backup the SQLite database, you just need to copy the directory data/db from the wallabag application directory.

```bash
sqlite3 data/db/wallabag.db .dump > wallabag-sqlbkp_`date +"%Y%m%d"`.bak
```
