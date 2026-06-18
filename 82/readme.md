# Day 82

## Task

The Nautilus DevOps team is testing Ansible playbooks on various servers within their stack. They've placed some playbooks under /home/thor/playbook/ directory on the jump host and now intend to test them on app server 2 in Stratos DC. However, an inventory file needs creation for Ansible to connect to the respective app. Here are the requirements:

a. Create an ini type Ansible inventory file /home/thor/playbook/inventory on jump host.

b. Include App Server 2 in this inventory along with necessary variables for proper functionality.

c. Ensure the inventory hostname corresponds to the server name as per the wiki, for example stapp01 for app server 1 in Stratos DC.

Note: Validation will execute the playbook using the command ansible-playbook -i inventory playbook.yml. Ensure the playbook functions properly without any extra arguments.

## Solution

```bash
vi /home/thor/playbook/inventory
```

Add App Server 2 as a single ini-style host line with host, user, and password:

```ini
stapp02 ansible_host=stapp02 ansible_user=steve ansible_ssh_pass=Am3ric@
```

- `stapp02` is the inventory hostname
- `ansible_user` / `ansible_ssh_pass` are steve's credentials.

>A more proper way to do this would be to set up SSH key authentication (`ssh-copy-id`) on `stapp02` rather than using the password in the inventory file, but I'm just doing this way for simplicity in the task.  I have a feeling I'll likely need to be doing this a lot if it's anything like previous sections.

## Validation

First do a quick connectivity check.

```bash
cd /home/thor/playbook
ansible -i inventory all -m ping
```

That should come back successful.  Then run the playbook that they say in the task:

```bash
ansible-playbook -i inventory playbook.yml
```

It should complete with no issues.

## Insights

All we're really doing is setting up an inventory file here, which is something that I've done for my playbooks as well.

Like I mentioned above, I kept things more simple here just for the task rather than doing them the proper way with SSH keys.
