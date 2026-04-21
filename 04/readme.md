# Day 4

## Task

In a bid to automate backup processes, the xFusionCorp Industries sysadmin team has developed a new bash script named xfusioncorp.sh. While the script has been distributed to all necessary servers, it lacks executable permissions on App Server 2 within the Stratos Datacenter.


Your task is to grant executable permissions to the /tmp/xfusioncorp.sh script on App Server 2. Additionally, ensure that all users have the capability to execute it.

## Solution

ssh into App Server 2 and run the following command:

```bash
sudo chmod a+x /tmp/xfusioncorp.sh
```

## Validation

Verify that the permissions have been set correctly:

```bash
ls -l /tmp/xfusioncorp.sh
```

The output should show that the file has executable permission for all users, which should look like this `-rwxr-xr-x`.  The important part is the `x` in the permissions for owner, group, and others.

## Insights

Very straightforward and something I use pretty often, so this was a quick task.  `chmod a+x` adds executable permission for all users.  You could also use something like `chmod 755` but `+x` is clear for this case since we just want to add executable permission without changing any other permissions.