# Day 85

## Task

The Nautilus DevOps team is testing various Ansible modules on servers in Stratos DC. They're currently focusing on file creation on remote hosts using Ansible. Here are the details:

a. Create an inventory file ~/playbook/inventory on jump host and include all app servers.

b. Create a playbook ~/playbook/playbook.yml to create a blank file /opt/code.txt on all app servers.

c. Set the permissions of the /opt/code.txt file to 0744.

d. Ensure the user/group owner of the /opt/code.txt file is tony on app server 1, steve on app server 2 and banner on app server 3.

Note: Validation will execute the playbook using the command ansible-playbook -i inventory playbook.yml, so ensure the playbook functions correctly without any additional arguments.

## Solution

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
    - name: Create blank /opt/code.txt with per-host owner and set permissions
      ansible.builtin.file:
        path: /opt/code.txt
        state: touch
        mode: '0744'
        owner: "{{ ansible_user }}"
        group: "{{ ansible_user }}"
```

## Validation

Just run the playbook:

```bash
ansible-playbook -i inventory playbook.yml
```

Then confirm the file, permissions, and owner on each host:

```bash
ansible all -i inventory -a "ls -l /opt/code.txt"
```

You should see `-rwxr--r--` the owners set on their respective servers.

## Insights

This is my first time attempting to apply per-server owners for files through ansible, but fortunately the owners lined up with the users in the inventory, so I could just use `{{ansible_user}}` inside the playbook to set them.

If the owners weren't the same, we could have set a different custom variable inside the inventory.
