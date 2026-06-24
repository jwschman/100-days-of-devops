# Day 90

## Task

There are some files that need to be created on all app servers in Stratos DC. The Nautilus DevOps team want these files to be owned by user root only however, they also want that the app specific user to have a set of permissions on these files. All tasks must be done using Ansible only, so they need to create a playbook. Below you can find more information about the task.

Create a playbook named playbook.yml under /home/thor/ansible directory on jump host, an inventory file is already present under /home/thor/ansible directory on Jump Server itself.

    Create an empty file blog.txt under /opt/devops/ directory on app server 1. Set some acl properties for this file. Using acl provide read '(r)' permissions to group tony (i.e entity is tony and etype is group).

    Create an empty file story.txt under /opt/devops/ directory on app server 2. Set some acl properties for this file. Using acl provide read + write '(rw)' permissions to user steve (i.e entity is steve and etype is user).

    Create an empty file media.txt under /opt/devops/ on app server 3. Set some acl properties for this file. Using acl provide read + write '(rw)' permissions to group banner (i.e entity is banner and etype is group).

Note: Validation will try to run the playbook using command ansible-playbook -i inventory playbook.yml so please make sure the playbook works this way, without passing any extra arguments.

## Solution

So we're going to have to target hosts individually.  Check them out (although I have an idea what they'll be...)

```bash
cd /home/thor/ansible
cat inventory
```

The app servers are `stapp01`, `stapp02`, and `stapp03`.  What a shock!  Let's write the playbook:

```bash
vim playbook.yml
```

```yaml
---
- hosts: stapp01
  become: true
  tasks:
    - name: Create blog.txt owned by root
      file:
        path: /opt/devops/blog.txt
        state: touch
        owner: root
        group: root
    - name: Give group tony read on blog.txt
      acl:
        path: /opt/devops/blog.txt
        entity: tony
        etype: group
        permissions: r
        state: present

- hosts: stapp02
  become: true
  tasks:
    - name: Create story.txt owned by root
      file:
        path: /opt/devops/story.txt
        state: touch
        owner: root
        group: root
    - name: Give user steve read+write on story.txt
      acl:
        path: /opt/devops/story.txt
        entity: steve
        etype: user
        permissions: rw
        state: present

- hosts: stapp03
  become: true
  tasks:
    - name: Create media.txt owned by root
      file:
        path: /opt/devops/media.txt
        state: touch
        owner: root
        group: root
    - name: Give group banner read+write on media.txt
      acl:
        path: /opt/devops/media.txt
        entity: banner
        etype: group
        permissions: rw
        state: present
```

## Validation

Run the playbook:

```bash
ansible-playbook -i inventory playbook.yml
```

Then check the files exist, are owned by root, and have the right ACL on each server:

```bash
ansible stapp01 -i inventory -b -a "getfacl /opt/devops/blog.txt"
ansible stapp02 -i inventory -b -a "getfacl /opt/devops/story.txt"
ansible stapp03 -i inventory -b -a "getfacl /opt/devops/media.txt"
```

The owner of all of them should be `root` and they should all have the specified ACL that we applied (only group `tony` on `stapp` gets `r--`, the others are `rw`)

## Insights

So we've created files with `state: touch` in previous tasks, and all we really had to do here was assign the owner to `root` and then set acls (using `acl`) to `r` or `rw` for the specified user on the server.

Also, rather than doing the tasks on all hosts we specified them one by one, which is actually something I've never had to do, but it's a pretty straightforward way of carrying out tasks on specific nodes.

**ALSO** make sure you actually read the instructions carefully.  I may have missed the files being owned by `root` part during my initial try...

It would have also been easy to miss that only one of the hosts needed the ACL to be set to `r` while the others were `rw`.
