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


# Managing Settings for Automation Content Navigator
You can create a configuration file (or settings file) for ansible-navigator to override the default values of its configuration settings. The settings file can be in JSON (.json) or YAML (.yml or .yaml) format. This discussion focuses on the YAML format.

Automation content navigator looks for a settings file in the following order and uses the first file that it finds:
- If the ANSIBLE_NAVIGATOR_CONFIG environment variable is set, then use the configuration file at the location it specifies.
- An ansible-navigator.yml file in your current Ansible project directory.
- A ~/.ansible-navigator.yml file (in your home directory). Notice that it has a "dot" at the start of its file name.
Just like the Ansible configuration file, each project can have its own automation content navigator settings file.

## The following ansible-navigator.yml file configures some common settings:
![Screenshot (518)](https://github.com/Salmamohamedm/Red-Hat-Enterprise-Linux-Automation-with-Ansible-9.0/assets/109488469/1be2db48-2826-49c4-a2c6-5552c02e9f83)

# Configuring Connections
Ansible needs to know how to communicate with its managed hosts. One of the most common reasons to change the configuration file is to control which methods and users Ansible uses to administer managed hosts. The following are some examples of required information:
- The location of the inventory that lists the managed hosts and host groups
- The connection protocol to use to communicate with the managed hosts (by default, SSH), and whether a nonstandard network port is needed to connect to the server
- The remote user to use on the managed hosts; this could be root or it could be an unprivileged user
- If the remote user is unprivileged, Ansible needs to know if it should try to escalate privileges to root and how to do it (for example, by using sudo)
- Whether to prompt for an SSH password or sudo password to log in or gain privileges
# Inventory Location
In the [defaults] section of the ansible.cfg file, the inventory parameter can point directly to a static inventory file, or to a directory containing multiple static inventory files and dynamic inventory scripts.
```
[defaults]
inventory = ./inventory
```
# Connection Settings
- By default, Ansible connects to managed hosts using the SSH protocol. The most important parameters that control how Ansible connects to the managed hosts are set in the [defaults] 
  section.
  
- If not configured otherwise, Ansible attempts to connect to the managed host using the same username as the local user running the Ansible commands. To specify a different remote 
  user, set the remote_user parameter to that username. If the local user running Ansible has private SSH keys configured that allow them to authenticate as the remote user on the 
  managed hosts, Ansible automatically logs in.
  
- If you do not have SSH key-based authentication configured for your remote user, it is possible to use password-based authentication. However, to use password-based SSH 
  authentication with ansible-navigator, you need to configure ansible-navigator so that it does not generate playbook artifacts (log files that record information about playbook runs).

## The following example is a minimal ansible-navigator.yml file that disables the generation of playbook artifacts:
```
ansible-navigator:
  playbook-artifact:
    enable: false
```
With this configuration, you can run ansible-navigator run with the -m stdout and --ask-pass options in addition to the playbook that you want to run, so that the ansible-navigator command prompts you for the remote user's SSH password.With this configuration, you can run ansible-navigator run with the -m stdout and --ask-pass options in addition to the playbook that you want to run, so that the ansible-navigator command prompts you for the remote user's SSH password.

> [!Important]
> You can set the ask_pass = true parameter in the defaults section of the ansible.cfg file so that you are always prompted by Ansible tools for the SSH password.
 If you do not disable playbook artifact generation for ansible-navigator, or if you do not use -m stdout when running ansible-navigator, the ansible-navigator command might hang or 
 otherwise fail to run.

If you have password-based SSH authentication set up, you can configure key-based SSH authentication.

The first step is to make sure that the user on the control node has an SSH key pair configured in ~/.ssh. You can run the ssh-keygen command to generate a key pair.
For a single existing managed host, you can install your public key on the managed host and use the ssh-copy-id command to populate your local ~/.ssh/known_hosts file with its host key, as follows:
```
 ssh-copy-id root@web1.example.com
```
> [!NOTE]
You can also use an Ansible Playbook to deploy your public key to the remote_user account on all managed hosts using the authorized_key module.


A play that ensures that your public key is deployed to the managed hosts' root accounts might read as follows:
```
- name: Public key is deployed to managed hosts for Ansible
  hosts: all

  tasks:
    - name: Ensure key is in root's ~/.ssh/authorized_hosts
      ansible.posix.authorized_key:
        user: root
        state: present
        key: '{{ item }}'
      with_file:
        - ~/.ssh/id_rsa.pub
```
Because the managed host would not have SSH key-based authentication configured yet, you would have to run the playbook using the ansible-navigator run command with the --ask-pass option for the command to authenticate as the remote user.

# Escalating Privileges
For security and auditing reasons, Ansible might need to connect to remote hosts as an unprivileged user before escalating privileges to get administrative access as the root user. This can be set up in the [privilege_escalation] section of the Ansible configuration file.

To enable privilege escalation by default, set the become = true parameter in the configuration file. Even if this is set by default, various methods exist to override it when running ad hoc commands or Ansible Playbooks. (For example, there might be times when you want to run a task or play that does not escalate privileges.)

The become_method parameter specifies how to escalate privileges. Several options are available, but the default is to use sudo. Likewise, the become_user parameter specifies which user to escalate to, but the default is root.

If the become_method mechanism chosen requires the user to enter a password to escalate privileges, you can set the become_ask_pass = true parameter in the configuration file.
> [!Important]
> If you have become_ask_pass = true set when you use ansible-navigator, you also need to disable playbook artifact generation and use -m stdout as previously discussed in this section.


 > [!NOTE]
On Red Hat Enterprise Linux 8 and 9, the default configuration of /etc/sudoers grants all users in the wheel group the ability to use sudo to become root after entering their password.
> One way to enable a user (someuser in the following example) to use sudo to become root without a password is to install a file with the appropriate parameters into the /etc/sudoers.d directory
> (owned by root, with octal permissions 0400):
```
## password-less sudo for Ansible user
someuser ALL=(ALL) NOPASSWD:ALL
```
Think through the security implications of whatever approach you choose for privilege escalation. Different organizations and deployments might have different tradeoffs to consider.

The following example ansible.cfg file assumes that you can connect to the managed hosts as someuser using SSH key-based authentication, and that someuser can use sudo to run commands as root without entering a password:
```
[defaults]
inventory = ./inventory
remote_user = someuser
ask_pass = false

[privilege_escalation]
become = true
become_method = sudo
become_user = root
become_ask_pass = false
```
> [!NOTE]
Setting become = true means that all tasks in the plays in playbooks that you run are run as the user specified by the become_user parameter, usually the root user. From a security perspective, rather than setting become = true in the ansible.cfg file, it might be a better practice to configure privilege escalation for specific plays in your playbooks, or on a task-by-task basis as needed


# Configuration File Comments
Both the ansible.cfg file and ansible-navigator.yml support the number sign (#) at the start of a line as a comment character. The number sign at the start of a line comments out the entire line.

In addition, the ansible.cfg file supports the semicolon (;) as a comment character. The semicolon character comments out everything to the right of it on the line.

## References
ssh-keygen, and ssh-copy-id man pages

  [Configuring Ansible: Ansible Documentation](https://docs.ansible.com/ansible/latest/installation_guide/intro_configuration.html)
