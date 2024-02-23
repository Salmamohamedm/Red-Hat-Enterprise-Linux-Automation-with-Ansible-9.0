# Chapter 3. Managing Variables and Facts
Write playbooks that use variables to simplify management of the playbook and facts to reference information about managed hosts.
# Managing Variables
 - Objectives
     - Create and reference variables that affect particular hosts or host groups, the play, or the global environment, and describe how variable precedence works.
# Introduction to Ansible Variables
Ansible supports variables that can be used to store values that can then be reused throughout files in an Ansible project. This can simplify the creation and maintenance of a project and reduce the number of errors.

Variables provide a convenient way to manage dynamic values for a given environment in your Ansible project. Examples of values that variables might contain include:

- Users to create

- Packages to install

- Services to restart

- Files to remove

- Archives to retrieve from the internet

# Naming Variables
Variable names must start with a letter, and they can only contain letters, numbers, and underscores.

The following table illustrates the difference between invalid and valid variable names.

![Screenshot (524)](https://github.com/Salmamohamedm/Red-Hat-Enterprise-Linux-Automation-with-Ansible-9.0/assets/109488469/8ebd946b-3da8-481d-b41a-ed4619ec0dcf)

# Defining Variables
  Variables can be defined in a variety of places in an Ansible project. If a variable is set using the same name in two places, and those settings have different values, precedence 
  determines which value is used.

You can set a variable that affects a group of hosts or only individual hosts. Some variables are facts that can be set by Ansible based on the configuration of a system. Other variables can be set inside the playbook, and affect one play in that playbook, or only one task in that play.


You can also set extra variables on the ansible-navigator run command line by using the --extra-vars or -e option and specifying those variables, and they override all other values for that variable name.
```
ansible-navigator run -m playbook playbook.yml --extra-vars "variable1=value1 variable2=value2"
```
The following simplified list shows ways to define a variable, ordered from the lowest precedence to the highest:

- Group variables defined in the inventory

- Group variables defined in files in a group_vars subdirectory in the same directory as the inventory or the playbook

- Host variables defined in the inventory

- Host variables defined in files in a host_vars subdirectory in the same directory as the inventory or the playbook

- Host facts, discovered at runtime

- Play variables in the playbook (vars and vars_files)

- Task variables

- Extra variables defined on the command line
For example, a variable that is set to affect the all host group is overridden by a variable that has the same name and is set to affect a single host.
One recommended practice is to choose globally unique variable names, so that you do not have to consider precedence rules. However, sometimes you might want to use precedence to cause different hosts or host groups to get different settings than your defaults.

If the same variable name is defined at more than one level, the level with the highest precedence wins. A narrow scope, such as a host variable or a task variable, takes precedence over a wider scope, such as a group variable or a play variable. Variables defined by the inventory are overridden by variables defined by the playbook. Extra variables defined on the command line with the --extra-vars, or -e, option have the highest precedence.


