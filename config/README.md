
# Structure of a hierarchical root repository

The directory demonstrates how to use a Config Sync hierarchical root repository to
config a Kubernetes cluster shared by two different teams, `team-1` and
`team-2`. 
- Each team has its own Kubernetes namespace, Kubernetes service account, resoure
quotas, network policies, rolebindings.
- The cluster admin sets up a policy in `namespaces/limit-range.yaml` to constrain resource allocations (to Pods or Containers) in both namespaces.
- The cluster admin also sets up ClusterRoles and ClusterRoleBinidngs.

A valid Config Sync hierarchical root repository must include
three subdirectories: `cluster/`, `namespaces/`, and `system/`.

The `cluster/` directory contains configs that apply to entire clusters (such as ClusterRole, ClusterRoleBinding), rather than to namespaces.

The `namespaces/` directory contains configs for the namespace objects and the
namespace-scoped objects. 
Each subdirectory under
`namespaces/` include the configs for a namespace object and all the
namespace-scoped objects under the namespace. 
The name of a subdirectory  should
be the same as the name of the namespace object. 
Namespace-scoped objects which
need to be created in each namespace, can be put directly under `namespaces/`
(e.g., `namespaces/limit-range.yaml`).

The `system/` directory contains configs for the Config Sync Operator.

The `compiled/` directory (which is not required for using Config Sync) contains the output of `nomos hydrate`, which compiles
the configs under the `cluster/`, `namespaces/`, `system/` directories to the exact form that would be sent to the APIServer to apply.
The cluster-scoped resources are under the `compiled/` directory directly. Each
subdirectory under the `compiled/` directory includes all the configs for the
resources under a namespace. 

```
.
├── cluster
│   ├── clusterrolebinding-namespace-reader.yaml
│   ├── clusterrole-namespace-reader.yaml
│   ├── clusterrole-secret-admin.yaml
│   └── clusterrole-secret-reader.yaml
├── compiled
│   ├── clusterrolebinding_namespace-reader.yaml
│   ├── clusterrole_namespace-reader.yaml
│   ├── clusterrole_secret-admin.yaml
│   ├── clusterrole_secret-reader.yaml
│   ├── namespace_team-1.yaml
│   ├── namespace_team-2.yaml
│   ├── team-1
│   │   ├── limitrange_limits.yaml
│   │   ├── networkpolicy_default-deny-egress.yaml
│   │   ├── resourcequota_pvc.yaml
│   │   ├── rolebinding_secret-reader.yaml
│   │   └── serviceaccount_sa.yaml
│   └── team-2
│       ├── limitrange_limits.yaml
│       ├── networkpolicy_default-deny-all.yaml
│       ├── resourcequota_pvc.yaml
│       ├── rolebinding_secret-admin.yaml
│       └── serviceaccount_sa.yaml
├── namespaces
│   ├── limit-range.yaml
│   ├── team-1
│   │   ├── namespace.yaml
│   │   ├── network-policy-default-deny-egress.yaml
│   │   ├── resource-quota-pvc.yaml
│   │   ├── rolebinding-secret-reader.yaml
│   │   └── sa.yaml
│   └── team-2
│       ├── namespace.yaml
│       ├── network-policy-default-deny-all.yaml
│       ├── resource-quota-pvc.yaml
│       ├── rolebinding-secret-admin.yaml
│       └── sa.yaml
├── README.md
└── system
    └── repo.yaml
```
