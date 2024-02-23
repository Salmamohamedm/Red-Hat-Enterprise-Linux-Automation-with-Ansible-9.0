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
