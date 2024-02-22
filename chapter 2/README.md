# Chapter 2. Implementing an Ansible Playbook
Create an inventory of managed hosts, write a simple Ansible Playbook, and run the playbook to automate tasks on those hosts.
# Building an Ansible Inventory
- Objectives
  - Describe Ansible inventory concepts and manage a static inventory file.
# Defining the Inventory
An inventory defines a collection of hosts that Ansible manages. These hosts can also be assigned to groups, which can be managed collectively. Groups can contain child groups, and hosts can be members of multiple groups. The inventory can also set variables that apply to the hosts and groups that it defines.

There are two ways to define host inventories. Use a text file to define a static host inventory. Use an Ansible plig-in to generate a dynamic host inventory as needed, using external information providers.

# Specifying Managed Hosts with a Static Inventory
A static inventory file is a text file that specifies the managed hosts that Ansible targets. You can write this file using a number of different formats, including INI-style or YAML. The INI-style format is very common and is used for most examples in this course.
> [!NOTE]
Ansible supports multiple static inventory formats. This section focuses on the most common one, the INI-style format.

In its simplest form, an INI-style static inventory file is a list of hostnames or IP addresses of managed hosts, each on a single line:
```
web1.example.com
web2.example.com
db1.example.com
db2.example.com
192.0.2.42
```
Normally, however, you organize managed hosts into host groups. Host groups allow you to more effectively run Ansible against a collection of systems. In this case, each section starts with a host group name enclosed in square brackets ([]). This is followed by the hostname or an IP address for each managed host in the group, each on a single line.

In the following example, the host inventory defines two host groups: webservers and db-servers.
```
[webservers]
web1.example.com
web2.example.com
192.0.2.42

[db-servers]
db1.example.com
db2.example.com
```
> [!Important]
> Two host groups always exist:
>  - The all host group contains every host explicitly listed in the inventory
>  - The ungrouped host group contains every host explicitly listed in the inventory that is not a member of any other group.
# Defining Nested Groups
Ansible host inventories can include groups of host groups. This is accomplished by creating a host group name with the :children suffix. The following example creates a new group called north-america, which includes all hosts from the usa and canada groups.
```
[usa]
washington1.example.com
washington2.example.com

[canada]
ontario01.example.com
ontario02.example.com

[north-america:children]
canada
usa
```
A group can have both managed hosts and child groups as members. For example, in the previous inventory you could add a [north-america] section that has its own list of managed hosts. That list of hosts would be merged with the additional hosts that the north-america group inherits from its child groups.
# Simplifying Host Specifications with Ranges
You can specify ranges in the hostnames or IP addresses to simplify Ansible host inventories. You can specify either numeric or alphabetic ranges. Ranges have the following syntax:
```
[START:END]
```
Ranges match all values from START to END, inclusively. Consider the following examples:
- 192.168.[4:7].[0:255] matches all IPv4 addresses in the 192.168.4.0/22 network (192.168.4.0 through 192.168.7.255).

- server[01:20].example.com matches all hosts named server01.example.com through server20.example.com.

- [a:c].dns.example.com matches hosts named a.dns.example.com, b.dns.example.com, and c.dns.example.com.

- 2001:db8::[a:f] matches all IPv6 addresses from 2001:db8::a through 2001:db8::f.
If leading zeros are included in numeric ranges, they are used in the pattern. The second example above does not match server1.example.com but does match server07.example.com. To illustrate this, the following example uses ranges to simplify the [usa] and [canada] group definitions from the preceding example:
```
[usa]
washington[1:2].example.com

[canada]
ontario[01:02].example.com
```
# Verifying the Inventory
When in doubt, use the ansible-navigator inventory command to verify a machine's presence in the inventory. 
```
ansible-navigator inventory -m stdout --host washington1.example.com
```
 The following command lists all hosts in the inventory.
 ```
ansible-navigator inventory -m stdout --list
```
The following command lists all hosts in a group.
```
ansible-navigator inventory -m stdout --graph
```
Run the ansible-navigator inventory command to interactively browse inventory hosts and groups:
```
ansible-navigator inventory
```
> [!Important]
> If the inventory contains a host and a host group with the same name, the ansible-navigator inventory command prints a warning.
> Ensure that host groups do not use the same names as hosts in the inventory.

# Overriding the Location of the Inventory
The /etc/ansible/hosts file is considered the system's default static inventory file. However, normal practice is not to use that file but to specify a different location for your inventory files.

The ansible-navigator commands that you use to run playbooks can specify the location of an inventory file on the command line with the --inventory PATHNAME or -i PATHNAME option, where PATHNAME is the path to the desired inventory file.

> [!NOTE]
You can also define a different default location for the inventory file in your Ansible configuration file.

# Dynamic Inventories
Ansible inventory information can also be dynamically generated, using information provided by external databases. The open source community has written a number of dynamic inventory plug-ins that are available from the upstream Ansible project. If those Ansible plug-ins do not meet your needs, you can also write your own.

For example, a dynamic inventory program could contact your Red Hat Satellite server or Amazon EC2 account, and use information stored there to construct an Ansible inventory. Because the program does this when you run Ansible, it can populate the inventory with up-to-date information provided by the service as new hosts are added, and old hosts are removed.

# Managing Ansible Configuration Files
- Objectives
  - Describe where Ansible configuration files are located, how Ansible selects them, and edit them to apply changes to default settings.
# Configuring Ansible
You can create and edit two files in each of your Ansible project directories that configure the behavior of Ansible and the ansible-navigator command:
- ansible.cfg, which configures the behavior of several Ansible tools.
- ansible-navigator.yml, which changes default options for the ansible-navigator command.
# Managing Ansible Settings
You can create an ansible.cfg file in your Ansible project's directory to apply settings that apply to multiple Ansible tools.

The Ansible configuration file consists of several sections, with each section containing settings defined as key-value pairs. Section titles are enclosed in square brackets. For basic operation, use the following two sections:
- [defaults], which sets defaults for Ansible operation
- [privilege_escalation], which configures how Ansible performs privilege escalation on managed hosts
For example, the following is a typical ansible.cfg file:
```
[defaults]
inventory = ./inventory
remote_user = user
ask_pass = false

[privilege_escalation]
become = true
become_method = sudo
become_user = root
become_ask_pass = false
```
The following table explains these parameters:

![Screenshot (510)](https://github.com/Salmamohamedm/Red-Hat-Enterprise-Linux-Automation-with-Ansible-9.0/assets/109488469/30bd08a6-0349-4c6c-95d4-fa3803b8984b)
# Determining Current Configuration Settings
The ansible-navigator config command displays the current Ansible configuration. The command displays the actual value that Ansible uses for each parameter and from which source it retrieves that value, configuration file, or environment variable.

The following output is from the ansible-navigator config command run from a /home/student/project/ directory that contains an ansible.cfg file:
![Screenshot (517)](https://github.com/Salmamohamedm/Red-Hat-Enterprise-Linux-Automation-with-Ansible-9.0/assets/109488469/8c89f3f3-b1d8-4317-902d-45ec7c001bd7)

in the preceding example, each line describes an Ansible configuration parameter.

- The Default ask pass and Default ask vault pass parameters use their default values, indicated by the True value in the Default column.

- The Default become and Default become ask pass parameters have been manually configured to True in the /home/student/project/ansible.cfg configuration file. The Default column is 
  False for these two parameters. The Source column provides the path to the configuration file which defines these parameters, and the Current column shows that the value for these 
  two parameters is True.

- The Default become method parameter has the current value of sudo, and the Default become user parameter has the current value of root.
To exit the interactive mode of the ansible-navigator config command, press Esc or type :q.

> [!NOTE]
  If the project does not include an ansible.cfg file, then Ansible tries to use a ~/.ansible.cfg file in the home directory of the user running Ansible, and if that does not exist, it 
  tries to use an /etc/ansible/ansible.cfg file.
  However, if you are using ansible-navigator, the ansible-navigator command looks for these files inside the automation execution environment. On the current Red Hat-supported 
  execution environments, these files do not exist or are empty.
  If you are using the earlier ansible-playbook command or other Ansible commands that do not use automation execution environments, then Ansible looks for these files on your 
  workstation.



