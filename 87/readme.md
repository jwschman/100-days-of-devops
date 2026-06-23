# Day 87

## Task

The Nautilus Application development team wanted to test some applications on app servers in Stratos Datacenter. They shared some pre-requisites with the DevOps team, and packages need to be installed on app servers. Since we are already using Ansible for automating such tasks, please perform this task using Ansible as per details mentioned below:

    Create an inventory file /home/thor/playbook/inventory on jump host and add all app servers in it.

    Create an Ansible playbook /home/thor/playbook/playbook.yml to install sqlite package on all  app servers using Ansible yum module.

    Make sure user thor should be able to run the playbook on jump host.

Note: Validation will try to run playbook using command ansible-playbook -i inventory playbook.yml so please make sure playbook works this way, without passing any extra arguments.

## Solution

>Copy-paste again for the first part:

Starting out the same as the last few, so I'm just going to copy paste here:

So first we're creating an inventory again:

```bash
cd /home/thor/playbook
vim inventory
```

and add this:

```ini
stapp01 ansible_user=tony ansible_ssh_pass=Ir0nM@n ansible_ssh_common_args='-o StrictHostKeyChecking=no'
stapp02 ansible_user=steve ansible_ssh_pass=Am3ric@ ansible_ssh_common_args='-o StrictHostKeyChecking=no'
stapp03 ansible_user=banner ansible_ssh_pass=BigGr33n ansible_ssh_common_args='-o StrictHostKeyChecking=no'
```

Then just make the playbook:

```bash
vim playbook.yml
```

and add:

```yaml
---
- hosts: all
  become: true
  tasks:
    - name: Install sqlite package
      yum:
        name: sqlite
        state: present
```

## Validation

Run the playbook the same way the validation will:

```bash
ansible-playbook -i inventory playbook.yml
```

We should see that everything succeeded with the changes, and then we can check if the packages are present on the app servers:

```bash
ansible all -i inventory -a "rpm -q sqlite"
```

That should show the installed sqlite version for each app server.

## Insights

So it seems like we're just using the same inventory for all these tasks, which is much quicker than setting up the nodes on Jenkins each time.

The actual playbook was just as simple as the other ones we've done.  Just a single task for making sure a package is present on the machine, something I have in my ansible playbooks as well.
