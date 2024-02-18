# Chapter 1. Introducing Ansible
# Installing Ansible
- install automation content navigator on a control node that runs Red Hat Enterprise Linux.
# Outcomes
 - You should be able to install automation content navigator on a control node.

# Procedure 1.1. Instructions
1. On the workstation machine, install the ansible-navigator RPM package that provides automation content navigator so that you can use that machine as your control node.
```
sudo dnf install ansible-navigator
```
> [!NOTE]
 In a production setting, you would need to use subscription-manager to register your system with Red Hat Subscription Management and enable the ansible-automation-platform-2.2-for-rhel-9-x86_64-rpms repository first.


2. Verify that automation content navigator is installed on the system. Run the ansible-navigator command with the --version option.
```
ansible-navigator --version
```
3. Log in to the container registry. Use the podman login command to log in to the automation hub registry at utility.lab.example.com. Use admin as the username and redhat as the password.
```
 podman login utility.lab.example.com
```
4. Download the execution environment container image. Run the ansible-navigator images command to make automation content navigator download the execution environment image and display a list of the available images.
```
ansible-navigator images
```

