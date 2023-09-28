# Openshift-Custom-Alerting_Rules

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

### Create Custom Openshift Prometheus Alerting_Rules

Create `HostHighCpuLoad` policy for `CPU load is > 50%` for `warning` and `CPU load is > 80%` for `critical alerts`. 'oc apply -f HostHighCpuLoad.yaml':

```
apiVersion: monitoring.coreos.com/v1
kind: PrometheusRule
metadata:
  name: cpu-usage
  namespace: openshift-monitoring
spec:
  groups:
  - name: CPUAlerts
    rules:
    - alert: HostHighCpuLoad
      expr: (sum by (instance) (avg by (mode, instance) (rate(node_cpu_seconds_total{mode!="idle"}[2m]))) > 0.5) * on(instance) group_left (nodename) node_uname_info{nodename=~".+"}
      for: 2m
      labels:
        severity: warning
      annotations:
        summary: Host high CPU load (instance {{ $labels.instance }})
        description: "CPU load is > 50%\n  VALUE = {{ $value }}\n  LABELS = {{ $labels }}"
    - alert: HostHighCpuLoad
      expr: (sum by (instance) (avg by (mode, instance) (rate(node_cpu_seconds_total{mode!="idle"}[2m]))) > 0.8) * on(instance) group_left (nodename) node_uname_info{nodename=~".+"}
      for: 2m
      labels:
        severity: critical
      annotations:
        summary: Host high CPU load (instance {{ $labels.instance }})
        description: "CPU load is > 80%\n  VALUE = {{ $value }}\n  LABELS = {{ $labels }}"
```

To become a subscription administrator, you must add an entry for your user to the
`ClusterRoleBinding` named `open-cluster-management:subscription-admin`. A new entry may look like
the following:


