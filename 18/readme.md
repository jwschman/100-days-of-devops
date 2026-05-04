# Day 18

## Task

We need to setup a database server on Nautilus DB Server in Stratos Datacenter. Please perform the below given steps on DB Server:


a. Install/Configure MariaDB server.

b. Create a database named kodekloud_db4.

c. Create a user called kodekloud_aim and set its password to Rc5C9EyvbU.

d. Grant full permissions to user kodekloud_aim on database kodekloud_db4.


## Solution

Install and login to the MariaDB server:

```bash
sudo yum install mariadb-server
sudo systemctl enable --now mariadb.service
sudo mariadb
```

Then make the database, user, and grant permissions:

```sql
CREATE DATABASE kodekloud_db4;
CREATE USER 'kodekloud_aim' IDENTIFIED BY 'Rc5C9EyvbU';
GRANT ALL PRIVILEGES ON kodekloud_db4.* to kodekloud_aim;
FLUSH PRIVILEGES;
```

## Validation

First we can check that we have the permissions on the db with:

```sql
SHOW GRANTS FOR 'kodekloud_aim';
```

Then we can try to login with the new user:

```bash
mysql -u kodekloud_aim -p
```

Enter the password to login.

## Insights

Pretty easy one, and the documentation pretty much had all the commands that I needed.

If this were an actual install I would have used `mysql_secure_installation` to harden things more, but they were not required by the task so I didn't use it this time.