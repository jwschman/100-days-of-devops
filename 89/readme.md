# Day 89

## Task

Developers are looking for dependencies to be installed and run on Nautilus app servers in Stratos DC. They have shared some requirements with the DevOps team. Because we are now managing packages installation and services management using Ansible, some playbooks need to be created and tested. As per details mentioned below please complete the task:

a. On jump host create an Ansible playbook /home/thor/ansible/playbook.yml and configure it to install vsftpd on all app servers.

b. After installation make sure to start and enable vsftpd service on all app servers.

c. The inventory /home/thor/ansible/inventory is already there on jump host.

d. Make sure user thor should be able to run the playbook on jump host.

Note: Validation will try to run playbook using command ansible-playbook -i inventory playbook.yml so please make sure playbook works this way, without passing any extra arguments.

## Solution

This is basically a trimmed-down version of the last task — install a package and make sure its service is up.  The inventory's already there, so we just write the playbook:

```bash
cd /home/thor/ansible
vim playbook.yml
```

and add:

```yaml
---
- hosts: all
  become: true
  tasks:
    - name: Install vsftpd
      yum:
        name: vsftpd
        state: present

    - name: Start and enable the vsftpd service
      service:
        name: vsftpd
        state: started
        enabled: true
```

## Validation

Run the playbook the same way the validation will:

```bash
ansible-playbook -i inventory playbook.yml
```

Then check that the package is installed and the service is running and enabled on all the servers:

```bash
ansible all -i inventory -a "rpm -q vsftpd"
ansible all -i inventory -a "systemctl is-active vsftpd"
ansible all -i inventory -a "systemctl is-enabled vsftpd"
```

Those should return the installed version, `active`, and `enabled` for each app server.

## Insights

This is actually a simpler version of the previous task.  Just making sure a package is present and then enabling the service.

Also, I don't think I've run `systemctl is-active` and `is-enabled` since studying for the LFCS the other year.
