# Day 9

## Task

There is a critical issue going on with the Nautilus application in Stratos DC. The production support team identified that the application is unable to connect to the database. After digging into the issue, the team found that mariadb service is down on the database server.

Look into the issue and fix the same.

## Solution

SSH in to the database server and check the status of the mariadb service:

```bash
sudo systemctl status mariadb
```

The service is disabled, so try to enable and start it with:

```bash
sudo systemctl enable --now mariadb
```

But, it failed so check the logs with:

```bash
sudo journalctl -u mariadb
```

The logs show that the mariadb service is failing to start because of a permission issue with the data directory.  Check the permissions with:

```bash
ls -ld /var/lib/mysql
```

The directory is owned by root instead of the mysql user, so change the ownership with:

```bash
sudo chown -R mysql:mysql /var/lib/mysql
```

Then try to enable the service again:

```bash
sudo systemctl enable --now mariadb
```

## Validation

Check the status of the mariadb service again with:

```bash
sudo systemctl status mariadb
```

Also just to be sure, check the logs:

```bash
sudo journalctl -u mariadb
```

## Insights

Oh cool... permissions issues.  Reminds me 5 (oh wait... 6 now!) years ago when I had no idea what they were and had a ton of headaches with my home server.

Checking the status and logs of the service were my first instincts, and I was able to quickly identify the problem with the data directory permissions.  A quick `chown` fixed the issue and I got the service up and running.
