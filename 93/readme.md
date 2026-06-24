# Day 93

## Task

The Nautilus DevOps team had a discussion about, how they can train different team members to use Ansible for different automation tasks. There are numerous ways to perform a particular task using Ansible, but we want to utilize each aspect that Ansible offers. The team wants to utilise Ansible's conditionals to perform the following task:

An inventory file is already placed under /home/thor/ansible directory on jump host, with all the Stratos DC app servers included.

Create a playbook /home/thor/ansible/playbook.yml and make sure to use Ansible's when conditionals statements to perform the below given tasks.

    Copy blog.txt file present under /usr/src/data directory on jump host to App Server 1 under /opt/data directory. Its user and group owner must be user tony and its permissions must be 0755 .

    Copy story.txt file present under /usr/src/data directory on jump host to App Server 2 under /opt/data directory. Its user and group owner must be user steve and its permissions must be 0755 .

    Copy media.txt file present under /usr/src/data directory on jump host to App Server 3 under /opt/data directory. Its user and group owner must be user banner and its permissions must be 0755.

NOTE: You can use ansible_nodename variable from gathered facts with when condition. Additionally, please make sure you are running the play for all hosts i.e use - hosts: all.

Note: Validation will try to run the playbook using command ansible-playbook -i inventory playbook.yml, so please make sure the playbook works this way without passing any extra arguments.

## Solution

So we're just going to be using `when` conditionals over all the hosts, rather than assigning the tasks to respective host like on day 90.

So let's write the playbook:

```bash
cd ~/ansible
vim playbook.yml
```

```yaml
---
- hosts: all
  become: true
  tasks:
    - name: Copy blog.txt to App Server 1
      copy:
        src: /usr/src/data/blog.txt
        dest: /opt/data/blog.txt
        owner: tony
        group: tony
        mode: '0755'
      when: ansible_nodename == "stapp01"

    - name: Copy story.txt to App Server 2
      copy:
        src: /usr/src/data/story.txt
        dest: /opt/data/story.txt
        owner: steve
        group: steve
        mode: '0755'
      when: ansible_nodename == "stapp02"

    - name: Copy media.txt to App Server 3
      copy:
        src: /usr/src/data/media.txt
        dest: /opt/data/media.txt
        owner: banner
        group: banner
        mode: '0755'
      when: ansible_nodename == "stapp03"
```

## Validation

Run the playbook:

```bash
ansible-playbook -i inventory playbook.yml
```

We should show that each task gets skipped on two hosts.

Then let's make sure the correct files exist on all the hosts with correct owners:

```bash
ansible stapp01 -i inventory -b -a "ls -l /opt/data/blog.txt"
ansible stapp02 -i inventory -b -a "ls -l /opt/data/story.txt"
ansible stapp03 -i inventory -b -a "ls -l /opt/data/media.txt"
```

Each should show `-rwxr-xr-x` owner and group for the host.

## Insights

All we needed to do different here is use the `when` conditional for each task, and use the variable mentioned in the note of the task to specify which nodes the task will run on.  Other than that this task was just day 90 again.

And now it looks like we're done with Ansible.  That was quite quick after the Jenkins chapter.  Next we're going on to Terraform which I'm pretty comfortable with.
