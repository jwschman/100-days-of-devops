# Day 88

## Task

The Nautilus DevOps team wants to install and set up a simple httpd web server on all app servers in Stratos DC. Additionally, they want to deploy a sample web page for now using Ansible only. Therefore, write the required playbook to complete this task. Find more details about the task below.

We already have an inventory file under /home/thor/ansible directory on jump host. Create a playbook.yml under /home/thor/ansible directory on jump host itself.

    Using the playbook, install httpd web server on all app servers. Additionally, make sure its service should up and running.

    Using blockinfile Ansible module add some content in /var/www/html/index.html file. Below is the content:

    Welcome to XfusionCorp!

    This is  Nautilus sample file, created using Ansible!

    Please do not modify this file manually!

    The /var/www/html/index.html file's user and group owner should be apache on all app servers.

    The /var/www/html/index.html file's permissions should be 0777 on all app servers.

Note:

i. Validation will try to run the playbook using command ansible-playbook -i inventory playbook.yml so please make sure the playbook works this way without passing any extra arguments.

ii. Do not use any custom or empty marker for blockinfile module.

## Solution

Yay, the inventory already exists.  We just need to write the playbook:

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
    - name: Install httpd
      yum:
        name: httpd
        state: present

    - name: Start and enable the httpd service
      service:
        name: httpd
        state: started
        enabled: true

    - name: Add content to index.html
      blockinfile:
        path: /var/www/html/index.html
        create: true
        block: |
          Welcome to XfusionCorp!

          This is  Nautilus sample file, created using Ansible!

          Please do not modify this file manually!
        owner: apache
        group: apache
        mode: '0777'
```

## Validation

Run it the way the validator will:

```bash
ansible-playbook -i inventory playbook.yml
```

Then check the files and permissions are right on all the servers:

```bash
ansible all -i inventory -a "ls -l /var/www/html/index.html"
ansible all -i inventory -a "cat /var/www/html/index.html"
```

The `cat` should show the sample content wrapped in the default ANSIBLE MANAGED BLOCK markers since we were asked not to use an empty `marker` block.

## Insights

So the only thing new here was starting the service and then adding content to a file using `blockinfile`.  What's nice about `blockinfile` is that we can also set permissions directly inside it, so we didn't need to do a separate task for that.
