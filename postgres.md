# Reference Notes on Postgres

`Notes on Postgresql and side notes on Mysql ecosystem`

## Init

### Configuration Files.

- postgresql.conf
    - controls general settings, such as memory location, default storage location for new databases, the IP addresses that Postgresql listens on, location of logs and plenty more.
    - controls life-sustaining settings of the Postgres server.
    - one can override settings at the database, role, session and even function levels.
    - preferred to edit the *postgres.auto.conf* for custom settings.
- pg_hba.conf
    - controls access to the server, dictating which users can log in to which databases, which IP addresses can connect and which authentication scheme to accept.
    - dictates the authentication protocol client must follow.
    - 
- pg_ident.conf
    - if present, this file maps an authenticated OS login to a PostgreSQL user.

```sql

Postgres officially refers to users as roles. Not all roles need to have to have login privileges.

Postmaster(represents the postgresql service and affect the whole server) vs User constext.

After postgresql install, you should log in as postgres and create other roles.

create role le login password 'king' createdb;
create role le login password 'king' superuser;
```
- Settings with user or superuser context can be set for a specific database, user, session and function level.
- Important Network settings to watch(change requires a server restart):
    - listen_addresses
    - port
    - max_connections
    - log_destination
- Settings to tweak for better performance:
    - shared_buffers
    - effective_cache_size
    - work_mem
    - maintenance_Work_mem
- Creating group roles and add members to it.

## Database Creation

- Postgres uses a client/server model.
    - server process(postgres): manages the db files, accepts connections, perform db actions.
    - client process: app that wants to perform db actions.
- They can be on different hosts and communicate over a tcp/ip network. 
```sql

create database db_name;

```
- Existence of template databases that act as skeleton for new databases.
- You can use any database as a template, useful when you want to make replicas.
- You can make a db as a template after which it is no lonegr editable and deletable.

```sql

create database db_name template template_db_name;

update pg_database set datistemplate = true where datname = 'db_name';

```
## Schemas

- schemas organize your database into logical groups.
- schemas created to also house extensions.

## Privileges

- Permissions, this can bored down to row and column level.
- Types:
    - Select, Insert, Update, Alter, Execute, Delete and Truncate.
- Most privileges must have a context.
- 

## Getting Started

- Should have one superuser whose passwd is known: 'postgres'
- Postgresql creates one superuser and db at installation both called postgres.
- Before creating a db, create a role that will own that db

```sql

create role new_role login password 'new_pass';

```
- Create db and set the owner.

```sql

create database mydb with owner = new_role;
```

- GRANT command is the primary means to assign privileges.
- Owner of an object retains all privileges.

```sql
GRANT some_privilege to some_role;

```
- Default priviliges.

## Extensions

- add-ons that you can install to extend functionality beyond base offerings.
