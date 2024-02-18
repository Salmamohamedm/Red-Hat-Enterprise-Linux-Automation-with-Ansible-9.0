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
