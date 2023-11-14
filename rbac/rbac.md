# Defining and Applying Permissions using RBAC

## RBAC Scope
Red Hat OpenShift Container Platform (RHOCP) defines two groups of roles and bindings depending on the user's scope and responsibility: cluster roles and local roles.

| Role Level | Description |
| - | - |
| Cluster Role | Users or groups with this role level can manage the OpenShift cluster. |
| Local Role | Users or groups with this role level can only manage elements at a project level. |

> NOTE: Cluster role bindings take precedence over local role bindings.

## Managing RBAC Using the CLI
To add a cluster role to a user, use the add-cluster-role-to-user subcommand:
```sh
oc adm policy add-cluster-role-to-user cluster-role username
```
To remove a cluster role from a user, use the remove-cluster-role-from-user subcommand:
```sh
oc adm policy remove-cluster-role-from-user cluster-role username
```
You can use the oc adm policy who-can command to determine if a user can execute an action
on a resource. For example:
```sh
oc adm policy who-can delete user
```

## Default Roles
### Cluster role

| Default roles | Description |
| -- | -- |
| cluster-admin | Users with this role have superuser access to the cluster resources. These users can perform any action on the cluster, and have full control of all projects. |
| cluster-status | Users with this role can get cluster status information. |
| self-provisioner | Users with this role can create new projects. This is a cluster role, not a project role. |

### Project role
| Default roles | Description |
| -- | -- |
| admin | Users with this role can manage all project resources, including granting access to other users to access the project. |
| basic-user | Users with this role have read access to the project. |
| edit | Users with this role can create, change, and delete common application resources from the project, such as services and deployments. These users cannot act on management resources such as limit ranges and quotas, and cannot manage access permissions to the project. |
| view | Users with this role can view project resources, but cannot modify project resources. |

The admin role gives a user access to project resources such as quotas and limit ranges, and also the ability to create new applications. The edit role gives a ser sufficient access to act as a developer inside the project, but working under the constraints configured by a project administrator.

Project administrators can use the oc policy command to add and remove namespace roles.

To add a specified role to a user with the add-role-to-user subcommand. For example:
```sh
oc policy add-role-to-user role-name username -n project
```

## User Types

### Regular users
Most interactive OpenShift Container Platform users are regular users, represented with the User object. This type of user represents a person that has been allowed access to the platform.
Examples of regular users include `user1` and `user2`.


### System users
Many system users are created automatically when the infrastructure is defined, mainly for the purpose of enabling the infrastructure to securely interact with the API. System users include a cluster administrator (with access to everything), a per-node user, users for routers and registries, and various others. An anonymous system user is used by default for unauthenticated requests.
Examples of system users include: `system:admin`, `system:openshift-registry`, and `system:node:node1.example.com`.

### Service accounts
These are special system users associated with projects. Some service account users are created automatically when the project is first created. Project administrators can create more for the purpose of defining access to the contents of each project. Service accounts are often used to give extra privileges to pods or deployments. Service accounts are represented with the `ServiceAccount` object.
Examples of service account users include `system:serviceaccount:default:deployer` and `system:serviceaccount:foo:builder`.
