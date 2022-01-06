### Ansible Role to Install Wordpress on LAMP Stack on Amazon Linux 2

This role can be used to install a fully featured LAMP stack on Amazon Linux. This can be used to deply a full WordPress installation or any CMS/Applications that works over a LAMP stack.

---
#### Features
  - Fully configurable and compatible with AWS
  - Flexible and feature rich with easy customization of roles
  - Can be modified to support additional modules
  - Can be used to create a standalone LAMP stack or deploy applications over the current LAMP stack

#### Ansible installation
> Note : I'm using EC2 instance to perform the task

```sh
amazon-linux-extras install ansible2 -y
```
- The configuaration file of ansible will be
```sh
/etc/ansible/ansible.cfg
```
#### Inventory File:
The Ansible inventory file defines the hosts and groups of hosts upon which commands, modules, and tasks in a playbook operate. The default location for the inventory file is /etc/ansible/hosts. You can also create project-specific inventory files in alternate locations.

I have created a custom inventory file for my project..
```sh
vim remote
```
```sh
[group]
<IP>  ansible_user="ec2-user" ansible_port=22 ansible_ssh_private_key_file="ansible.pem"
```
> [amazon] is the group name
I've created only one client server. If we want to add more, we can add under the same group name if all are on same OS
If the server is different OS, we need to create another group.

> Note: Ansible is using SSH protocol to connect to the client servers.

There are two methods to pass commands to ansible client
1. [Adhoc method](https://docs.ansible.com/ansible/latest/user_guide/intro_adhoc.html) : Ansible ad hoc commands are written for a very specific task. It  is a one-line command that lets you perform basic tasks efficiently without writing playbooks.
The syntax of ad-hoc commands to run is as follows and see some of the ad-hoc commands as well.
```sh
$ ansible [pattern] -m [module] -a "[module options]"
```
2. [Playbook](https://docs.ansible.com/ansible/latest/user_guide/playbooks.html) : Playbooks are files consisting of your written code, and they are written in YAML format, which defines the tasks and executes them through the Ansible. Playbooks may include one or more plays. Plays defines a set of activities or tasks to be run on hosts of inventory file.
- We need to strictly follow the syntax suggested by ansible, otherwise it will throw the error. Let's see in the following, some of the suggestions made by ansible.
- Playbooks always start in a YAML file with three dashes.
- Items that start with a single dash shall be treated as list items.
- Whitespaces matters in ansible, so the items are always defined by indentation.
 
To Run the playbook, we need to execute the command shown as follows.
$ ansible-playbook

E.g.: $ ansible-playbook sample.yaml
