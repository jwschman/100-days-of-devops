# Day 3

## Task

Following security audits, the xFusionCorp Industries security team has rolled out new protocols, including the restriction of direct root SSH login.


Your task is to disable direct SSH root login on all app servers within the Stratos Datacenter.

## Solution

ssh into each app server and edit the SSH daemon configuration file:

```bash
sudo vim /etc/ssh/sshd_config
```

Find the line that says `PermitRootLogin` and change its value to `no`:

```bash
PermitRootLogin no
``` 

Make sure to restart the ssh server after making the change:

```bash
sudo systemctl restart sshd
```

## Validation

Make sure that sshd restarted successfully without errors:

```bash
sudo systemctl status sshd
```

You could try to ssh into the server as root to confirm that it's not allowed, but we don't have the root password to even try, so instead we can check the sshd_config file to confirm that the change was made:

```bash
grep PermitRootLogin /etc/ssh/sshd_config
```

Should return `PermitRootLogin no`

## Insights

A standard thing to do on servers to improve security, so this was a pretty quick and easy task.  Just remember to restart the sshd service after making the change, otherwise it won't take effect until sshd is restarted.
