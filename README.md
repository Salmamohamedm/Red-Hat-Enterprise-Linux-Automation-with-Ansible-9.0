# Red-Hat-Enterprise-Linux-Automation-with-Ansible-9.0
Describe the fundamental concepts of Ansible and how it is used, and install development tools from Red Hat Ansible Automation Platform.
# What Is Ansible?
Ansible is an open source automation platform. It is a simple automation language that can accurately describe an IT application infrastructure in Ansible Playbooks. It is also an automation engine that runs Ansible Playbooks.

Ansible can manage powerful automation tasks and can adapt to many workflows and environments. At the same time, new users of Ansible can very quickly use it to become productive.
# Ansible Is Simple
Ansible Playbooks provide human-readable automation. This means that playbooks are automation tools that are also easy for humans to read, comprehend, and change. No special coding skills are required to write them. Playbooks execute tasks in order. The simplicity of playbook design makes them usable by every team, which allows people new to Ansible to get productive quickly.

# Ansible Is Powerful
You can use Ansible to deploy applications for configuration management, for workflow automation, and for network automation. You can use Ansible to orchestrate the entire application lifecycle.

# Ansible Is Agentless
Ansible is built around an agentless architecture. Typically, Ansible connects to the hosts it manages by using OpenSSH or WinRM and runs tasks, often (but not always) by pushing out small programs called Ansible modules to those hosts. These programs are used to put the system in a specific desired state. Any modules that are pushed are removed when Ansible has finished its tasks. You can start using Ansible almost immediately because no special agents need to be approved for use and then deployed to the managed hosts. Because there are no agents and no additional custom security infrastructure, Ansible is more efficient and more secure than other alternatives.

- Ansible has a number of important strengths:

   - Cross platform support: Ansible provides agentless support for Linux, Windows, UNIX, and network devices, in physical, virtual, cloud, and container environments.

  -  Human-readable automation: Ansible Playbooks, written as YAML text files, are easy to read and help ensure that everyone understands what they do.

   - Precise application descriptions: Every change can be made by Ansible Playbooks, and every aspect of your application environment can be described and documented.

  -  Easy to manage in version control: Ansible Playbooks and projects are plain text. They can be treated like source code and placed in your existing version control system.

   - Support for dynamic inventories: The list of machines that Ansible manages can be dynamically updated from external sources to capture the correct, current list of all managed 
     servers all the time, regardless of infrastructure or location.

   - Orchestration that integrates easily with other systems: HP SA, Puppet, Jenkins, Red Hat Satellite, and other systems that exist in your environment can be leveraged and 
     integrated into your Ansible workflow.
