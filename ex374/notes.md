# Study points for the exam
## Understand and use Git
Protip: `man gittutorial`

### Clone a Git repository
```
git clone https://my.repo.example.com/git-repo.git

git clone git@my.repo.exampole.com/git-repo.git
```

### Create, modify and push files in a Git repository
```
touch README.md
git add README.md
git commit -m "Add README for project
git push
```

## Manage inventory variables
https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html#defining-variables-in-inventory

### Structure host and group variables using multiple files per host or group
Variable files and directories can be stored either in the same directory as the playbook or the inventory.

```
.
├── group_vars
│   └── webservers
│       ├── apache.yml
│       └── firewall.yml
├── host_vars
│   └── database01.yml
└── playbook.yml
```

### Use special variables to override the host, port, or remote user for a specific host
```
webservers:
    hosts:
        webserver01:
            ansible_host: database01
            ansible_port: 23
            ansible_user: myuser
```

### Set up directories containing multiple host variable files for managed hosts
```
host_vars
├── database01.yml
└── database02
    ├── firewall.yml
    └── mysql.yml
```

### Override names used in inventory files with a different name or IP address
```
webservers:
    hosts:
        my_web_server:
            ansible_host: webserver01.example.com
```

## Manage task execution
### Control privilege execution
Add the `become` keyword on:
- Play level
- Task level
- Role level

### Run selected tasks from a playbook
Use the `--tags` option to specify tasks
Use the ``--skip-tags` keyword to skip certain tasks

## Transform data with filters and plugins

### Populate variables with data from external sources using lookup plugins
`my_var: "{{ lookup('file', 'some_file') }}`

### Use lookup and query functions to incorporate data from external sources into playbooks and deployed template files
```yaml
- debug:
    var: "{{ lookup('template', 'some_template.j2') }}"
```

### Implement loops using structures other than simple lists using lookup plugins and filters
```yaml
- debug:
    msg: "{{ item.key }}"
  loop: "{{ groceries | dict2items }}"
```

### Inspect, validate, and manipulate variables containing networking information with filters
https://docs.ansible.com/ansible/latest/user_guide/playbooks_filters.html#managing-network-interactions

## Delegate tasks

### Run a task for a managed host on a different host
```yaml
- name: Run task on different host
  package:
    name: cowsay
    state: latest
  delegate_to: somehost.example.com
```

### Control whether facts gathered by a task are delegated to the managed host or the controlling host
```yaml
- name: Run task on different host
  package:
    name: cowsay
    state: latest
  delegate_to: somehost.example.com
  delegate_facts: true
```

## Manage content collections
### Create a content collection
```
ansible-galaxy collection init some.collection
cd some/collection
ansible-galaxy collection build
```

### Install a content collection
`ansible-galaxy collection install some.collection`

### Publish a content collection
`ansible-galaxy collection publish some-collection-1.0.0.tar.gz`

## Manage execution environments

### Build an execution environment
```
dnf install ansible-builder
ansible-builder build
```

### Run playbooks in a execution environment
```
ansible-navigator run playbook.yml --eei homemade-ee:v1.1 -m stdout
```

### Upload execution environments into automation hub
```
podman build -t hub.example.com/lab/homemade-ee:v1.1
podman login -u home -p made hub.example.com
podman push hub.example.com/lab/homemade-ee:v1.1
```

### Using execution environments in automation controller

## Manage inventories and credentials
### Manage advanced inventories
### Create a dynamic inventory from an identity management server or a database server
### Create machine credentials to access inventory hosts
### Create a source control credential

## Manage automation controller
### Run playbooks in automation controller
### Pull content into automation controller from either Git or automation hub
### Pull an execution environment from automation hub and run a playbook in it

