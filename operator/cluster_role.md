It seems there was a misunderstanding in the creation of the `ClusterRole` and `ClusterRoleBinding`. The resource `namespaces` is indeed not namespace-scoped, so it should be handled at the cluster level using `ClusterRole` and `ClusterRoleBinding`.

Let's ensure that the steps are correctly laid out and provide the necessary RBAC permissions for the service account:

### Create ClusterRole

First, create a ClusterRole that grants permissions to create namespaces at the cluster scope.

Create a file named `clusterrole.yaml` with the following content:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: outsystems-operator-controller-manager-clusterrole
rules:
- apiGroups: [""]
  resources: ["namespaces"]
  verbs: ["create", "get", "list", "watch", "update", "patch", "delete"]
```

### Create ClusterRoleBinding

Next, create a ClusterRoleBinding to bind the ClusterRole to the service account.

Create a file named `clusterrolebinding.yaml` with the following content:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: outsystems-operator-controller-manager-clusterrolebinding
subjects:
- kind: ServiceAccount
  name: outsystems-operator-controller-manager
  namespace: selfhosted-operator-dev
roleRef:
  kind: ClusterRole
  name: outsystems-operator-controller-manager-clusterrole
  apiGroup: rbac.authorization.k8s.io
```

### Apply the Configuration

Apply these configurations to your cluster:

```sh
kubectl apply -f clusterrole.yaml
kubectl apply -f clusterrolebinding.yaml
```

### Verify Permissions

After applying the RBAC configurations, verify that the service account now has the necessary permissions:

```sh
kubectl auth can-i create namespaces --as=system:serviceaccount:selfhosted-operator-dev:outsystems-operator-controller-manager
```

This command should return "yes". If it returns "no", there may be an issue with the RBAC resources applied.

### Summary

Here are the complete YAML files for reference:

#### clusterrole.yaml

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: outsystems-operator-controller-manager-clusterrole
rules:
- apiGroups: [""]
  resources: ["namespaces"]
  verbs: ["create", "get", "list", "watch", "update", "patch", "delete"]
```

#### clusterrolebinding.yaml

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: outsystems-operator-controller-manager-clusterrolebinding
subjects:
- kind: ServiceAccount
  name: outsystems-operator-controller-manager
  namespace: selfhosted-operator-dev
roleRef:
  kind: ClusterRole
  name: outsystems-operator-controller-manager-clusterrole
  apiGroup: rbac.authorization.k8s.io
```

By correctly setting up the ClusterRole and ClusterRoleBinding, you should be able to resolve the permission issue and allow the operator to create namespaces at the cluster scope.
