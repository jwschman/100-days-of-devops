# Day 8

## Task

During the weekly meeting, the Nautilus DevOps team discussed about the automation and configuration management solutions that they want to implement. While considering several options, the team has decided to go with Ansible for now due to its simple setup and minimal pre-requisites. The team wanted to start testing using Ansible, so they have decided to use jump host as an Ansible controller to test different kind of tasks on rest of the servers.

Install ansible version 4.10.0 on Jump host using pip3 only. Make sure Ansible binary is available globally on this system, i.e all users on this system are able to run Ansible commands.

## Solution

I just checked the installing Ansible documentation and got to installing a specific version with pip3.  

```bash
sudo -H pip3 install ansible==4.10.0
```

## Validation

check the ansible version with:

```bash
ansible --version
```

and check that it's installed globally with:

```bash
which ansible
```

and see that it's at `/usr/local/bin/ansible`

## Insights

So I haven't spent much time with pip3 since I haven't done much with python.  I do know what Ansible is and use it for setting up my homelab hosts, though, so I was familiar with it.  I just had to figure out how to use pip3 to install a specific version of Ansible and make it available globally.

I had never tried to install something globally with pip3 before, so I had to do a little bit of research, and found the `sudo -H` option to make sure it gets put in `/usr/local/bin` instead of the user's home directory.  I also had to check the documentation to make sure how to specify the version of Ansible to install, which was just `ansible==4.10.0`.  

Overall, it was a pretty quick task that took less than 5 minutes.  Writing this readme actually took more time than the actual task, which is a good sign I think.
