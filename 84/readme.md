# Day 84

## Task

The Nautilus DevOps team needs to copy data from the jump host to all application servers in Stratos DC using Ansible. Execute the task with the following details:

a. Create an inventory file /home/thor/ansible/inventory on jump_host and add all application servers as managed nodes.

b. Create a playbook /home/thor/ansible/playbook.yml on the jump host to copy the /usr/src/sysops/index.html file to all application servers, placing it at /opt/sysops.

Note: Validation will run the playbook using the command ansible-playbook -i inventory playbook.yml. Ensure the playbook functions properly without any extra arguments.

## Solution

So first we're creating an inventory again:

```bash
cd /home/thor/ansible
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
    - name: Copy index.html to app servers
      ansible.builtin.copy:
        src: /usr/src/sysops/index.html
        dest: /opt/sysops/
```

## Validation

Just run the playbook:

```bash
ansible-playbook -i inventory playbook.yml
```

And everything should be ok.  We can check that the files got created with:

```bash
ansible all -i inventory -a "cat /opt/sysops/index.html"
```

That should show the contents of `index.html` for all three hosts.

## Insights

So I added the `-o StrictHostKeyChecking=no` in the inventory this round since it was included in the previous day, and it kind of gives me a bit of insurance.

Other than that, this was another quick task.  I actually use the builtin copy module  in one of my playbooks so I basically just did that again here.
