# Day 86

## Task

The Nautilus DevOps team is planning to test several Ansible playbooks on different app servers in Stratos DC. Before that, some pre-requisites must be met. Essentially, the team needs to set up a password-less SSH connection between Ansible controller and Ansible managed nodes. One of the tickets is assigned to you; please complete the task as per details mentioned below:

a. Jump host is our Ansible controller, and we are going to run Ansible playbooks through thor user from jump host.

b. There is an inventory file /home/thor/ansible/inventory on jump host. Using that inventory file test Ansible ping from jump host to App Server 1, make sure ping works.

## Solution

Looks like we're back in Jenkins mode with copying ssh keys.  Just creat the ssh key and copy it to the app servers

```bash
ssh-keygen
```

Use the defaults like we did before, and then copy it over:

```bash
ssh-copy-id tony@stapp01
ssh-copy-id steve@stapp02
ssh-copy-id banner@stapp03
```

Then we need to make sure ansible connects as the users rather than `thor` so we just set the `ansible_user` in the inventory:

```ini
stapp01 ansible_user=tony ansible_ssh_pass=Ir0nM@n
stapp02 ansible_user=steve ansible_ssh_pass=Am3ric@
stapp03 ansible_user=banner ansible_ssh_pass=BigGr33n
```

## Validation

Test the ping against the app servers:

```bash
ansible all -i inventory -m ping
```

## Insights

This one was a bit different than the other ansible tasks we've done so far since we didn't have to actually make a playbook.

I left the ansible_ssh_pass variables in the inventory since they could possibly be used as a fallback, but they really weren't necessary given that we had ssh access.
