---
title: My PostgreSQL Cheatsheet
date: 2025-01-12 22:00:00 +0100
categories: [cheatsheets]
tags: [cheatsheet, postgresql]     # TAG names should always be lowercase
description: Commands I encountered when learning about PostgreSQL.
---

I heard a lecture about Databases during my Bachelor studies long ago. Time to refresh my knowledge.

I use the course *PostgreSQL Essential v16* from EDB.


# Connect to postgres on ubuntu 24

## Some checks

```
systemctl status postgresql

pg_isready -h localhost -p 5432
```

```
sudo -i -u postgres

psql

# also

psql -p 5432 -U postgres -d postgres
```

possible to set environmental variables: `PGDATABASE, PGHOST, PGPORT` and `PGUSER`


## Execute file

```
psql -f edbstore.sql
```

## Configuration

Aiuthentication:

```
nano /etc/postgresql/16/main/pg_hba.conf 


psql -c 'SELECT pg_reload_conf()'
```

# User tools - CLI

wildcards `?` and `*`

"" for case sensitivity

```
\?
\h [command]

\c
\s
\s FILENAME

\e
\e FILENAME

\w FILENAME

\o FILENAME # output piped to file

\g FILENAME

\watch <seconds>#

\set city Edmonton
\echo :city
\unset city

\if
\elif
\else
\endif

\d[(i|s|t|v|b|S)][+] [patern]

\du # all users
\dn[+] # schemas
\df[+] # functions

\l[+]

\conninfo
\cd [directory]

\! [command]

\q
```

`+` for additional information

special variable `ATUOCOMMIT, ENCODING, HISTFILE` etc.

connect
```
\c <database_name>
```

quit `\q`




## Other commands


Anyway, first commands shown
```
psql postgres postgres
```

```
show port;
show data_directory;

select datname, oig from pg_database;
select spcname from pg_tablespace;
```

```
create table test(i, int);
select pg_relation_filepath('test');
insert into test values(1);
```

Execute os command from psql:
```
\! <command>
```

smallest block/storage page size: 8kb

show table size
```
\dt+ test
```

# General information

Database Cluster Data Directory Layout

 - global: cluster wide database objects
 - base: contains databases
 - pg_tblsc: symbolic link to tablespaces
 - pg_wal: write ahead logging
 - pg_log: startup, logs
 - log: error logs
 - status directories
 - configuration files: postgresql.conf, pg_hba.conf, pg_ident.conf, postgresql.auto.conf
 - Postmaster info files

 Initial databases:
 - template1
 - template0
 - postgres

 Check course for details on how to set up a new cluster using `pg_ctlcluster`
