# Day 92

## Task



One of the Nautilus DevOps team members is working on to develop a role for httpd installation and configuration. Work is almost completed, however there is a requirement to add a jinja2 template for index.html file. Additionally, the relevant task needs to be added inside the role. The inventory file ~/ansible/inventory is already present on jump host that can be used. Complete the task as per details mentioned below:

a. Update ~/ansible/playbook.yml playbook to run the httpd role on App Server 3.

b. Create a jinja2 template index.html.j2 under /home/thor/ansible/role/httpd/templates/ directory and add a line This file was created using Ansible on <respective server> (for example This file was created using Ansible on stapp01 in case of App Server 1). Also please make sure not to hard code the server name inside the template. Instead, use inventory_hostname variable to fetch the correct value.

c. Add a task inside /home/thor/ansible/role/httpd/tasks/main.yml to copy this template on App Server 3 under /var/www/html/index.html. Also make sure that /var/www/html/index.html file's permissions are 0744.

d. The user/group owner of /var/www/html/index.html file must be respective sudo user of the server (for example tony in case of stapp01).

Note: Validation will try to run the playbook using command ansible-playbook -i inventory playbook.yml so please make sure the playbook works this way without passing any extra arguments.

## Solution

There's already a few files already created, so let's check them out.

```bash
cd ~/ansible
cat inventory
cat playbook.yml
```

That shows us:

```ini
stapp01 ansible_host=stapp01 ansible_ssh_pass=Ir0nM@n ansible_user=tony
stapp02 ansible_host=stapp02 ansible_ssh_pass=Am3ric@ ansible_user=steve
stapp03 ansible_host=stapp03 ansible_ssh_pass=BigGr33n ansible_user=banner
```

Then the playbook at ~/ansible/playbook.yml is:

```yaml
---
- hosts: 
  become: yes
  become_user: root
  roles:
    - role/httpd
```

and there's also another task inside `/home/thor/ansible/role/httpd/tasks/main.yml` that we have to edit as well:

```yaml
---
# tasks file for role/test

- name: install the latest version of HTTPD
  yum:
    name: httpd
    state: latest

- name: Start service httpd
  service:
    name: httpd
    state: started
```

So now let's fix everything up. First let's fix `~/ansible.playbook.yml`:

```yaml
---
- hosts: stapp03
  become: true
  roles:
    - role/httpd
```

Next let's create the jinja2 template at `/home/thor/ansible/role/httpd/templates/index.html.j2`:

```bash
vim /home/thor/ansible/role/httpd/templates/index.html.j2
```

and add:

```jinja2
This file was created using Ansible on {{ inventory_hostname }}
```

`inventory_hostname` is the variable that will get filled in when the template is rendered.

Next we add the template task to `/home/thor/ansible/role/httpd/tasks/main.yml`:

```yaml
- name: Deploy index.html from jinja2 template
  template:
    src: index.html.j2
    dest: /var/www/html/index.html
    owner: "{{ ansible_user }}"
    group: "{{ ansible_user }}"
    mode: '0744'
```

I think that's everything...

## Validation

Run the playbook:

```bash
ansible-playbook -i inventory playbook.yml
```

Then check the file on App Server 3:

```bash
ansible stapp03 -i inventory -b -a "cat /var/www/html/index.html"
ansible stapp03 -i inventory -b -a "ls -l /var/www/html/index.html"
```

The `cat` should print:

```text
This file was created using Ansible on stapp03
```

and `ls -l` should show `-rwxr--r--` with `banner banner` as the owner and group.

## Insights

So I have very little experience using jinja2 templates but a quick search led me to the Ansible documentation which had everything I needed.

All I really needed to use was the builtin `template` module, and then the already available `inventory_hostname` and `ansible_user` variables can just be popped in to the template and the task, and we were good to go.
