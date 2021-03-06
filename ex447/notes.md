Some sources that might be handy:
* `https://ansible.github.io/workshops/exercises/ansible_rhel/`
* `https://docs.ansible.com/ansible/2.8/user_guide/playbooks_best_practices.html#`
* `https://goetzrieger.github.io`

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
These can be configured in hosts file etc.
```
General for all connections:

ansible_host
The name of the host to connect to, if different from the alias you wish to give to it.

ansible_port
The ssh port number, if not 22

ansible_user
The default ssh user name to use.
```

Example where 'ansible_host' is set to 'localhost' for all the hosts in the group 'sputnik':
```
---
all:
  children:
    sputnik:
      hosts:
        lab-web01:
        lab-web02:
        lab-web03:
      vars:
        ansible_host: localhost
```

Example where 'ansible_host' is set to 'localhost' for the host 'lab-web01':
```
---
all:
  children:
    sputnik:
      hosts:
        lab-web01:
          ansible_host: localhost
        lab-web02:
        lab-web03:
```

Limit the hosts the playbook is run against:
```console
-l 'SUBSET', --limit 'SUBSET'
          further limit selected hosts to an additional pattern
```

Example:
This will target all hosts that begin with lab:
```console
  [birger@tower]$ ansible-playbook delegation.yaml -i sputnik/hosts -l 'lab*'
```

Set remote user:
```
-u 'REMOTE_USER', --user 'REMOTE_USER'
          connect as this user (default=None)
```

### Set up directories containing multiple host variable files for some of your managed hosts

*You can also add group_vars/ and host_vars/ directories to your playbook directory. The ansible-playbook command looks for these directories in the current working directory by default. Other Ansible commands (for example, ansible, ansible-console, and so on) will only look for group_vars/ and host_vars/ in the inventory directory. If you want other commands to load group and host variables from a playbook directory, you must provide the --playbook-dir option on the command line. If you load inventory files from both the playbook directory and the inventory directory, variables in the playbook directory will override variables set in the inventory directory.*

*Keeping your inventory file and variables in a git repo (or other version control) is an excellent way to track changes to your inventory and host variables.*

Example:
```console
/etc/ansible/host_vars/
└── my-server
    ├── cluster_settings.yaml
    └── db_settings.yaml
```

#### Directory Layout for best practice:

Example one:
```
The top level of the directory would contain files and directories like so:

production                # inventory file for production servers
staging                   # inventory file for staging environment

group_vars/
   group1.yml             # here we assign variables to particular groups
   group2.yml
host_vars/
   hostname1.yml          # here we assign variables to particular systems
   hostname2.yml

library/                  # if any custom modules, put them here (optional)
module_utils/             # if any custom module_utils to support modules, put them here (optional)
filter_plugins/           # if any custom filter plugins, put them here (optional)

site.yml                  # master playbook
webservers.yml            # playbook for webserver tier
dbservers.yml             # playbook for dbserver tier

roles/
    common/               # this hierarchy represents a "role"
        tasks/            #
            main.yml      #  <-- tasks file can include smaller files if warranted
        handlers/         #
            main.yml      #  <-- handlers file
        templates/        #  <-- files for use with the template resource
            ntp.conf.j2   #  <------- templates end in .j2
        files/            #
            bar.txt       #  <-- files for use with the copy resource
            foo.sh        #  <-- script files for use with the script resource
        vars/             #
            main.yml      #  <-- variables associated with this role
        defaults/         #
            main.yml      #  <-- default lower priority variables for this role
        meta/             #
            main.yml      #  <-- role dependencies
        library/          # roles can also include custom modules
        module_utils/     # roles can also include custom module_utils
        lookup_plugins/   # or other types of plugins, like lookup in this case

    webtier/              # same kind of structure as "common" was above, done for the webtier role
    monitoring/           # ""
    fooapp/               # ""
```

Example two:
```
inventories/
   production/
      hosts               # inventory file for production servers
      group_vars/
         group1.yml       # here we assign variables to particular groups
         group2.yml
      host_vars/
         hostname1.yml    # here we assign variables to particular systems
         hostname2.yml

   staging/
      hosts               # inventory file for staging environment
      group_vars/
         group1.yml       # here we assign variables to particular groups
         group2.yml
      host_vars/
         stagehost1.yml   # here we assign variables to particular systems
         stagehost2.yml

library/
module_utils/
filter_plugins/

site.yml
webservers.yml
dbservers.yml

roles/
    common/
    webtier/
    monitoring/
    fooapp/
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

You can specify 'become' for both tasks and plays.

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

To list available plugins:
```console
ansible-doc -t lookup -l
```

List information about a specific plugin:
```console
ansible-doc -l lookup file
```

### Populate variables with data from external sources using lookup plugins
https://www.devopsschool.com/blog/deep-dive-into-lookup-plugins-in-ansible-with-example/

```yaml
---
- hosts: all
  vars:
    motd_value: "{{ lookup('file', '/etc/motd') }}"
  tasks:
    - debug:
        msg: "{{ motd_value }}"
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
- `https://www.educba.com/ansible-filters/`

```yaml
- name: Looping over dictionaries
  gather_facts: false
  hosts: localhost
  vars:
    tag_data:
      Environment: dev
      Application: payment
  become: false
  tasks:
    - name: Using dict2items
      ansible.builtin.debug:
        msg: "{{ item.key }} - {{ item.value }}"
      loop: "{{ tag_data | dict2items }}"
```

### Inspect, validate, and manipulate variables containing networking information with filters
*ipaddr() is a Jinja2 filter designed to provide an interface to netaddr Python package from within Ansible. It can operate on strings or lists of items, test various data to check if they are valid IP addresses and manipulate the input data to extract requested information. ipaddr() works both with IPv4 and IPv6 addresses in various forms, there are also additional functions available to manipulate IP subnets and MAC addresses.*

https://docs.ansible.com/ansible/2.4/playbooks_filters_ipaddr.html

https://docs.ansible.com/ansible/latest/user_guide/playbooks_filters_ipaddr.html

```yaml
Example us of an IPv4 filter:

{{ myvar | ipv4 }}

And similar example of an IPv6 filter:

{{ myvar | ipv6 }}
```

```yaml
Here’s an example test to look for IPv4 addresses:

'192.168.0.1'       -> 192.168.0.1
'192.168.32.0/24'   -> 192.168.32.0/24
'fe80::100/10'      -> False
45443646733         -> False
'523454/24'         -> 0.7.252.190/24
```

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

If you have an RedHat Developer account you can get a 60 day trial version of Ansible Tower: https://www.redhat.com/en/technologies/management/ansible/try-it?extIdCarryOver=true&sc_cid=701f2000001OH7YAAW

Its also possible to install awx.

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

```
Creating a new User:
1. Users
2. Create a new user
3. Fill in the required information
4. Click Save
```

```
Associating a User to a Team:
1. Teams
2. Select the Team you want to associate the user to
3. Click USERS tab
4. Click Add User
5. Select the User you want to add
```

# Manage inventories and credentials

How to import inventory file with CLI tool:
awx-manage inventory_import --inventory-name Linux_UA_Hosts --source hosts_add

### Manage advanced inventories
https://goetzrieger.github.io/ansible-tower-advanced/9-advanced-inventories/

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

- `https://www.unixarena.com/2019/03/backup-restore-ansible-awx-tower-cli.html/`

```console
[birger@tower ansible-automation-platform-setup-bundle-1.2.1-1]$ ./setup.sh -h
Usage: ./setup.sh [Options] [-- Ansible Options]

Options:
  -i INVENTORY_FILE     Path to ansible inventory file (default: inventory)
  -e EXTRA_VARS         Set additional ansible variables as key=value or YAML/JSON
                        i.e. -e bundle_install=false will force an online install

  -b                    Perform a database backup in lieu of installing
  -r                    Perform a database restore in lieu of installing
  -k                    Generate and distribute a new SECRET_KEY

  -h                    Show this help message and exit

Ansible Options:
  Additional options to be passed to ansible-playbook can be added
  following the -- separator
```

```console
[birger@tower ansible-automation-platform-setup-bundle-1.2.1-1]$ cat roles/backup/defaults/main.yml
---
backup_dir: /var/backups/tower/
backup_dest: "{{ playbook_dir }}/"
```

Backing up Ansible Tower DB:
```console
[birger@tower ansible-automation-platform-setup-bundle-1.2.1-1]$ sudo ./setup.sh -b

[birger@tower ansible-automation-platform-setup-bundle-1.2.1-1]$ sudo file tower-backup-2021-05-07-08\:43\:54.tar.gz
tower-backup-2021-05-07-08:43:54.tar.gz: gzip compressed data, last modified: Fri May  7 15:44:12 2021, from Unix, original size 327680
```

Setting a custom directory to store the backups:
```console
[birger@tower ansible-automation-platform-setup-bundle-1.2.1-1]$ sudo ./setup.sh -b -e backup_dest='/opt/tower-backups'

[birger@tower ansible-automation-platform-setup-bundle-1.2.1-1]$ ll /opt/tower-backups/
total 312
-rw-------. 1 root root 318345 May  7 08:53 tower-backup-2021-05-07-08:53:24.tar.gz
lrwxrwxrwx. 1 root root     58 May  7 08:53 tower-backup-latest.tar.gz -> /opt/tower-backups/tower-backup-2021-05-07-08:53:24.tar.gz
```

# Labs

Some labs i found browsing the web

### ANSIBLE GETTING STARTED
https://goetzrieger.github.io/ansible-getting-started/

### ANSIBLE TOWER GETTING STARTED
https://goetzrieger.github.io/ansible-tower-getting-started/

### ANSIBLE TOWER ADVANCED
https://goetzrieger.github.io/ansible-tower-advanced/

### ANSIBLE COLLECTIONS
https://goetzrieger.github.io/ansible-collections/
