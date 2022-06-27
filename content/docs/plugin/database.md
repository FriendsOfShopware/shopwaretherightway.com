---
weight: 10
title: Database
hidden: true
---

# Database

All tables or columns created by you should contain your plugin prefix and should be dropped after uninstallation.
All columns except indexing fields for the DAL should be created in an own table to ensure migrations are fast executed on a larger data set.

## Expensive migrations

Expensive migrations should be avoided, as this migration could run into a timeout. An example migration could be

```sql
ALTER TABLE users
ADD COLUMN `count` SMALLINT(6) NOT NULL AFTER `lastname`;
```

The `AFTER` declaration makes that MySQL copies the entire table and relocates the columns. This can take very long, when the table is large and locks all operations on the table too. So the learning here is to avoid `AFTER` declaration.

## Wildcard Selects

Wildcard selects like:

```sql
SELECT table1.*, table2.*
FROM table1
INNER JOIN table2 ON(table2.id = table1.id)
```

Can be dangerous. When a plugin adds a similar column from `table1` to `table2` we will get an ambiguous MySQL error. So explicit selects are more secure.