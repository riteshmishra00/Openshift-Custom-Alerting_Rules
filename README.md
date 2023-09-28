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

Create `HostHighCpuLoad` policy for `CPU load is > 50%` for `warning` and `CPU load is > 80%` for `critical alerts`:
`oc apply -f HostHighCpuLoad.yaml`

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

Create `HostOutOfDiskSpace` policy for `Disk is almost full (< 50% left)` for `warning` and `Disk is almost full (< 75% left)` for `critical alerts`:
`oc apply -f HostOutOfDiskSpace.yaml`

```
apiVersion: monitoring.coreos.com/v1
kind: PrometheusRule
metadata:
  name: host-disk-usage
  namespace: openshift-monitoring
spec:
  groups:
  - name: HostDiskAlerts
    rules:
    - alert: HostOutOfDiskSpace
      expr: ((node_filesystem_avail_bytes * 100) / node_filesystem_size_bytes < 50 and ON (instance, device, mountpoint) node_filesystem_readonly == 0) * on(instance) group_left (nodename) node_uname_info{nodename=~".+"}
      for: 2m
      labels:
        severity: warning
      annotations:
        summary: Host out of disk space (instance {{ $labels.instance }})
        description: "Disk is almost full (< 50% left)\n  VALUE = {{ $value }}\n  LABELS = {{ $labels }}"
    - alert: HostOutOfDiskSpace
      expr: ((node_filesystem_avail_bytes * 100) / node_filesystem_size_bytes < 75 and ON (instance, device, mountpoint) node_filesystem_readonly == 0) * on(instance) group_left (nodename) node_uname_info{nodename=~".+"}
      for: 2m
      labels:
        severity: warning
      annotations:
        summary: Host out of disk space (instance {{ $labels.instance }})
        description: "Disk is almost full (< 75% left)\n  VALUE = {{ $value }}\n  LABELS = {{ $labels }}"
```

Create `HostOutOfMemory` policy for `Node memory is filling up (< 30% left)` for `warning` and `Node memory is filling up (< 15% left)` for `critical alerts`:
`oc apply -f HostOutOfMemory.yaml`

```
apiVersion: monitoring.coreos.com/v1
kind: PrometheusRule
metadata:
  name: host-memory-usage
  namespace: openshift-monitoring
spec:
  groups:
  - name: HostMemoryAlerts
    rules:
    - alert: HostOutOfMemory
      expr: (node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes * 100 < 30) * on(instance) group_left (nodename) node_uname_info{nodename=~".+"}
      for: 2m
      labels:
        severity: warning
      annotations:
        summary: Host out of memory (instance {{ $labels.instance }})
        description: "Node memory is filling up (< 10% left)\n  VALUE = {{ $value }}\n  LABELS = {{ $labels }}"
    - alert: HostOutOfMemory
      expr: (node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes * 100 < 15) * on(instance) group_left (nodename) node_uname_info{nodename=~".+"}
      for: 2m
      labels:
        severity: warning
      annotations:
        summary: Host out of memory (instance {{ $labels.instance }})
        description: "Node memory is filling up (< 10% left)\n  VALUE = {{ $value }}\n  LABELS = {{ $labels }}"
```

Create `HostSwapIsFillingUp` policy for `Swap is filling up (>70%)` for `warning` and `Swap is filling up (>80%)` for `critical alerts`:
`oc apply -f HostSwapIsFillingUp.yaml`

```
apiVersion: monitoring.coreos.com/v1
kind: PrometheusRule
metadata:
  name: host-swap-memory-usage
  namespace: openshift-monitoring
spec:
  groups:
  - name: CPUAlerts
    rules:
    - alert: HostSwapIsFillingUp
      expr: ((1 - (node_memory_SwapFree_bytes / node_memory_SwapTotal_bytes)) * 100 > 70) * on(instance) group_left (nodename) node_uname_info{nodename=~".+"}
      for: 2m
      labels:
        severity: warning
      annotations:
        summary: Host swap is filling up (instance {{ $labels.instance }})
        description: "Swap is filling up (>70%)\n  VALUE = {{ $value }}\n  LABELS = {{ $labels }}"
    - alert: HostSwapIsFillingUp
      expr: ((1 - (node_memory_SwapFree_bytes / node_memory_SwapTotal_bytes)) * 100 > 80) * on(instance) group_left (nodename) node_uname_info{nodename=~".+"}
      for: 2m
      labels:
        severity: critical
      annotations:
        summary: Host swap is filling up (instance {{ $labels.instance }})
        description: "Swap is filling up (>80%)\n  VALUE = {{ $value }}\n  LABELS = {{ $labels }}"
```

