Study points for the exam
You should be able to accomplish the following grouped tasks without assistance:

# Understand and use Git

### Clone a Git repository
```console
git clone git@github.com:BirgerM/RedHat-Training.git

git clone https://github.com/BirgerM/RedHat-Training.git
```

### Update, modify and create files in a Git repository
```console
touch some-file.txt
```

### Add those modified files back into the Git repository
```console
git add some-file.txt

git commit -m "add some file"

git push
```

# Manage inventory variables
https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html#intro-inventory
https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html#adding-variables-to-inventory

### Structure host and group variables using multiple files per host or group
Files for group and host variables should be stored in the following directories:
  - /etc/ansible/group_vars
  - /etc/ansible/host_vars

Group vars:
  - /etc/ansible/group_vars/webservers.yaml

Host vars:
  - /etc/ansible/host_vars/webserver01.yaml

Example groupvars:
```yaml
---
ntp_server: acme.example.org
database_server: storage.example.org
```

Its also possible to have subdirectories to store the variables. This applies to both groups and hosts as long as the subdirectory is named after the group or host. Example for the `webservers` group:
```console
/etc/ansible/
├── ansible.cfg
├── group_vars
│   └── webservers
│       ├── cluster_settings.yaml
│       └── db_settings.yaml
```

### Use special variables to override the host, port, or remote user Ansible uses for a specific host

### Set up directories containing multiple host variable files for some of your managed hosts
*You can also add group_vars/ and host_vars/ directories to your playbook directory. The ansible-playbook command looks for these directories in the current working directory by default. Other Ansible commands (for example, ansible, ansible-console, and so on) will only look for group_vars/ and host_vars/ in the inventory directory. If you want other commands to load group and host variables from a playbook directory, you must provide the --playbook-dir option on the command line. If you load inventory files from both the playbook directory and the inventory directory, variables in the playbook directory will override variables set in the inventory directory.

Keeping your inventory file and variables in a git repo (or other version control) is an excellent way to track changes to your inventory and host variables.*

Example:
```console
/etc/ansible/host_vars/
└── my-server.testing
    ├── cluster_settings.yaml
    └── db_settings.yaml
```
### Override the name used in the inventory file with a different name or IP address

# Manage task execution

### Control privilege execution

### Run selected tasks

# Transform data with filters and plugins

### Populate variables with data from external sources using lookup plugins

### Use lookup and query functions to template data from external sources into playbooks and deployed template files

### Implement loops using structures other than simple lists using lookup plugins and filters

### Inspect, validate, and manipulate variables containing networking information with filters

# Delegate tasks

### Run a task for a managed host on a different host, then control whether facts gathered by that task are delegated to the managed host or the other host

# Install Ansible Tower

### Perform basic configuration of Ansible Tower after configuration

# Manage access for Ansible Tower

### Create Ansible Tower users and teams and make associations of one to the other

# Manage inventories and credentials

### Manage advanced inventories

### Create a dynamic inventory from an identity management server or a database server

### Create machine credentials to access inventory hosts

### Create a source control credential

# Manage projects

### Create a job template

# Manage job workflows

### Create a job workflow template

# Work with the Ansible Tower API

### Write an API scriptlet to launch a job

# Back up Ansible Tower

### Back up an instance of Ansible Tower
