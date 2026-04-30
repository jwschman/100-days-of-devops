# Day 17

## Task

The Nautilus application development team has shared that they are planning to deploy one newly developed application on Nautilus infra in Stratos DC. The application uses PostgreSQL database, so as a pre-requisite we need to set up PostgreSQL database server as per requirements shared below:

PostgreSQL database server is already installed on the Nautilus database server.

a. Create a database user kodekloud_rin and set its password to LQfKeWWxWD.

b. Create a database kodekloud_db3 and grant full permissions to user kodekloud_rin on this database.

Note: Please do not try to restart PostgreSQL server service.

## Solution

SSH in to the database server and log in to the postgres database:

```bash
psql
```

It wanted a password, and I didn't have one, so try to log in with the postgres user:

```bash
sudo -u postgres psql
```

Success!  Now just make the user, grant priveleges, and make the db:

```psql
CREATE USER kodekloud_rin WITH PASSWORD 'LQfKeWWxWD'
CREATE DATABASE kodekloud_db3;
GRANT ALL PRIVILEGES ON DATABASE kodekloud_db3 TO kodekloud_rin;
```

## Validation

`\du` to list all users

`\l` to list all databases

Then exit out and try to connect to the new database with the new user:

```bash
psql -U kodekloud_rin -d kodekloud_db3
```

## Insights

This was a quick one since I actually have a bit of experience with postgres.  I did look up the commands just to be sure, specifically the `WITH PASSWORD` part, but other than that everything was pretty simple.
