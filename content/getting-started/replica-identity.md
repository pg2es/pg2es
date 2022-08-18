---
title: "Replica Identity"
weight: 5
description: >
  Configuration of PostgreSQL replica identity, for update and delete operations.

---
TL;DR: You need to specify which **old values** are required.

You may receive warning, that some field is not in WAL and search replica works in upsert only mode.  
This is because old values for `PK` or `Routing` field is not available in updates stream.

If you define `Routing` field or use `PK` which is not Postgres `PRIMARY KEY`, you may need to configure it.
```sql
-- E.G:
ALTER TABLE mytable REPLICA IDENTITY FULL;
```

According to [docs](https://www.postgresql.org/docs/current/sql-altertable.html#SQL-ALTERTABLE-REPLICA-IDENTITY) there four modes:
* `DEFAULT`  Records the old values of the columns of the primary key, if any.
* `USING INDEX index_name`  Records the old values of the columns covered by the named uniq index
* `FULL`  Records the old values of all columns in the row.
* `NOTHING`  Records no information about the old row. 


### Using index

Index must be unique, not partial, not deferrable, and include only columns marked NOT NULL.   
Columns included via `... INCLUDE ( col1, col2`, would be ignored by REPLICA IDENTITY.

```sql
CREATE UNIQUE INDEX example_uniq_idx ON mytable (
    foo,
    bar
    -- ALL YOUR FIELDS SHOULD BE HERE
) INCLUDE (
    baz
    -- THEESE FIELDS WOULD BE IGNORED
);
ALTER TABLE mytable REPLICA IDENTITY USING INDEX example_uniq_idx;
```
