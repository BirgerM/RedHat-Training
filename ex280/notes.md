
# Manage OpenShift Container Platform

### Use the command-line interface to manage and configure an OpenShift cluster
If the crc cluster is running you display the credentials for the admin account by running `crc console --credentials`. To login run `oc login -u developer -p developer https://api.crc.testing:6443`

### Use the web console to manage and configure an OpenShift cluster
With CodeReadyContainers you can use the `crc console` command to open the oc console in the default browser.

### Create and delete projects
```
# Create project
oc new-project demo-project

# Delete project
oc delete project demo-project
```

### Import, export, and configure Kubernetes resources

### Examine resources and cluster status
```
# Examine resources
oc get pods

oc explain pods

oc describe pods

# View cluster status
oc status
```

### View logs
```
# View pod logs. See 'oc logs --help' for more options
oc logs pod/cakephp-ex-5b54f5f7-xr6pf
```

### Monitor cluster events and alerts
This can be done by logging into the oc console and navigating to the monitoring section.

### Troubleshoot common cluster events and alerts

### Use product documentation
The exam is only 3 hours long, getting familiar with the documentation beforehand might be a smart choice.

## Manage users and policies
An easy way to create local users is by using the HTPasswd tool. This is installed by running `yum install httpd-tools -y`. By default crc comes with a pre-configured htpass-secret, to configure your own users either delete this secret or extract and replace it.

### Configure the HTPasswd identity provider for authentication
```
# Extract pre-configured users to htpasswd file
mkdir some-directory
oc extract secret/htpass-secret -n openshift-config --to=some-directory
cat some-directory/htpasswd

# Add user 'bob' with password 'princess' to htpasswd file
htpasswd -B -b htpasswd bob princess

# Since the htpass-secret already exists we can replace it by running this command.
# '--dry-run' ensures that no changes are applied by the 'oc create secret' command
# '-o yaml' outputs the contents in yaml format.
# 'oc replace -f -' Takes the yaml content as input and replaces the old secret with the new one.
oc create secret generic htpass-secret --from-file=htpasswd=htpasswd --dry-run -o yaml -n openshift-config | oc replace -f -

# Its also possible to delete the htpass-secret and apply a new one
oc delete secret htpass-secret -n openshift-config

# Create new htpass-secret
oc create secret generic htpass-secret --from-file=htpasswd=htpasswd -n openshift-config
```

### Create and delete users
```
# Create user
oc create user bob

# Delete user
oc delete user bob
```

### Modify user passwords
```
# If the users are configured with HTPasswd you can do the following
oc extract secret/htpass-secret -n openshift-config --to=some-directory

# Remove the user you want to change the password for
vim some-directory/htpasswd

# Add the user again with a new password
htpasswd -B -b htpasswd bob NoLongerPrincess
```

### Modify user and group permissions

### Create and manage groups

# Control access to resources

### Define role-based access controls

### Apply permissions to users

### Create and apply secrets to manage sensitive information

### Create service accounts and apply permissions using security context constraints

# Configure networking components

### Troubleshoot software defined networking

### Create and edit external routes

### Control cluster network ingress

### Create a self signed certificate

### Secure routes using TLS certificates

# Configure pod scheduling

### Limit resource usage

### Scale applications to meet increased demand

### Control pod placement across cluster nodes

# Configure cluster scaling

### Manually control the number of cluster workers

### Automatically scale the number of cluster workers