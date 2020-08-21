Ansible
=======

# Overview
Ansible is a provisioning tool for automation of machine configuration. It uses a single host system to execute commands on remote systems as defined in a configuration file.

# Architecture

Ansible is installed on a host machine with various configuration files (playbooks) and lists of clients to manage (inventory). When a playbook is executed, the host machine issues commands via SSH or via API endpoints as applicable to achieve what was described in the playbook.

## Hierarchy

* **Playbooks** contain **plays** which contain **tasks** which call **modules** and notify **handlers**.

* **Modules** are essentially libraries for certain tasks. For example, there are modules for communicating with MySQL, reading CSV files, and running arbitrary shell commands.
