# OpenShift API Comparison

## Purpose
This playbook will provide a list of GVKs on a source OpenShift cluster that are unavailable on a destination OpenShift cluster.

This is intended as a starting point for locating resources that cannot be migrated when performing an MTC migration.

## Requirements
- An OpenShift 3.11 source cluster.
- An OpenShift 4 destination cluster.
- Ensure you have ansible, python-kubernetes, and python-openshift and the ansible community kubernetes collection installed.
  - Fedora users can `sudo dnf -y install ansible python3-kubernetes python3-openshift ansible-collection-community-kubernetes`

N.B. Only OpenShift 3.11 is supported as an OpenShift 3 source cluster for MTC and this playbook makes some assumptions about available GVKs when pruning resources with multiple versions from the final list.

## Usage
- `export SRC_KUBECONFIG=/path/to/source/cluster/kubeconfig`
- `export DST_KUBECONFIG=/path/to/destination/cluster/kubeconfig`
- `ansible-playbook openshift-gvk-comparison.yml`

## Output
The last three tasks will print out lists of the source cluster GVKS, destination cluster GVKs, and unhandled GVKs that exist on the source cluster and are not present on the destination cluster.

```
TASK [Unhandled GVKs] ***************************************************************************************************************************************************
ok: [localhost] => {
    "unhandled_gvks": [
        "securitycontextconstraints.v1",
        "ingresses.extensions/v1beta1",
        "podsecuritypolicies.extensions/v1beta1",
        "replicationcontrollers.extensions/v1beta1",
        "horizontalpodautoscalers.autoscaling/v2beta1",
        "certificatesigningrequests.certificates.k8s.io/v1beta1",
        "poddisruptionbudgets.policy/v1beta1",
        "podsecuritypolicies.policy/v1beta1",
        "volumeattachments.storage.k8s.io/v1beta1",
        "mutatingwebhookconfigurations.admissionregistration.k8s.io/v1beta1",
        "validatingwebhookconfigurations.admissionregistration.k8s.io/v1beta1",
        "customresourcedefinitions.apiextensions.k8s.io/v1beta1",
        "priorityclasses.scheduling.k8s.io/v1beta1",
        "clusternetworks.network.openshift.io/v1",
        "egressnetworkpolicies.network.openshift.io/v1",
        "hostsubnets.network.openshift.io/v1",
        "netnamespaces.network.openshift.io/v1",
        "bundles.automationbroker.io/v1alpha1",
        "bundleinstances.automationbroker.io/v1alpha1",
        "bundlebindings.automationbroker.io/v1alpha1",
        "clusterservicebrokers.servicecatalog.k8s.io/v1beta1",
        "clusterserviceclasses.servicecatalog.k8s.io/v1beta1",
        "clusterserviceplans.servicecatalog.k8s.io/v1beta1",
        "servicebindings.servicecatalog.k8s.io/v1beta1",
        "servicebrokers.servicecatalog.k8s.io/v1beta1",
        "serviceclasses.servicecatalog.k8s.io/v1beta1",
        "serviceinstances.servicecatalog.k8s.io/v1beta1",
        "serviceplans.servicecatalog.k8s.io/v1beta1"
    ]
}
```

## Additional considerations
Note that some of these resources are intentionally excluded by MTC during migrations. For instance in the example these can be found in the default excluded resources list. https://github.com/migtools/mig-operator/blob/master/roles/migrationcontroller/defaults/main.yml
```
        "servicebindings.servicecatalog.k8s.io/v1beta1",
        "servicebrokers.servicecatalog.k8s.io/v1beta1",
        "serviceclasses.servicecatalog.k8s.io/v1beta1",
        "serviceinstances.servicecatalog.k8s.io/v1beta1",
        "serviceplans.servicecatalog.k8s.io/v1beta1"
```

Further other resources may be cluster scoped and not directly migratable. Some examples in the list above include:
```
        "clusterservicebrokers.servicecatalog.k8s.io/v1beta1",
        "clusterserviceclasses.servicecatalog.k8s.io/v1beta1",
        "clusterserviceplans.servicecatalog.k8s.io/v1beta1",
```

## Kubernetes Deprecated API Migration Guide
The Kubernetes Depricated API Migration Guide](https://kubernetes.io/docs/reference/using-api/deprecation-guide/) can be reviewed to find any additional APIs that might be removed prior to your migration.
