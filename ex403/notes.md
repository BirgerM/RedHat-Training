# Study points for the exam
You should be able to perform these tasks:

# Documentation
https://access.redhat.com/documentation/en-us/red_hat_satellite/6.6/

https://access.redhat.com/documentation/en-us/red_hat_satellite/6.6/html/administering_red_hat_satellite/index

https://access.redhat.com/documentation/en-us/red_hat_satellite/6.6/html/content_management_guide/index

https://access.redhat.com/documentation/en-us/red_hat_satellite/6.6/html/managing_hosts/index

https://access.redhat.com/documentation/en-us/red_hat_satellite/6.6/html/provisioning_guide/index

# Configure Red Hat Satellite server
## Create and configure organizations and locations

https://access.redhat.com/documentation/en-us/red_hat_satellite/6.6/html/content_management_guide/managing_locations

https://access.redhat.com/documentation/en-us/red_hat_satellite/6.6/html/content_management_guide/managing_organizations

```
Administer -> Locations -> New Location
```

```
Administer -> Organizations -> New Organization
```

## Create and configure users and roles

https://access.redhat.com/documentation/en-us/red_hat_satellite/6.6/html/administering_red_hat_satellite/chap-red_hat_satellite-administering_red_hat_satellite-users_and_roles

Administer -> Users -> Create User
Administer -> Roles -> Create Role

## Create and configure Ansible® roles

https://access.redhat.com/documentation/en-us/red_hat_satellite/6.6/html/administering_red_hat_satellite/chap-red_hat_satellite-administering_red_hat_satellite-managing_ansible_roles

*In Satellite, you can import Ansible roles and Red Hat Enterprise Linux system roles to help with automation of routine tasks. Ansible is enabled by default on Satellite and Capsule. If you use custom or third party Ansible roles, you must add the roles to the /etc/ansible/roles directory of the Capsule or Satellite where you want to use them.*

```
Configure -> Ansible Roles
Configure -> Ansible Variables
```

## Create and configure Ansible variables

https://access.redhat.com/documentation/en-us/red_hat_satellite/6.6/html/administering_red_hat_satellite/chap-red_hat_satellite-administering_red_hat_satellite-managing_ansible_roles#sect-Red_Hat_Satellite-Administering_Red_Hat_Satellite-Creating_Ansible_Variables

```
Configure -> Ansible Variables -> New Ansible Variable
```

# Manage content and software channels

https://access.redhat.com/documentation/en-us/red_hat_satellite/6.6/html/content_management_guide/index

## Synchronize Red Hat Enterprise Linux content

https://access.redhat.com/documentation/en-us/red_hat_satellite/6.6/html/content_management_guide/importing_red_hat_content#Importing_Red_Hat_Content-Synchronizing_Red_Hat_Repositories

For Web UI Users
1. In the Satellite web UI, navigate to Content > Products and select the product that contains the repositories that you want to synchronize.
2. Select the repositories that you want to synchronize and click Sync Now.

## Configure subscriptions, content, and content views

### Subscriptions

https://access.redhat.com/documentation/en-us/red_hat_satellite/6.6/html/content_management_guide/managing_subscriptions

### Content

https://access.redhat.com/documentation/en-us/red_hat_satellite/6.6/html/content_management_guide/importing_custom_content

### Content Views

https://access.redhat.com/documentation/en-us/red_hat_satellite/6.6/html/content_management_guide/managing_content_views

```
Content -> Content Views -> Create New View
```

## Create custom content channels and populate them with software

## Create and configure development life cycles

Content -> Lifecycle Environment Paths -> Create Environment Path

## Sign an RPM

https://access.redhat.com/articles/3359321

### Create gpg key
```
[root@foreman ~]# gpg --gen-key
gpg (GnuPG) 2.0.22; Copyright (C) 2013 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Please select what kind of key you want:
   (1) RSA and RSA (default)
   (2) DSA and Elgamal
   (3) DSA (sign only)
   (4) RSA (sign only)
Your selection?
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (2048)
Requested keysize is 2048 bits
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 1y
Key expires at Sun 19 Jun 2022 09:42:42 AM EDT
Is this correct? (y/N) y

GnuPG needs to construct a user ID to identify your key.

Real name: Foreman Labs
Email address: foreman.labs@foreman.xyz
Comment:
You selected this USER-ID:
    "Foreman Labs <foreman.labs@foreman.xyz>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? o
You need a Passphrase to protect your secret key.

We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
gpg: key 7ADB8EA4 marked as ultimately trusted
public and secret key created and signed.

gpg: checking the trustdb
gpg: 3 marginal(s) needed, 1 complete(s) needed, PGP trust model
gpg: depth: 0  valid:   1  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 1u
gpg: next trustdb check due at 2022-06-19
pub   2048R/7ADB8EA4 2021-06-19 [expires: 2022-06-19]
      Key fingerprint = 1CBA 9700 9A13 B1FF A423  18D5 5EDB B5F2 7ADB 8EA4
uid                  Foreman Labs <foreman.labs@foreman.xyz>
sub   2048R/7EDDB053 2021-06-19 [expires: 2022-06-19]
```

### Export gpg key

```
[root@foreman ~]# gpg --list-keys
/root/.gnupg/pubring.gpg
------------------------
pub   2048R/7ADB8EA4 2021-06-19 [expires: 2022-06-19]
uid                  Foreman Labs <foreman.labs@foreman.xyz>

[root@foreman ~]# gpg --export -a 'Foreman Labs' > RPM-GPG-KEY-foremanlabs
```

### Import key into RPM database

```
[root@foreman ~]# rpm --import RPM-GPG-KEY-foremanlabs
```

### Create .rpmmacros file in order to utilize key

This man page `man rpmsign` tells you pretty much all you need to know.

```
[root@foreman ~]# cat ~/.rpmmacros 
%_gpg_name Foreman Labs
```

### Sign package

```
[root@foreman ~]# rpm --addsign custom-rpm1-1.0-1.el7.x86_64.rpm 
Enter pass phrase: 
Pass phrase is good.
custom-rpm1-1.0-1.el7.x86_64.rpm:
```

# Manage systems

## Create activation keys

https://access.redhat.com/documentation/en-us/red_hat_satellite/6.6/html/content_management_guide/managing_activation_keys
```
Content -> Activation Keys -> Create Activation Key
```

## Create host group keys

https://access.redhat.com/documentation/en-us/red_hat_satellite/6.6/html/content_management_guide/managing_activation_keys
```
Content -> Activation Keys -> 'Key' -> Host Collections -> Add -> 'Select Host Collection' -> Add Selected
```

## Configure bare-metal deployments

Hosts -> Create Hosts

# Manage Ansible roles

## Import Ansible roles

https://access.redhat.com/documentation/en-us/red_hat_satellite/6.6/html/administering_red_hat_satellite/chap-red_hat_satellite-administering_red_hat_satellite-managing_ansible_roles

Put roles on Satellite server in /etc/ansible/roles, then import from console.

## Configure Ansible roles

https://access.redhat.com/documentation/en-us/red_hat_satellite/6.6/html/managing_hosts/using_ansible_roles

```
Configure -> Ansible -> Roles
```

# Manage Ansible variables

## Import Ansible variables from an Ansible role

```
To import Ansible variables, complete the following steps:

1. In the Satellite web UI, navigate to Configure > Variables.
2. In the upper right of the window, select the Capsule that contains the Ansible variables that you want to import.
3. Select the Ansible variables that you want to import, and click Update.
```

## Configure an Ansible variable

https://access.redhat.com/documentation/en-us/red_hat_satellite/6.6/html/administering_red_hat_satellite/chap-red_hat_satellite-administering_red_hat_satellite-managing_ansible_roles
```
# Create new Ansible Variable
Configure -> Ansible Variables -> New Ansible Variable
```

```
# Modify existing Ansible Variable
Configure -> Ansible Variables -> 'Choose a variable from the list.'
```

# Manage clients

## Register an existing client to a Satellite server

https://access.redhat.com/documentation/en-us/red_hat_satellite/6.6/html/content_management_guide/managing_activation_keys#Managing_Activation_Keys-Using_Activation_Keys

```console
# On the host, clear any old data to ensure updated data is uploaded correctly.
subscription-manager clean

# Install the katello-ca-consumer-latest RPM
# This RPM installs the necessary certificates for accessing repositories on Satellite Server and configures Red Hat Subscription Manager to use the server’s URL.
rpm -Uvh http://satellite.example.com/pub/katello-ca-consumer-latest.noarch.rpm

# Register the host
subscription-manager register --org="Default_Organization" \
--activationkey=ak-Reg_To_Dev_EL7
```

## Apply errata to clients

https://access.redhat.com/documentation/en-us/red_hat_satellite/6.6/html/content_management_guide/managing_errata#applying_errata_to_a_host
```
Content -> Errata -> Select Errata -> Apply Errata
```

## Install custom software on clients

These are the main steps that has to be done in order to install custom software on clients
1. Create GPG key
1. Create Product
1. Create Content View
1. Create Activation Key
1. Attach subscripton to host with Activation Key


### Create GPG key

```
Content -> Content Credentials -> Create Content Credential
```

### Create Product

```
# Create Product
Content -> Products -> Create -> Product

# Create New Repository for the Product
Newly Created Product -> Repositories -> New Repository
```

### Create Content View

```
# Create Content View
Content -> Content View -> Create New View

# Add Yum Content to the new view
Newly Created View -> Yum Content -> Repositories -> Add -> Select Repository -> Add Repositories
```

### Create Activation Key

```
Content -> Activation Keys -> Create Activation Key

# Attach Subscription to Activation Key
Newly Created Activation Key -> Subscriptions -> Add -> Select Product
```

### Subscribe Host

```
[foo@bar ~]$ sudo subscription-manager register --org="Foo" --activationkey="Bar"
```

## Use Satellite server to remotely execute scripts on clients

### On foreman we have to enable the plugins for remote-execution first.

```
foreman-installer --enable-foreman-plugin-remote-execution \
--enable-foreman-proxy-plugin-remote-execution-ssh
```

### Create Custom Job Template

1. Create Template
  * Hosts -> Job Templates -> New Job Template
1. Add Name
  * My First Template
1. Set a Job category
1. Paste in a script and save
```bash
#!/bin/bash
echo "Hello World from <%= @host.name %>!"
```

Go to Hosts -> All Hosts -> Choose a host -> Schedule Remote Job -> Select the Template -> Run Job -> Click the Host on the bottom to see details about the run.

## Assign an Ansible role to an existing client

The short answer to this is:
```
Hosts -> All Hosts -> Select Host -> Edit -> Ansible Roles -> Select Role -> Submit
```

### Install and configure Ansible

Since foreman isnt set up with Ansible by default we need to do configure that manually.

Documentation for foreman: `https://theforeman.org/plugins/foreman_ansible/2.x/index.html`

For Ansible documentation run: `ansible-doc -t callback foreman`

Install Ansible
```
sudo yum install ansible -y
```

```
cp /etc/ansible/ansible.cfg /etc/ansible/ansible.bak
```

Configure the callback in ansible.cfg and ensure the following

```ini
[defaults]
callback_whitelist = foreman
```

Add the following to the end of ansible.cfg and insert the URL for your foreman.
```ini
[callback_foreman]
url = 'https://foreman.example.com'
ssl_cert = /etc/foreman-proxy/ssl_cert.pem
ssl_key = /etc/foreman-proxy/ssl_key.pem
verify_certs = /etc/foreman-proxy/ssl_ca.pem
```

When the callback is whitelisted and the right options are configured nable foreman plugins for Ansible
```
foreman-installer --enable-foreman-plugin-ansible \
--enable-foreman-proxy-plugin-ansible
```

Add Ansible roles to the roles path
```
ansible-galaxy role install arillso.motd -p /etc/ansible/roles/
```

Add roles from the foreman console
```
Configure -> Ansible -> Roles -> Select roles -> Update
```

# Provision clients
## Provision bare metal and virtual clients using kickstarts
## Use Ansible variables in kickstarts
## Use Ansible roles in kickstarts

As with all Red Hat performance-based exams, configurations must persist after reboot without intervention.