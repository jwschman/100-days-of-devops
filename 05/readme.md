# Day 5

## Task

Following a security audit, the xFusionCorp Industries security team has opted to enhance application and server security with SELinux. To initiate testing, the following requirements have been established for App server 3 in the Stratos Datacenter:


    Install the required SELinux packages.

    Permanently disable SELinux for the time being; it will be re-enabled after necessary configuration changes.

    No need to reboot the server, as a scheduled maintenance reboot is already planned for tonight.

    Disregard the current status of SELinux via the command line; the final status after the reboot should be disabled.

## Solution

ssh into App Server 3 and run the following commands:

```bash
sudo yum install -y selinux-policy selinux-policy-targeted
```

Then edit the SELinux configuration file to disable it permanently:

```bash
sudo vim /etc/selinux/config
```

Find the line that says `SELINUX=enforcing` and change it to `SELINUX=disabled`:

```bash
SELINUX=disabled
```

## Validation

Verify that the SELinux configuration has been updated:

```bash
grep SELINUX /etc/selinux/config
```

The output should show `SELINUX=disabled`

## Insights

This was the first one that I had to look up.  First, I didn't even know what package manager I'd be using, so I ran `hostnamectl` to find that the server is running CentOS, so I knew to use `yum`.

Then I had to look up what packages to install SELinux and I found that `selinux-policy` and `selinux-policy-targeted` are the main packages for SELinux.  Then I had to check how to disable SELinux permanently, and found that it's done in the `/etc/selinux/config` file by changing `SELINUX=enforcing` to `SELINUX=disabled`.

This change won't take effect until the server is rebooted, but the instructions said that we wonn't need to reboot right away.  After the reboot we could check the status with `sestatus` or `getenforce` to confirm that it's disabled.