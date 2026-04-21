# Day 2

## Task

As part of the temporary assignment to the Nautilus project, a developer named jim requires access for a limited duration. To ensure smooth access management, a temporary user account with an expiry date is needed. Here's what you need to do:


Create a user named jim on App Server 1 in Stratos Datacenter. Set the expiry date to 2026-12-07, ensuring the user is created in lowercase as per standard protocol.

## Solution

ssh into App Server 1 and run the following command:

```bash
sudo useradd -e 2026-12-07 jim
```

## Validation

You can verify the user was created with the correct expiry date by using the `chage -l` command:

```bash
sudo chage -l jim
```

## Insights

Pretty straightforward, another one I remember practicing a lot for the LFCS.  The `-e` flag is used to set the expiry date for the user in YYYY-MM-DD format, and you can check the expiry date with `chage -l` which will show you the account information including the expiry date.