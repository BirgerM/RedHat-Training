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
*Create gpg key: gpg --gen-key
*Export key: gpg --export -a "Foreman Buff" > RPM-GPG-KEY-foremanbuff
*Import key into RPM database: rpm --import RPM-GPG-KEY-foremanbuff
*Create .rpmmacros file in order to utilize key
*Sign package

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
## Use Satellite server to remotely execute scripts on clients
## Assign an Ansible role to an existing client

# Provision clients
## Provision bare metal and virtual clients using kickstarts
## Use Ansible variables in kickstarts
## Use Ansible roles in kickstarts

As with all Red Hat performance-based exams, configurations must persist after reboot without intervention.