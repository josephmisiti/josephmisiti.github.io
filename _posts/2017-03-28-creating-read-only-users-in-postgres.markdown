---
layout: post
title:  'Creating read-only users in Postgres (.0+)'
date:   2017-03-28
categories: jekyll update
---



If your database is in the public schema, it is easy (this assumes you have already created the `readonlyuser`)

```
db=# \d
                                List of relations
 Schema |                        Name                        |   Type   | Owner
--------+----------------------------------------------------+----------+--------
 public   | users                                | table    | postgres
 public   | dogs                      | table    | postgres

```

You can use the following steps:

```
db=> GRANT SELECT ON ALL TABLES IN SCHEMA public to readonlyuser;
GRANT
db=> GRANT CONNECT ON DATABASE mydatabase to readonlyuser;
GRANT
db=> GRANT SELECT ON ALL SEQUENCES IN SCHEMA public to readonlyuser;
GRANT
```

If your database is using `customschema`,

```
db=# \d
                                List of relations
 Schema |                        Name                        |   Type   | Owner
--------+----------------------------------------------------+----------+--------
 customschema   | users                                | table    | postgres
 customschema   | dogs                      | table    | postgres

```


You should execute the above but add one more command:

```
db=> ALTER USER readonlyuser SET search_path=customschema, public;
ALTER ROLE
```
