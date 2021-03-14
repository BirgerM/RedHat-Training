
# Manage OpenShift Container Platform

### Use the command-line interface to manage and configure an OpenShift cluster
If the crc cluster is running you display the credentials for the admin account by running `crc console --credentials`. To login run `oc login -u developer -p developer https://api.crc.testing:6443`

### Use the web console to manage and configure an OpenShift cluster
With CodeReadyContainers you can use the `crc console` command to open the oc console in the default browser.

### Create and delete projects
```bash
# Create project
oc new-project demo-project

# Delete project
oc delete project demo-project
```

### Import, export, and configure Kubernetes resources

### Examine resources and cluster status
```bash
# Examine resources
oc get pods

oc explain pods

oc describe pods

# View cluster status
oc status
```

### View logs
```bash
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
```bash
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
```bash
# Create user
oc create user bob

# Delete user
oc delete user bob
```

### Modify user passwords
```bash
# If the users are configured with HTPasswd you can do the following
oc extract secret/htpass-secret -n openshift-config --to=some-directory

# Remove the user you want to change the password for
vim some-directory/htpasswd

# Add the user again with a new password
htpasswd -B -b htpasswd bob NoLongerPrincess
```

### Modify user and group permissions

### Create and manage groups
```bash
# Add a group with no users
oc adm groups new my-group

# Add a group with two users
oc adm groups new my-group user1 user2
```

# Control access to resources

### Define role-based access controls
```bash
# Add role to user
oc adm policy add-role-to-user edit user1

# Create new role
oc create role pod-reader --verb=get --verb=list --resource=pod -n my-app
```

### Apply permissions to users
```bash
# Give user edit role
oc adm policy add-role-to-user edit user1

# Add user to namespaced role
oc adm policy add-role-to-user pod-reader user1 --role-namespace=my-app -n my-app
```

### Create and apply secrets to manage sensitive information

### Create service accounts and apply permissions using security context constraints
```bash
# Create service account
oc create serviceaccount my-service-account

# Add the 'privileged' security context constraint to the service account my-service-account in the current namespace
oc adm policy add-scc-to-user privileged -z my-service-account
```

# Configure networking components

### Troubleshoot software defined networking

### Create and edit external routes
```bash
# Create an edge route named "my-route" that exposes frontend service.
oc create route edge my-route --service=frontend

# Edit
oc edit route my-route
```

### Control cluster network ingress

### Create a self signed certificate
```bash
# This will create a x509 certificate and a private key
# '-nodes' option ensures that the key file is not encrypted
openssl req -new -x509 -nodes -out selfsigned.crt -keyout selfsigned.key
```

### Secure routes using TLS certificates
```bash
# Create a secure route using the self signed certificate
oc create route edge my-edge --service=my-service --cert=selfsigned.crt --key=selfsigned.key
```

# Configure pod scheduling

### Limit resource usage
ResourceQuota sets aggregate quota restrictions enforced per namespace
```bash
# To limit quota used in a namespace
oc create quota my-quota --hard=cpu=1,memory=1G,pods=2,services=3,replicationcontrollers=2,resourcequotas=1,secrets=5,persistentvolumeclaims=10
```

### Scale applications to meet increased demand
```bash
# Scale the deployment to 3 replicas manually
oc scale deployment/my-deployment --replicas=3

# Configure autoscaling
oc autoscale deployment my-app --min=2 --max=10 --cpu-percent=80
```

### Control pod placement across cluster nodes

# Configure cluster scaling
```
https://docs.openshift.com/container-platform/4.5/machine_management/
```

### Manually control the number of cluster workers
```bash
# Edit the machine set:
oc edit machineset <machineset> -n openshift-machine-api

# Scale down the machine set to 0:
oc scale --replicas=0 machineset <machineset> -n openshift-machine-api

# Scale up the machine set as needed:
oc scale --replicas=2 machineset <machineset> -n openshift-machine-api
```

### Automatically scale the number of cluster workers
https://docs.openshift.com/container-platform/4.5/machine_management/applying-autoscaling.html

```bash
# Create a YAML file for the ClusterAutoscaler resource that contains the customized resource definition.
vim autoscaler.yaml

# Create the resource in the cluster
oc create -f autoscaler.yaml
```
```yaml
apiVersion: "autoscaling.openshift.io/v1"
kind: "ClusterAutoscaler"
metadata:
  name: "default"
spec:
  podPriorityThreshold: -10 
  resourceLimits:
    maxNodesTotal: 24 
    cores:
      min: 8 
      max: 128 
    memory:
      min: 4 
      max: 256 
    gpus:
      - type: nvidia.com/gpu 
        min: 0 
        max: 16 
      - type: amd.com/gpu 
        min: 0 
        max: 4 
  scaleDown: 
    enabled: true 
    delayAfterAdd: 10m 
    delayAfterDelete: 5m 
    delayAfterFailure: 30s 
    unneededTime: 60s
```
