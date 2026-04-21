# Day 7

## Task

The system admins team of xFusionCorp Industries has set up some scripts on jump host that run on regular intervals and perform operations on all app servers in Stratos Datacenter. To make these scripts work properly we need to make sure the thor user on jump host has password-less SSH access to all app servers through their respective sudo users (i.e tony for app server 1). Based on the requirements, perform the following:

Set up a password-less authentication from user thor on jump host to all app servers through their respective sudo users.

## Solution

First we need to generate the ssh key pair for the thor user on the jump host.

```bash
ssh0-keygen
```

I just used defaults for everything since nothing was specified in the instructions, and didn't set a passphrase.

Next, we need to copy the public key to the respective sudo users on each app server.  We can use `ssh-copy-id` for that.

```bash
ssh-copy-id tony@stapp01
ssh-copy-id steve@stapp02
ssh-copy-id bruce@stapp03
```

It will ask you for the password of each user, and once you enter it, it will copy the public key to the respective user's `~/.ssh/authorized_keys` file.

## Validation

Just ssh into each app server using the respective sudo user and check if you can log in without a password.

```bash
ssh tony@stapp01
ssh steve@stapp02
ssh bruce@stapp03
```

## Insights

Another very common sysadmin task, and one that I perform very often with my servers and VMs.  It's a very useful skill, and one I wish I had known about when I first started using Linux and FreeBSD, and not 3 years later.  It saves a lot of time and hassle, and it's also more secure than passwords.
