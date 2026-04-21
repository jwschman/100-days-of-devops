# Day 1

## Task

To accommodate the backup agent tool's specifications, the system admin team at xFusionCorp Industries requires the creation of a user with a non-interactive shell. Here's your task:


Create a user named kareem with a non-interactive shell on App Server 2.

## Solution

ssh into App Server 2 and run the following command:

```bash
sudo useradd -s /sbin/nologin kareem
```

## Validation

You can verify the user was created with the correct shell by checking the `/etc/passwd` file:

```bash
grep kareem /etc/passwd
```

## Insights

It's kind of funny that this is the first task, because it's one of the two tasks that I didn't do correctly when I took the LFCS in 2023.  One of the tasks was to create a user with a non-interactive shell, and that's something that never came up in my studies and I had trouble finding it in man pages, so I just created a user with the default shell, and I looked it up right after the test finished.  Live and learn.