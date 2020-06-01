# Dockerised Odoo DEV environment

# Installation
## Build the container
$ `docker-compose up`
$ `docker container exec -it <container_id> /bin/bash`

## From terminal
```
    docker run -d -t -p 8069:8069 --name=odoo_dev -v <absolute_path>\odoo_etc:/etc/odoo  ubuntu
```

## Side notes
- Make sure you:
    - 
    - install the script as unix (You can use `dos2unix` on your linux env before running)
        - Recursive:
            - $ `find /path -type f -print0 | xargs -0 dos2unix --`

# Connection errors
```
psycopg2.OperationalError: could not connect to server: No such file or directory
        Is the server running locally and accepting
        connections on Unix domain socket "/var/run/postgresql/.s.PGSQL.5432"?
```
    - solution
        - # `service postgresql start`
```
psycopg2.OperationalError: FATAL:  role "root" does not exist
```
    - solution
        - # `su - postgres -c "createuser -s root"`

# Notes

use dos2unix  on the file before running it!

postgresql herstarten
role toevoegen: -> $ su - postgres -c "createuser -s root"
pip install suds

Restore postgres db
    $ `createdb <dbname>`
    $ `pg_restore -U <username> -d <dbname> -1 <filename>.dump`



Brakken Odoo fix:
    - Error: `(b,'', b"/usr/bin/env: 'node': No such file or directory`
        - Solution:
        $ `ln -s /usr/bin/nodejs /usr/bin/node`

__________________

#Error:
Database creation error: new encoding (UTF8) is incompatible with the encoding of the template database (SQL_ASCII) 
HINT: Use the same encoding as in the template database, or use template0 as template.

##solution
```
root@server:~# su postgres
postgres@server:~ $ psql -U postgres
psql (9.0.3)
Type "help" for help.

postgres=# update pg_database set datallowconn = TRUE where datname = 'template0';
UPDATE 1

postgres=# \c template0
You are now connected to database "template0".

template0=# update pg_database set datistemplate = FALSE where datname = 'template1';
UPDATE 1

template0=# drop database template1;
DROP DATABASE

template0=# create database template1 with template = template0 encoding = 'UTF8';
CREATE DATABASE

template0=# update pg_database set datistemplate = TRUE where datname = 'template1';
UPDATE 1

template0=# \c template1
You are now connected to database "template1".

template1=# update pg_database set datallowconn = FALSE where datname = 'template0';
UPDATE 1

template1=#

Ctrl+D

postgres@server:~ $ exit
root@server:~#
```

###Ref./Credit
> http://www.openerp.bg/%D0%B1%D0%BB%D0%BE%D0%B3/item/163-%D0%BF%D1%80%D0%BE%D0%BC%D1%8F%D0%BD%D0%B0-%D0%BD%D0%B0-postgresql-default-template0-%D0%BD%D0%B0-utf8-encoding