
# Manage OpenShift Container Platform

### Use the command-line interface to manage and configure an OpenShift cluster
If the crc cluster is running you display the credentials for the admin account by running `crc console --credentials`. To login run `oc login -u developer -p developer https://api.crc.testing:6443`

### Use the web console to manage and configure an OpenShift cluster
With CodeReadyContainers you can use the `crc console` command to open the oc console in the default browser.

### Create and delete projects
```console
# Create project
[birger@localhost ~]$ oc new-project demo-project

# Delete project
[birger@localhost ~]$ oc delete project demo-project
```

### Import, export, and configure Kubernetes resources

### Examine resources and cluster status
```console
# Examine resources
[birger@localhost ~]$ oc get pods

[birger@localhost ~]$ oc explain pods

[birger@localhost ~]$ oc describe pods

# View cluster status
[birger@localhost ~]$ oc status
```

### View logs
```console
# View pod logs. See 'oc logs --help' for more options
[birger@localhost ~]$ oc logs pod/cakephp-ex-5b54f5f7-xr6pf
```

### Monitor cluster events and alerts
This can be done by logging into the oc console and navigating to the monitoring section.

### Troubleshoot common cluster events and alerts

### Use product documentation
The exam is only 3 hours long, getting familiar with the documentation beforehand might be a smart choice.

## Manage users and policies
An easy way to create local users is by using the HTPasswd tool. This is installed by running `yum install httpd-tools -y`. By default crc comes with a pre-configured htpass-secret, to configure your own users either delete this secret or extract and replace it.

### Configure the HTPasswd identity provider for authentication
```console
# Extract pre-configured users to htpasswd file
[birger@localhost ~]$ mkdir some-directory
[birger@localhost ~]$ oc extract secret/htpass-secret -n openshift-config --to=some-directory
[birger@localhost ~]$ cat some-directory/htpasswd

# Add user 'bob' with password 'princess' to htpasswd file
[birger@localhost ~]$ htpasswd -B -b htpasswd bob princess

# Since the htpass-secret already exists we can replace it by running this command.
# '--dry-run' ensures that no changes are applied by the 'oc create secret' command
# '-o yaml' outputs the contents in yaml format.
# 'oc replace -f -' Takes the yaml content as input and replaces the old secret with the new one.
[birger@localhost ~]$ oc create secret generic htpass-secret --from-file=htpasswd=htpasswd --dry-run -o yaml -n openshift-config | oc replace -f -

# Its also possible to delete the htpass-secret and apply a new one
[birger@localhost ~]$ oc delete secret htpass-secret -n openshift-config

# Create new htpass-secret
[birger@localhost ~]$ oc create secret generic htpass-secret --from-file=htpasswd=htpasswd -n openshift-config
```

### Create and delete users
```console
# Create user
[birger@localhost ~]$ oc create user bob

# Delete user
[birger@localhost ~]$ oc delete user bob
```

### Modify user passwords
```console
# If the users are configured with HTPasswd you can do the following
[birger@localhost ~]$ oc extract secret/htpass-secret -n openshift-config --to=some-directory

# Remove the user you want to change the password for
[birger@localhost ~]$ vim some-directory/htpasswd

# Add the user again with a new password
[birger@localhost ~]$ htpasswd -B -b htpasswd bob NoLongerPrincess
```

### Modify user and group permissions

### Create and manage groups
```console
# Add a group with no users
[birger@localhost ~]$ oc adm groups new my-group

# Add a group with two users
[birger@localhost ~]$ oc adm groups new my-group user1 user2
```

# Control access to resources

### Define role-based access controls
```console
# Add role to user
[birger@localhost ~]$ oc adm policy add-role-to-user edit user1

# Create new role
[birger@localhost ~]$ oc create role pod-reader --verb=get --verb=list --resource=pod -n my-app
```

### Apply permissions to users
```console
# Give user edit role
[birger@localhost ~]$ oc adm policy add-role-to-user edit user1

# Add user to namespaced role
[birger@localhost ~]$ oc adm policy add-role-to-user pod-reader user1 --role-namespace=my-app -n my-app
```

### Create and apply secrets to manage sensitive information

### Create service accounts and apply permissions using security context constraints
```console
# Create service account
[birger@localhost ~]$ oc create serviceaccount my-service-account

# Add the 'privileged' security context constraint to the service account my-service-account in the current namespace
[birger@localhost ~]$ oc adm policy add-scc-to-user privileged -z my-service-account
```

# Configure networking components

### Troubleshoot software defined networking

### Create and edit external routes
```console
# Create an edge route named "my-route" that exposes frontend service.
[birger@localhost ~]$ oc create route edge my-route --service=frontend

# Edit
[birger@localhost ~]$ oc edit route my-route
```

### Control cluster network ingress

### Create a self signed certificate
```console
# This will create a x509 certificate and a private key
# '-nodes' option ensures that the key file is not encrypted
[birger@localhost ~]$ openssl req -new -x509 -nodes -out selfsigned.crt -keyout selfsigned.key
```

### Secure routes using TLS certificates
```console
# Create a secure route using the self signed certificate
[birger@localhost ~]$ oc create route edge my-edge --service=my-service --cert=selfsigned.crt --key=selfsigned.key
```

# Configure pod scheduling

### Limit resource usage
ResourceQuota sets aggregate quota restrictions enforced per namespace
```console
# To limit quota used in a namespace
[birger@localhost ~]$ oc create quota my-quota --hard=cpu=1,memory=1G,pods=2,services=3,replicationcontrollers=2,resourcequotas=1,secrets=5,persistentvolumeclaims=10
```

### Scale applications to meet increased demand
```console
# Scale the deployment to 3 replicas manually
[birger@localhost ~]$ oc scale deployment/my-deployment --replicas=3

# Configure autoscaling
[birger@localhost ~]$ oc autoscale deployment my-app --min=2 --max=10 --cpu-percent=80
```

### Control pod placement across cluster nodes
A taint allows a node to refuse pod to be scheduled unless that pod has a matching toleration.

```console
# The node has the following taints:
[birger@localhost ~]$ oc adm taint nodes node1 key1=value1:NoSchedule
[birger@localhost ~]$ oc adm taint nodes node2 key2=value2:NoSchedule
```

The pod has the following tolerations:
```yaml
tolerations:
- key: "key1"
  operator: "Equal"
  value: "value1"
  effect: "NoSchedule"
- key: "key1"
  operator: "Equal"
  value: "value1"
  effect: "NoExecute"
```

#### Example
```console
# Configure taints on your node
[birger@localhost ~]$ oc adm taint node crc-ctj2r-master-0 app=postgres:NoSchedule 

# Create a new test project
[birger@localhost ~]$ oc new-project test-project

# Create a new application
[birger@localhost ~]$ oc new-app rails-postgresql-example

# Verify that pods are stuck in pending state
[birger@localhost ~]$ oc get pods

# Edit deploymentconfig and insert tolerations as shown below
[birger@localhost ~]$ oc edit deploymentconfig postgresql
```
```yaml
# Example output from edit command
apiVersion: apps.openshift.io/v1
kind: DeploymentConfig
metadata:
  annotations:
    description: Defines how to deploy the database
    openshift.io/generated-by: OpenShiftNewApp
    template.alpha.openshift.io/wait-for-ready: "true"
  creationTimestamp: "2021-03-15T17:52:36Z"
  generation: 3
  labels:
    app: rails-postgresql-example
    template: rails-postgresql-example
  name: postgresql
  namespace: testing-2
  resourceVersion: "49392"
  selfLink: /apis/apps.openshift.io/v1/namespaces/testing-2/deploymentconfigs/postgresql
  uid: 2966689b-5938-4742-accf-3c28824a56ad
spec:
  replicas: 1
  revisionHistoryLimit: 10
  selector:
    name: postgresql
  strategy:
    activeDeadlineSeconds: 21600
    recreateParams:
      timeoutSeconds: 600
    resources: {}
    type: Recreate
  template:
    metadata:
      annotations:
        openshift.io/generated-by: OpenShiftNewApp
      creationTimestamp: null
      labels:
        name: postgresql
      name: postgresql
    spec:
      tolerations:
      - key: app
        value: postgres
        effect: NoSchedule
      containers:
      - env:
        - name: POSTGRESQL_USER
          valueFrom:
            secretKeyRef:
              key: database-user
              name: rails-postgresql-example
        - name: POSTGRESQL_PASSWORD
          valueFrom:
            secretKeyRef:
              key: database-password
              name: rails-postgresql-example
....
```

```console
# Verify that pods are now getting created
[birger@localhost ~]$ oc get pods
```

# Configure cluster scaling
```
https://docs.openshift.com/container-platform/4.5/machine_management/
```

### Manually control the number of cluster workers
```console
# Edit the machine set:
[birger@localhost ~]$ oc edit machineset <machineset> -n openshift-machine-api

# Scale down the machine set to 0:
[birger@localhost ~]$ oc scale --replicas=0 machineset <machineset> -n openshift-machine-api

# Scale up the machine set as needed:
[birger@localhost ~]$ oc scale --replicas=2 machineset <machineset> -n openshift-machine-api
```

### Automatically scale the number of cluster workers
https://docs.openshift.com/container-platform/4.5/machine_management/applying-autoscaling.html

```console
# Create a YAML file for the ClusterAutoscaler resource that contains the customized resource definition.
vim autoscaler.yaml

# Create the resource in the cluster
[birger@localhost ~]$ oc create -f autoscaler.yaml
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
