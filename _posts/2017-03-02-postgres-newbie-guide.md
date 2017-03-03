---
layout: post
title: Postgres Newbie Guide (For Me)
---
## Postgresql Basic Installation and Quick Setup Guide

## Install
Most of the time I'm using Debian-based Linux, so

```
apt-get install postgresql-9.4
```

## postgres user
It will automatically create a **postgres** user account, so you need to setup it's password

```
sudo passwd postgres
```

## switch to postgres account

```
su - postgres
```

## run psql
Postgres command line

```
psql
```

## the ff must be run inside Postgres command line

### create user role

```
CREATE ROLE testrole WITH LOGIN;
ALTER ROLE testrole WITH PASSWORD 'password';
ALTER ROLE testrole WITH CREATEDB;
```

### some useful commands

```
\du               'list users
\db               'list tablespace
\list             'list databases
\connect <db>     'connect to database
\dt               'list tablespace
\di               'list indexes
```

## useful links
* [Installation](https://www.postgresql.org/download/linux/debian/)
* [User management](https://www.postgresql.org/docs/8.1/static/user-manag.html)
* [Postgres Tutorial - Digital Ocean](https://www.digitalocean.com/community/tutorials/how-to-use-roles-and-manage-grant-permissions-in-postgresql-on-a-vps--2)
* [Alter Role man](http://man.yolinux.com/cgi-bin/man2html?cgi_command=alter_role(7))
