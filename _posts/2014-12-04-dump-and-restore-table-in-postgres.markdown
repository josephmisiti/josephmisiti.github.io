---
layout: post
title:  "Dumping and restoring a table in Postgres"
date:   2014-12-04
---

I recently needed to copy a single table in a database from server A to server B. This required dumping the table on server A and restoring the table on server B.

To dump a single table in postgres, use the `pg_dump` command:

```
pg_dump --table <TABLE_NAME> -FC <DATABASE_NAME> > <TABLE_NAME>.sql
```

If you are on UBUNTU, and are using defaults across the board, this will probably require you
to switch user to postgres first.

```
sudo su - postgres
```

After you have copied `<TABLE_NAME>.sql` to the new machine, you can restore that same table using the following comamnd:

```
pg_restore --dbname <DATABASE_NAME --table=<TABLE_NAME> <TABLE_NAME>.sql
```
