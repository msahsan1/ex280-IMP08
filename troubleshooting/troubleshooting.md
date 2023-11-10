# Troubleshooting OpenShift Clusters and Applications

## Troubleshooting Common Issues with an OpenShift Cluster

### Verifying the Health of OpenShift Nodes
```sh
oc get nodes
```
Displays a column with the status of each node. If a node is not Ready, then it cannot
communicate with the OpenShift control plane, and is effectively dead to the cluster.
```sh
oc adm top nodes
```
Displays a column with the status of each node. If a node is not Ready, then it cannot
communicate with the OpenShift control plane, and is effectively dead to the cluster.
```sh
oc describe node my-node-name
```
Displays the resources available and used from the scheduler point of view, and other
information. Look for the headings "Capacity", "Allocatable", and "Allocated resources" in the output. The heading "Conditions" indicates whether the node is under memory pressure, disk pressure, or some other condition that would prevent the node from starting new containers.

### Reviewing the Cluster Version Resource

```sh
oc get clusterversion
```
Run `oc describe clusterversion` to obtain more detailed information about the cluster
status.
```sh
oc describe clusterversion
```

### Reviewing Cluster Operators
Run `oc get clusteroperators` or `oc get co`to retrieve the list of all cluster operators:
```sh
oc get clusteroperators
```
Each row describes a cluster operator.
- The NAME field indicates the name of the operator. This operator is responsible for managing
authentication.
- The AVAILABLE field indicates that the authentication operator deployed successfully and is
available for use in the cluster. Notice that a cluster operator might return a status of
available even if its degraded. An operator reports degraded when its current state does
not match its desired state over a period of time. For example, if the operator requires three
running pods, but one pod is crashing, the operator is available but in a degraded state.
- The PROGRESSING field indicates whether an operator is being updated to a newer version by
the top level operator. If new resources are being deployed by the cluster version
operator, then the columns read True .
- The DEGRADED field returns the health of the operator. The entry reads True if the operator
encounters an error that prevents it from working properly. The operator services might still
be available, however, all the requirements might not be satisfied. This can indicate that the
operator will fail and require user intervention.

### Displaying the Logs of OpenShift Nodes

```sh
oc adm node-logs -u crio my-node-name
oc adm node-logs -u kubelet my-node-name
```
You can also display all journal logs of a node:
```sh
oc adm node-logs my-node-name
```

### Opening a Shell Prompt on an OpenShift Node
The `oc debug node` command provides a way to open a shell prompt in any node of your cluster.
That prompt comes from a special-purpose tools container that mounts the node root file
system at the /host folder, and allows you to inspect any files from the node.
```sh
oc debug node/my-node-name
```
### Using shell session to verify kubelet service
```sh
[user@host ~]$ oc debug node/my-node-name
...output omitted...
sh-4.4# chroot /host
sh-4.4# systemctl is-active kubelet
active
```
### Using shell session to verify tcrio service
```sh
sh-4.4# systemctl status crio
● crio.service - Container Runtime Interface for OCI (CRI-O)
Loaded: loaded (/usr/lib/systemd/system/crio.service; disabled; vendor preset: dis
abled)
Drop-In: /etc/systemd/system/crio.service.d
└─10-mco-default-madv.conf, 10-mco-profile-unix-socket.conf, 20-nodenet.co
nf
Active: active (running) since Wed 2022-04-20 20:35:59 UTC; 5h 59min ago
...output omitted...
```
### Using shell session to verify that the etcd pod is running
```sh
sh-4.4# crictl ps --name etcd
CONTAINER
... STATE
NAME
5e18a8220a9d5 ... Running
etcd-operator
19bc3ed5e8643 ... Running
etcd-metrics
...output omitted...
```
A shell session started from the oc debug node command depends on the OpenShift control
plane to work. It uses the same tunneling technology that allows opening a shell prompt inside a
running pod (see the oc rsh command later in this section).
> Note: The oc debug node command is not based on the SSH or RSH protocols.

### Troubleshooting The Container Engine
```sh
[user@host ~]$ oc debug node/my-node-name
...output omitted...
sh-4.4# chroot /host
sh-4.4# crictl ps
...output omitted...
```

## Troubleshooting Application Deployments

### Troubleshooting Pods That Fail to Start
A common scenario is that OpenShift creates a pod and that pod never establishes a Running
state. Start troubleshooting using the `oc get pod` and `oc status` commands to verify whether your pods and containers are running. At some point, the pods are in an error state, such as ErrImagePull or ImagePullBackOff.

When this happens, the first step is listing events from the current project using the `oc get events` command. If your project contains many pods, then you can get a list of events filtered by pod using the `oc describe pod` command. You can also run similar `oc describe` commands to filter events by deployments and deployment configurations.

### Troubleshooting Running and Terminated Pods
```sh
[user@host ~]$ oc logs my-pod-name
```
If the pod contains multiple containers, then the oc logs command requires the -c option.
```sh
[user@host ~]$ oc logs my-pod-name -c my-container-name
```
We can use the `--tail` option.
```sh
oc logs --tail 3 -n openshift-image-registry cluster-image-registry-operator-564bd5dd8f-s46bz
```

### Creating Troubleshooting Pods
A common scenario is creating a pod from a deployment, but running as the root user and thus
proving that the deployment references a container image that was not designed to run under
the default security policies of OpenShift:
```sh
oc debug deployment/my-deployment-name --as-root
```

### Changing a Running Container
The following commands help with making changes to running containers. They all assume that pods contain a single container. If not, you must add the `-c my-container-name` option.
```sh
oc rsh my-pod-name
```
Opens a shell inside a pod to run shell commands interactively and non-interactively.

To copies local files to a location inside a pod. You can also reverse arguments and copy files from inside a pod to your local file system. See also the oc rsync command for copying multiple files at once.
```sh
oc cp /local/path my-pod-name:/container/path
```
Creates a TCP tunnel from local-port on your workstation to local-port on the pod. The tunnel is alive as long as you keep the oc port-forward running. This allows you to get network access to the pod without exposing it through a route. Because the tunnel starts at your localhost, it cannot be accessed by other machines.
```sh
oc port-forward my-pod-name local-port:remote-port
```

## Troubleshooting OpenShift CLI Commands
The --loglevel level option displays OpenShift API requests, starting with level 6. As you
increase the level, up to 10, more information about those requests is added, such as their HTTP request headers and response bodies. Level 10 also includes a curl command to replicate each request.
You can try these two commands, from any project, and compare their outputs.
```sh
oc get pods --loglevel 6
oc get pods --loglevel 10
```
Sometimes, you only need the authentication token that the oc command uses to authenticate
OpenShift API requests. With this token, an automation tool can make OpenShift API requests
as if it was logged in as your user. To get your token, use the -t option of the oc whoami
command:
```sh
oc whoami -t
```