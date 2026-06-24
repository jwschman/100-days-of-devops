# Day 91

## Task

The Nautilus DevOps team want to install and set up a simple httpd web server on all app servers in Stratos DC. They also want to deploy a sample web page using Ansible. Therefore, write the required playbook to complete this task as per details mentioned below.

We already have an inventory file under /home/thor/ansible directory on jump host. Write a playbook playbook.yml under /home/thor/ansible directory on jump host itself. Using the playbook perform below given tasks:

    Install httpd web server on all app servers, and make sure its service is up and running.

    Create a file /var/www/html/index.html with content:

This is a Nautilus sample file, created using Ansible!

    Using lineinfile Ansible module add some more content in /var/www/html/index.html file. Below is the content:

Welcome to Nautilus Group!

Also make sure this new line is added at the top of the file.

    The /var/www/html/index.html file's user and group owner should be apache on all app servers.

    The /var/www/html/index.html file's permissions should be 0755 on all app servers.

Note: Validation will try to run the playbook using command ansible-playbook -i inventory playbook.yml so please make sure the playbook works this way without passing any extra arguments.

## Solution

Back to doing the same thing on all hosts.  Let's just create the playbook and get going:

```bash
cd /home/thor/ansible
vim playbook.yml
```

```yaml
---
- hosts: all
  become: true
  tasks:
    - name: Install httpd
      yum:
        name: httpd
        state: present

    - name: Start and enable httpd
      service:
        name: httpd
        state: started
        enabled: true

    - name: Create index.html with the sample content
      copy:
        content: "This is a Nautilus sample file, created using Ansible!\n"
        dest: /var/www/html/index.html
        owner: apache
        group: apache
        mode: '0755'

    - name: Add the welcome line at the top of the file
      lineinfile:
        path: /var/www/html/index.html
        line: "Welcome to Nautilus Group!"
        insertbefore: BOF
```

Order matters for this one.  We need to use `copy` before `lineinfile` so we actually have a file to insert into.

## Validation

Run the playbook:

```bash
ansible-playbook -i inventory playbook.yml
```

Check the service and the file on all servers:

```bash
ansible all -i inventory -a "systemctl is-active httpd"
ansible all -i inventory -b -a "cat /var/www/html/index.html"
ansible all -i inventory -b -a "ls -l /var/www/html/index.html"
```

We should see that httpd is active, and the file should read:

```text
Welcome to Nautilus Group!
This is a Nautilus sample file, created using Ansible!
```

and `ls -l` should show `-rwxr-xr-x` with `apache:apache` as owner and group.

## Insights

So using the `copy` module here made things quite easy.  We were able to insert the static content and assign the owner and permissions with one task.

Then once that was ready we had to use `lineinfile` like before, and use the `insertbefore: BOF` to insert it at the top of the file.  The Ansible documentation is pretty good about pointing out how to do this kind of stuff.
