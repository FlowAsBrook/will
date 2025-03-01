---
title: Kubernetes RBAC
date: 2025-03-01 19:48:15
tags: RBAC
categories: Kubernetes
---

> RBAC (Role-Based Access Control) in Kubernetes is a security model that controls who can access and perform actions on resources (like Pods, Deployments, Services, etc.) within a cluster.

# Component

Kubernetes RBAC consists of four main components:

| Component                                 | Description                                                  |
| ----------------------------------------- | ------------------------------------------------------------ |
| Role / ClusterRole                        | Defines what actions (verbs) can be performed on which resources. |
| RoleBinding / ClusterRoleBinding          | Grants a Role or ClusterRole to a User, Group, or ServiceAccount. |
| Subjects (Users, Groups, ServiceAccounts) | Who is allowed to perform actions (User, Group, or ServiceAccount). |
| Resources & API Groups                    | The objects that can be controlled (e.g., pods, deployments, services, etc.). |

## Role & ClusterRole

> RBAC defines what actions can be performed on which resources using Roles and ClusterRoles.

### Role

> A Role is used for namespace-scoped access control.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-reader
  namespace: my-namespace
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "watch"]
```

✅ This Role allows a user to view (get, list, watch) Pods only in the namespace my-namespace.

❌ This does not grant access to other namespaces.

### ClusterRole

>  A ClusterRole is used for cluster-wide permissions or permissions across all namespaces.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: cluster-pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "watch"]
```

✅ This allows viewing (get, list, watch) Pods in all namespaces.

## RoleBinding & ClusterRoleBinding

> Who Gets These Permissions?

### RoleBinding

> A RoleBinding assigns a Role to a specific user, group, or ServiceAccount in a namespace.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: pod-reader-binding
  namespace: my-namespace
subjects:
- kind: User
  name: my-user
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

✅ This binds the pod-reader Role to my-user in the my-namespace namespace.

❌ It does not grant permissions outside my-namespace.

### ClusterRoleBinding

> A ClusterRoleBinding assigns a ClusterRole to a User, Group, or ServiceAccount across all namespaces.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-pod-reader-binding
subjects:
- kind: User
  name: my-user
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: cluster-pod-reader
  apiGroup: rbac.authorization.k8s.io
```

✅ This grants my-user access to Pods across all namespaces.

## Subjects

> A RoleBinding or ClusterRoleBinding grants permissions to a subject, which can be:

| Subject Type   | Description                                              |
| -------------- | -------------------------------------------------------- |
| User           | A real human user (external identity).                   |
| Group          | A group of users (e.g., dev-team).                       |
| ServiceAccount | An internal Kubernetes identity for a Pod or Controller. |

Example: Binding a ServiceAccount (my-sa) to a Role

```yaml
subjects:
- kind: ServiceAccount
  name: my-sa
  namespace: my-namespace
```

## API Groups & Resources

Each RBAC rule specifies which resources in which API groups can be accessed.

| API Group                 | Resources Example                                      |
| ------------------------- | ------------------------------------------------------ |
| "" (core)                 | pods, services, nodes                                  |
| apps                      | deployments, daemonsets                                |
| batch                     | jobs, cronjobs                                         |
| rbac.authorization.k8s.io | roles, rolebindings, clusterroles, clusterrolebindings |

Example: Allow Access to Pods, Deployments, and Nodes

```yaml
rules:
- apiGroups: [""]
  resources: ["pods", "services"]
  verbs: ["get", "list", "watch"]
- apiGroups: ["apps"]
  resources: ["deployments"]
  verbs: ["get", "list", "watch"]
- apiGroups: [""]
  resources: ["nodes"]
  verbs: ["get"]
```

✅ Allows reading Pods and Services (core API group).

✅ Allows reading Deployments (apps API group).

✅ Allows reading Nodes (core API group).

# Works Scenario

## Scenario 1

Use Case:

- A user (dev-user) needs read-only access to Pods in the dev namespace.

- The ServiceAccount (cicd-runner-sa) needs to create Deployments in the cicd namespace.

Solution: Define the RBAC Rules

1. Create a Role for Read-Only Pod Access in dev Namespace

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-reader
  namespace: dev
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "watch"]
```

2. Bind the Role to dev-user in dev Namespace

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: pod-reader-binding
  namespace: dev
subjects:
- kind: User
  name: dev-user
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

3. Create a Role for cicd-runner-sa to Deploy Apps in cicd Namespace

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: deployment-creator
  namespace: cicd
rules:
- apiGroups: ["apps"]
  resources: ["deployments"]
  verbs: ["create", "update", "delete"]
```

4. Bind the Role to the cicd-runner-sa ServiceAccount

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: cicd-runner-binding
  namespace: cicd
subjects:
- kind: ServiceAccount
  name: cicd-runner-sa
  namespace: cicd
roleRef:
  kind: Role
  name: deployment-creator
  apiGroup: rbac.authorization.k8s.io
```

## Scenario 2

Scenario: Grant Read-Only Access to Pods Across Multiple Namespaces

Use Case:

- A developer team needs read-only access to Pods in multiple namespaces (dev, staging, prod).

- Instead of creating separate Roles in each namespace, we use one ClusterRole.

- We then create RoleBindings in each namespace to assign the ClusterRole.

1. Create a ClusterRole to Read Pods Across Namespaces

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: multi-namespace-pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "watch"]
```

2. Bind the ClusterRole to a User for Multiple Namespaces

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: multi-namespace-pod-reader-binding
subjects:
- kind: User
  name: developer-user
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: multi-namespace-pod-reader
  apiGroup: rbac.authorization.k8s.io
```



# Recap

Key Takeaways from Kubernetes RBAC Architecture

1. Role / ClusterRole → Defines permissions (what actions are allowed?).

2. RoleBinding / ClusterRoleBinding → Assigns permissions (who gets access?).

3. Subjects → Users, Groups, ServiceAccounts (who is authorized?).

4. API Groups → Determines resources that can be controlled.