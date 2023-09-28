# Openshift-Custom-Alerting_Rules
# Policy Collection

A collection of Openshift custom Prometheus alerting_rules policy Management.

## Repository structure

This repository hosts custom Prometheus alerting_rules policies for Openshift/Kubernetes. You can deploy these policies

## Using GitOps to deploy policies to a cluster

Fork this repository and use the forked version as the target to run the sync against. This is to
avoid unintended changes to be applied to your cluster automatically. To get latest policies from
the `policy-collection` repository, you can pull the latest changes from `policy-collection` to your
own repository through a pull request. Any further changes to your repository are automatically be
applied to your cluster.

Make sure you have oc or kubectl  installed and you are logged into your cluster terminal.

Run `kubectl create -f <policy-name.yaml>` to create a "policies" inside openshift-monitoring namespace.

**Note**: By default Openshift is integrated with more than 200+ alerting_rules policies.

### Subscription Administrator

In new versions of Open Cluster Management you must be a subscription administrator in order to
deploy policies using a subscription. In these cases the subscription is still successfully created,
but policy resources are not distributed as expected. You can view the status of the subscription to
see the subscription errors. If the subscription administrator role is required, a message similar
to the following one appears for any resource that is not created:

```
        demo-stable-policies-chan-Policy-policy-cert-ocp4:
          lastUpdateTime: "2021-10-15T20:37:59Z"
          phase: Failed
          reason: 'not deployed by a subscription admin. the resource apiVersion: policy.open-cluster-management.io/v1 kind: Policy is not deployed'
```

To become a subscription administrator, you must add an entry for your user to the
`ClusterRoleBinding` named `open-cluster-management:subscription-admin`. A new entry may look like
the following:

```
subjects:
  - kind: User
    apiGroup: rbac.authorization.k8s.io
    name: my-username
```

After updating the `ClusterRoleBinding`, you need to delete the subscription and deploy the subscription again.

### Policy Generator

GitOps through Open Cluster Management is able to handle Kustomize files, so you can also use the
[Policy Generator](https://github.com/stolostron/policy-generator-plugin) Kustomize plugin to
generate policies from Kubernetes manifests in your repository. The Policy Generator handles
Kubernetes manifests as well as policy engine manifests from policy engines like
[Gatekeeper](https://open-policy-agent.github.io/gatekeeper/) and [Kyverno](https://kyverno.io/).

For additional information about the Policy Generator:

- [Policy Generator product documentation](https://access.redhat.com/documentation/en-us/red_hat_advanced_cluster_management_for_kubernetes/2.6/html/governance/governance#policy-generator)
- [Policy Generator source repository documentation](https://github.com/stolostron/policy-generator-plugin/blob/main/README.md)
- [Policy Generator reference YAML](https://github.com/stolostron/policy-generator-plugin/blob/main/docs/policygenerator-reference.yaml)
- [Policy Generator examples](policygenerator)

## Community, discussion, contribution, and support

Check the [Contributing policies](CONTRIBUTING.md) document for guidelines on how to contribute to
the repository.

**Blogs**: Read our blogs that are in the [blogs folder](blogs/README.md).

**Resources**: View the following resources for more information on the components and mechanisms
are implemented in the product governance framework.

- [Open Cluster Management Quick Start](https://https://open-cluster-management.io/getting-started/quick-start/)
