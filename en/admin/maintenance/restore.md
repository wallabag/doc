# Restore wallabag

## Restore folders

```bash
rsync -Aax wallabag-dirbkp/ wallabag/
```

## Restore database

### Clean database before restoring

Before restoring the backup into the database, you need to clean the database

#### MySQL
To recreate the MySQL database:

```bash
mysql -h [server] -u [username] -p[password] -e "DROP DATABASE wallabag"
mysql -h [server] -u [username] -p[password] -e "CREATE DATABASE wallabag CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci"
```

If you don't use UTF8 with multibyte support (utf8mb4), use:
```bash
mysql -h [server] -u [username] -p[password] -e "DROP DATABASE wallabag"
mysql -h [server] -u [username] -p[password] -e "CREATE DATABASE wallabag"
```

#### PostgreSQL
To recreate the PostgreSQL database:
```bash
PGPASSWORD="password" psql -h [server] -U [username] -d wallabag -c "DROP DATABASE \"wallabag\";"
PGPASSWORD="password" psql -h [server] -U [username] -d wallabag -c "CREATE DATABASE \"wallabag\";"
```

#### SQLite
```bash
rm data/db/wallabag.sqlite
```

### Restoring
#### MySQL
To restore MySQL:
```bash
mysql -h [server] -u [username] -p[password] [db_name] < wallabag-sqlbkp.bak
```

#### PostgreSQL
```bash
PGPASSWORD="password" pg_restore -c -d wallabag -h [server] -U [username] wallabag-sqlbkp.bak
```

#### SQLite
```bash
sqlite3 data/db/wallabag.sqlite < wallabag-sqlbkp.bak
```
