# Ansible

## Intro to Ansible

### What is Ansible?

* A Configuration Management tool
* Red Hat leads development
* open-source
* written in Python
* started off with a few core modules for managing + configuration Linux servers
* main use cases:
  * config management (keep software and software setting in the desired & consistent state)
  * application deployment
* works with almost any system, including...
  * Windows & Linux servers
  * routers & switches
  * cloud services

<br>

### How does it work?

* If Ansible = robot, then the recipe (actions/tasks) would be a playbook
* Playbooks are written YAML
* Use Ansible Control Node (a "controller") configures Targets Nodes
* Agentless
  * No need to install Ansible on Target Nodes
  * Instead, Ansible controller will SSH into Target Nodes to run commands
  * On Linux Target Nodes, a Python interpreter is required

### Getting Ansible controller to do ping

To go in hosts file:
```
ec2-instance ansible_host=172.31.49.101 ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/tech515-sherin-aws.pem
```