# Upgrade your wallabag installation

## General considerations

Before doing an update, you should always do [a full backup](backup.md) of your installation or at least a backup of your database. You should also read the releases notes to see if there's specific issues to deal with during the migration.

It is best to keep your wallabag server upgraded regularly, and to install all point releases and major releases without skipping any of them, as skipping releases increases the risk of errors.

> **[danger] Downgrading**
>
> Even though possible, downgrading is not supported and risks corrupting your data! If you want to revert to an older wallabag version, make a new, fresh installation and then restore your data from backup. Before doing this, you should ask for help in [the wallabag forums](https://community.wallabag.org) to see if your issue can be resolved without downgrading.

## Upgrading

Starting from wallabag 2.2.0, wallabag can be simply upgraded by typing the following command:

```bash
make update
```

It will download and apply the latest version of wallabag (using git), then apply eventual database migrations, and finally clear the cache for the new version to be usable.

For previous version, you need to follow these guides: [Upgrade from 2.1.x to 2.2.0](/admin/maintenance/upgrades/upgrade21-22.md), [Upgrade from 2.0.x to 2.1.x](upgrade20-21.md) and [Upgrade from 1.x](upgrade1x.md)
