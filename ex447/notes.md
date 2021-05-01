Some sources that might be handy:
`https://ansible.github.io/workshops/exercises/ansible_rhel/`
`https://docs.ansible.com/ansible/2.8/user_guide/playbooks_best_practices.html#`

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
Ansible loads host and group variable files by searching paths relative to the inventory file or the playbook file. If your inventory file at /etc/ansible/hosts contains a host named ‘foosball’ that belongs to two groups, ‘raleigh’ and ‘webservers’, that host will use variables in YAML files at the following locations:

```
/etc/ansible/group_vars/raleigh # can optionally end in '.yml', '.yaml', or '.json'
/etc/ansible/group_vars/webservers
/etc/ansible/host_vars/foosball
```

You can also add group_vars/ and host_vars/ directories to your playbook directory. The ansible-playbook command looks for these directories in the current working directory by default. Other Ansible commands (for example, ansible, ansible-console, and so on) will only look for group_vars/ and host_vars/ in the inventory directory. If you want other commands to load group and host variables from a playbook directory, you must provide the --playbook-dir option on the command line. If you load inventory files from both the playbook directory and the inventory directory, variables in the playbook directory will override variables set in the inventory directory.

```
Files for group and host variables should be stored in the following directories:
  - /etc/ansible/group_vars
  - /etc/ansible/host_vars

Group vars:
  - /etc/ansible/group_vars/webservers.yaml

Host vars:
  - /etc/ansible/host_vars/webserver01.yaml
```

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
```console
-l 'SUBSET', --limit 'SUBSET'
          further limit selected hosts to an additional pattern

-u 'REMOTE_USER', --user 'REMOTE_USER'
          connect as this user (default=None)
```

```
General for all connections:

ansible_host
The name of the host to connect to, if different from the alias you wish to give to it.
ansible_port
The ssh port number, if not 22
ansible_user
The default ssh user name to use.
```

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
```
all:
  children:
    databases:
      hosts:
        database:
    webservers:
      hosts:
        webserver:
          ansible_host: database
```
# Manage task execution

### Control privilege execution
```yaml
- name: Ensure the httpd service is running
  service:
    name: httpd
    state: started
  become: yes
```

### Run selected tasks
```console
--skip-tags
          only run plays and tasks whose tags do not match these values

 -t, --tags
          only run plays and tasks tagged with these values
```

# Transform data with filters and plugins

### Populate variables with data from external sources using lookup plugins
https://www.devopsschool.com/blog/deep-dive-into-lookup-plugins-in-ansible-with-example/

```
vars:
  motd_value: "{{ lookup('file', '/etc/motd') }}"
tasks:
  - debug:
      msg: "motd value is {{ motd_value }}"
```

### Use lookup and query functions to template data from external sources into playbooks and deployed template files
```yaml
---
- name: set variable to content of motd file
  hosts: all
  vars:
    motd_value: "{{ lookup('file', '/etc/motd') }}"
  tasks:
    - name: display the content of motd
      debug:
        msg: "{{ motd_value }}"
```

### Implement loops using structures other than simple lists using lookup plugins and filters
https://www.educba.com/ansible-filters/

### Inspect, validate, and manipulate variables containing networking information with filters

# Delegate tasks

https://docs.ansible.com/ansible/latest/user_guide/playbooks_delegation.html

### Run a task for a managed host on a different host, then control whether facts gathered by that task are delegated to the managed host or the other host
```yaml
---
- hosts: app_servers

  tasks:
    - name: Gather facts from db servers
      ansible.builtin.setup:
      delegate_to: "{{ item }}"
      delegate_facts: true
      loop: "{{ groups['dbservers'] }}"
```

# Install Ansible Tower

https://www.unixarena.com/2019/03/ansible-tower-awx-installing-configuring-tower-cli.html/

https://releases.ansible.com/ansible-tower/setup/ansible-tower-setup-latest.tar.gz

```
root@localhost:~$ tar xvzf ansible-tower-setup-latest.tar.gz

root@localhost:~$ cd ansible-tower-setup-<tower_version>

vim inventory

as root: ./setup.sh
```

### Perform basic configuration of Ansible Tower after configuration

# Manage access for Ansible Tower

### Create Ansible Tower users and teams and make associations of one to the other

# Manage inventories and credentials

### Manage advanced inventories

awx-manage inventory_import --inventory-name Linux_UA_Hosts --source hosts_add

### Create a dynamic inventory from an identity management server or a database server

### Create machine credentials to access inventory hosts
```
1. Credentials
2. Create a new credential
3. Credential type: Machine
4. Insert SSH Private Key or SSH CERTIFICATE
5. Fill in the required fields
6. Click Save
```

### Create a source control credential
```
1. Credentials
2. Create a new credential
3. Credential type: Source Control
4. Insert SSH Private Key
5. Fill in the required fields
6. Click Save
```
https://www.unixarena.com/2018/12/ansible-tower-awx-store-credential-custom-credentials-type.html/

# Manage projects

### Create a job template

https://www.unixarena.com/2019/02/ansible-tower-awx-creating-new-job-template.html/

# Manage job workflows

### Create a job workflow template

https://www.unixarena.com/2019/03/ansible-tower-awx-creating-workflow-template.html/

# Work with the Ansible Tower API

### Write an API scriptlet to launch a job

https://www.unixarena.com/2019/03/ansible-tower-awx-trigger-ansible-job-using-rest-api.html/

# Back up Ansible Tower

### Back up an instance of Ansible Tower

https://www.unixarena.com/2019/03/backup-restore-ansible-awx-tower-cli.html/
