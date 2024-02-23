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

# Writing and Running Playbooks
- Objectives
  - Write a basic Ansible Playbook and run it using the automation content navigator.
# Ansible Playbooks
The power of Ansible is that you can use playbooks to run multiple, complex tasks against a set of targeted hosts in an easily repeatable manner.

A task is the application of a module to perform a specific unit of work. A play is a sequence of tasks to be applied, in order, to one or more hosts selected from your inventory. A playbook is a text file containing a list of one or more plays to run in a specific order.

Plays enable you to change a lengthy, complex set of manual administrative tasks into an easily repeatable routine with predictable and successful outcomes. In a playbook, you can save the sequence of tasks in a play into a human-readable and immediately runnable form. The tasks themselves, because of the way in which they are written, document the steps needed to deploy your application or infrastructure.

# Formatting an Ansible Playbook
The following example contains one play with a single task.
```
---
- name: Configure important user consistently
  hosts: servera.lab.example.com
  tasks:
    - name: Newbie exists with UID 4000
      ansible.builtin.user:
        name: newbie
        uid: 4000
        state: present
```
A playbook is a text file written in YAML format, and is normally saved with the extension .yml. The playbook uses indentation with space characters to indicate the structure of its data. YAML does not place strict requirements on how many spaces are used for the indentation, but two basic rules apply:

- Data elements at the same level in the hierarchy (such as items in the same list) must have the same indentation.

- Items that are children of another item must be indented more than their parents.

You can also add blank lines for readability.

> [!Important]
> You can only use space characters for indentation; do not use tab characters.
If you use the vi text editor, you can apply some settings which might make it easier to edit your playbooks. For example, you can add the following line to your $HOME/.vimrc file, and when vi detects that you are editing a YAML file, it performs a 2-space indentation when you press the Tab key and automatically indents subsequent lines.

```
autocmd FileType yaml setlocal ai ts=2 sw=2 et
```

A playbook usually begins with a line consisting of three dashes (---) to indicate the start of the document. It might end with three dots (...) to indicate the end of the document, although in practice this is often omitted.

Between those markers, the playbook is defined as a list of plays. An item in a YAML list starts with a single dash followed by a space. For example, a YAML list might appear as follows:

```
- apple
- orange
- grape
```

In the preceding playbook example, the line after --- begins with a dash and starts the first (and only) play in the list of plays.


The play itself is a collection of key-value pairs. Keys in the same play should have the same indentation. The following example shows a YAML snippet with three keys. The first two keys have simple values. The third has a list of three items as a value.

```
name: just an example
  hosts: webservers
  tasks:
    - first
    - second
    - third
```
The initial example play has three keys: name, hosts, and tasks. These keys all have the same indentation.

The first line of the example play starts with a dash and a space (indicating the play is the first item of a list), and then the first key, name. The name key associates an arbitrary string with the play as a label that identifies the purpose of the play. The name key is optional, but is recommended because it helps to document your playbook. This is especially useful when a playbook contains multiple plays.
```
- name: Configure important user consistently
```
The second key in the play is a hosts key, which specifies the hosts against which the play's tasks are run. The hosts key takes a host pattern as a value, such as the names of managed hosts or groups in the inventory.
```
  hosts: servera.lab.example.com
```


Finally, the last key in the play is tasks, whose value specifies a list of tasks to run for this play. This example has a single task, which runs the ansible.builtin.user module with specific arguments (to ensure the newbie user exists and has UID 4000).
```
 tasks:
    - name: newbie exists with UID 4000
      ansible.builtin.user:
        name: newbie
        uid: 4000
        state: present
```
The tasks key is the part of the play that actually lists, in order, the tasks to be run on the managed hosts. Each task in the list is itself a collection of key-value pairs.

In this example, the only task in the play has two keys:
- name is an optional label documenting the purpose of the task. It is a good idea to name all your tasks to help document the purpose of each step of the automation process.
- ansible.builtin.user is the module to run for this task. Its arguments are passed as a collection of key-value pairs, which are children of the module (name, uid, and state).

The following is another example of a tasks key with multiple tasks, each using the ansible.builtin.service module to ensure that a service should start at boot:
```
  tasks:
    - name: Web server is enabled
      ansible.builtin.service:
        name: httpd
        enabled: true

    - name: NTP server is enabled
      ansible.builtin.service:
        name: chronyd
        enabled: true

    - name: Postfix is enabled
      ansible.builtin.service:
        name: postfix
        enabled: true
```

> [!Important]
> The order in which the plays and tasks are listed in a playbook is important, because Ansible runs them in the same order.


# Finding Modules for Tasks
Modules are the tools that plays use to accomplish tasks. Hundreds of modules have been written that do different things. You can usually find a tested, special-purpose module that does what you need, often as part of the default automation execution environment.

Ansible Core 2.11 and later package the modules that you use for tasks in sets called Ansible Content Collections. Each Ansible Content Collection contains a selection of related Ansible content, including modules and documentation.

The ansible-core package provides a single Ansible Content Collection named ansible.builtin. These modules are always available to you. Visit https://docs.ansible.com/ansible/latest/collections/ansible/builtin/ for a list of modules contained in the ansible.builtin collection.

In addition, the default automation execution environment used by ansible-navigator in Red Hat Ansible Automation Platform 2.2, ee-rhel8-supported, includes a number of other Ansible Content Collections.

You can browse these collections by running the ansible-navigator collections command. In the interactive UI, you can type a colon (:) followed by the line number of a collection to get more information about it, including the list of modules and other Ansible content that it provides. You can do the same thing with the line number of a module to get documentation about that module. Press Esc to go back to the preceding list.

You can also download additional Ansible Content Collections from a number of places, including:

The automation hub offered through the Red Hat Hybrid Cloud Console at https://content.redhat.com/ansible/automation-hub

A private automation hub managed by your organization

The community's Ansible Galaxy website at https://galaxy.ansible.com

These can be installed in the collections directory of your Ansible project. Red Hat does not provide formal support for community Ansible Content Collections, only for Red Hat Certified Ansible Content Collections.

Modules are named using fully qualified collection names (FQCNs). This allows the same name to be used for different modules in two Ansible Content Collections without causing conflicts. For example, the copy module provided by the ansible.builtin Ansible Content Collection has ansible.builtin.copy as its FQCN.


> [!Important]
  In earlier versions of Ansible, modules had to be included with Ansible and were named using just their short name, for example the copy module. Ansible might still try to resolve 
  short names if your playbooks use them. However, to avoid errors, it is a good practice to use FQCNs in new playbooks.
  Most modules are idempotent, which means that they can be run safely multiple times, and if the system is already in the correct state, they do nothing. For example, if you run the 
  play from the preceding example a second time, it should report no changes.

# Running Playbooks
The ansible-navigator run command is used to run playbooks. The command is executed on the control node, and the name of the playbook to be run is passed as an argument.
Running the ansible-navigator run command with the -m stdout option prints the output of the playbook run to standard output. If the -m stdout option is not provided, then ansible-navigator runs in interactive mode. 
```
ansible-navigator run -m playbook site.yml
```
When you run the playbook, output is generated to show the play and tasks being executed. The output also reports the results of each task executed.
The following example shows the contents of a simple playbook, and then the result of running it.
```
[user@controlnode playdemo]$ cat webserver.yml
---
- name: Play to set up web server
  hosts: servera.lab.example.com
  tasks:
  - name: Latest httpd version installed
    ansible.builtin.dnf:
      name: httpd
      state: latest
...output omitted...
[user@controlnode playdemo]$ ansible-navigator run \
> -m stdout webserver.yml

PLAY [Play to set up web server] ************************************************

TASK [Gathering Facts] *********************************************************
ok: [servera.lab.example.com]

TASK [Latest httpd version installed] ******************************************
changed: [servera.lab.example.com]

PLAY RECAP *********************************************************************
servera.lab.example.com    : ok=2    changed=1    unreachable=0    failed=0   skipped=0    rescued=0    ignored=0
```
It appears you've successfully executed the playbook webserver.yml using Ansible Navigator. The output indicates that the playbook ran two tasks on the host servera.lab.example.com:

1. Gathering Facts: This task gathers system information from the target host.
2. Latest httpd version installed: This task used the ansible.builtin.dnf module to ensure that the latest version of the httpd package is installed on the target host. It shows that a 
   change was made on the host, indicating that the httpd package was updated to the latest version.   
- The "PLAY RECAP" section summarizes the outcome of the playbook run:

    - ok=2 indicates that both tasks completed successfully.
    - changed=1 shows that one task resulted in a change on the target host, which is the task to ensure the latest httpd version is installed.
    - unreachable=0, failed=0, skipped=0, rescued=0, and ignored=0 indicate that there were no unreachable hosts, failed tasks, skipped tasks, rescued tasks, or ignored tasks during the playbook run.



You should also see that the Latest httpd version installed task is changed for servera.lab.example.com. This means that the task changed something on that host to ensure that its specification was met. In this case, it means that the httpd package was not previously installed or was not the latest version.

In general, tasks in Ansible Playbooks are idempotent, and it is safe to run a playbook multiple times. If the targeted managed hosts are already in the correct state, no changes should be made. For example, assume that the playbook from the previous example is run again:

```
[user@controlnode playdemo]$ ansible-navigator run  -m stdout webserver.yml

PLAY [Play to set up web server] ************************************************

TASK [Gathering Facts] *********************************************************
ok: [servera.lab.example.com]

TASK [Latest httpd version installed] ******************************************
ok: [servera.lab.example.com]

PLAY RECAP *********************************************************************
servera.lab.example.com    : ok=2    changed=0    unreachable=0    failed=0   skipped=0    rescued=0    ignored=0
```
This time, all tasks passed with status ok and no changes were reported.

> [!Note]
  Community Ansible provides an earlier tool called ansible-playbook that takes many of the same options as ansible-navigator run -m stdout and that uses your control node as the 
  execution environment.
  It cannot use automation execution environments, and is only supported by Red Hat in Red Hat Enterprise Linux 9 for narrow use cases.
# Increasing Output Verbosity
The default output provided by the ansible-navigator run command does not provide detailed task execution information. The -v option provides additional information, with up to four levels.
![Screenshot (519)](https://github.com/Salmamohamedm/Red-Hat-Enterprise-Linux-Automation-with-Ansible-9.0/assets/109488469/799a5ce6-ada5-4fa4-a011-66e43ffbac9b)

Syntax Verification
Before executing a playbook, it is good practice to validate its syntax. You can use the ansible-navigator run --syntax-check command to validate the syntax of a playbook. The following example shows the successful syntax validation of a playbook.
```
 ansible-navigator run -m syntax-check webserver.yml
```
# Executing a Dry Run
You can use the --check option to run a playbook in check mode, which performs a "dry run" of the playbook. This causes Ansible to report what changes would have occurred if the playbook were executed, but does not make any actual changes to managed hosts.

The following example shows the dry run of a playbook containing a single task for ensuring that the latest version of the httpd package is installed on a managed host. In this case, the dry run reports that the task would make a change on the managed host.

```
[user@controlnode playdemo]$ ansible-navigator run -m stdout webserver.yml --check

PLAY [Play to set up web server] ***********************************************

TASK [Gathering Facts] *********************************************************
ok: [servera.lab.example.com]

TASK [Latest httpd version installed] ******************************************
changed: [servera.lab.example.com]

PLAY RECAP *********************************************************************
servera.lab.example.com    : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

## References
ssh-keygen, and ssh-copy-id man pages

  [Configuring Ansible: Ansible Documentation](https://docs.ansible.com/ansible/latest/installation_guide/intro_configuration.html)
