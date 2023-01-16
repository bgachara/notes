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

```bash

Postgres officially refers to users as roles. Not all roles need to have to have login privileges.

Postmaster(represents the postgresql service and affect the whole server) vs User constext.

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
