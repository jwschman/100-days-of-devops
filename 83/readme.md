# Day 83

## Task

An Ansible playbook needs completion on the jump host, where a team member left off. Below are the details:

    The inventory file /home/thor/ansible/inventory requires adjustments. The playbook must run on App Server 3 in Stratos DC. Update the inventory accordingly.

    Create a playbook /home/thor/ansible/playbook.yml. Include a task to create an empty file /tmp/file.txt on App Server 3.

Note: Validation will run the playbook using the command ansible-playbook -i inventory playbook.yml. Ensure the playbook works without any additional arguments.

## Solution

So first let's check out the inventory file:

```bash
cd /home/thor/ansible
vim inventory
```

Looks like they're trying to use an environment variable for the password, but it seems like you can't actually do that, so let's just do what I did in the previous day and set the password directly.  Change the line to:

```ini
`stapp03 ansible_user=banner ansible_ssh_pass=BigGr33n ansible_ssh_common_args='-o StrictHostKeyChecking=no'`
```

Then we just need to create the playbook:

```bash
vim playbook.yml
```

And input this:

```yaml
---
- hosts: stapp03
  tasks:
    - name: Creating an empty file
      ansible.builtin.file:
        path: /tmp/file.txt
        state: touch
```

## Validation

Just run the playbook like it says in the task, and everything should run ok.

If we really wanted to be thorough, we could ssh into `stapp03` and verify that the file existed with `ls /tmp/file.txt`

## Insights

Quite a simple task, quite a simple playbook.

I actually wasn't sure if you could use environment variables in inventory files... it's never something I tried to do.  After a little bit of searching it seemed like you couldn't, so I just entered the password directly.
