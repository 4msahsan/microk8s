<h1>microk8s installation </h1>

<pre>

[200~microk8s is not running. Use microk8s inspect for a deeper inspection.
<sub>root@microk8s:~# microk8s start<
root@microk8s:~# microk8s status
microk8s is running
high-availability: no
  datastore master nodes: 127.0.0.1:19001
  datastore standby nodes: none
addons:
  enabled:
    ha-cluster           # (core) Configure high availability on the current node
    helm                 # (core) Helm - the package manager for Kubernetes
    helm3                # (core) Helm 3 - the package manager for Kubernetes
  disabled:
    cert-manager         # (core) Cloud native certificate management
    community            # (core) The community addons repository
    dashboard            # (core) The Kubernetes dashboard
    dns                  # (core) CoreDNS
    gpu                  # (core) Automatic enablement of Nvidia CUDA
    host-access          # (core) Allow Pods connecting to Host services smoothly
    hostpath-storage     # (core) Storage class; allocates storage from host directory
    ingress              # (core) Ingress controller for external access
    kube-ovn             # (core) An advanced network fabric for Kubernetes
    mayastor             # (core) OpenEBS MayaStor
    metallb              # (core) Loadbalancer for your Kubernetes cluster
    metrics-server       # (core) K8s Metrics Server for API access to service metrics
    observability        # (core) A lightweight observability stack for logs, traces and metrics
    prometheus           # (core) Prometheus operator for monitoring and logging
    rbac                 # (core) Role-Based Access Control for authorisation
    registry             # (core) Private image registry exposed on localhost:32000
    storage              # (core) Alias to hostpath-storage add-on, deprecated
root@microk8s:~# microk8s config
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUREekNDQWZlZ0F3SUJBZ0lVRVpXU0VwSTRUL29tQ3NHYUtIejR6UmtMMW1Bd0RRWUpLb1pJaHZjTkFRRUwKQlFBd0Z6RVZNQk1HQTFVRUF3d01NVEF1TVRVeUxqRTRNeTR4TUI0WERUSXlNVEl6TURFM016QXhNbG9YRFRNeQpNVEl5TnpFM016QXhNbG93RnpFVk1CTUdBMVVFQXd3TU1UQXVNVFV5TGpFNE15NHhNSUlCSWpBTkJna3Foa2lHCjl3MEJBUUVGQUFPQ0FROEFNSUlCQ2dLQ0FRRUExTll2OGl6cDhoOGwrZmUrZ0RCNXBVTTFaczdtWURERnk2TVUKMGx1Q1B6Rnh3UDVNUmROSGVMeUFkNzBJdlg4U3AzTldSbHNoK3I0cStVeGF1QTBvVUV4dkUzTGphMnpGUk10Nwo5R0wzREhkMUdNS0FRdmx5aTFPT2UvWUJ4Z1JpRnB3eVhIa2hXSERoanQvWnMxcXNPZmwvL0NEY1o0UlFYamZiCjMyZ1E5WlVXUC80UWg5SjRmVTVqbTBHNHRiQlByQUZVS1dpS2c3ck5JS0w2MDVycGRLT1B3OXpCa1docmpETkIKVXU3RW9KdWZYNlJuQ1RyQUJVMEZXWEFzR0NoUDNlamEvb1NSOXVTckhOWnFZTkNpc3IzWW5oN1RLc2ljZzg3TwpicG5nUnljaXdteWNIQzIwTm5obWNCL0MydEdrVnVLZTBHR1NKcjh6Z3BudVZvVmpad0lEQVFBQm8xTXdVVEFkCkJnTlZIUTRFRmdRVXo3NmZ4TG12OXRmZ2c4bEtmQXk1dkRzdHNzRXdId1lEVlIwakJCZ3dGb0FVejc2ZnhMbXYKOXRmZ2c4bEtmQXk1dkRzdHNzRXdEd1lEVlIwVEFRSC9CQVV3QXdFQi96QU5CZ2txaGtpRzl3MEJBUXNGQUFPQwpBUUVBd0YwVjIrUURtU0FxK2ZkYW5GeEJtVWtaSGk4ZlIrV1FHTThaRHpMRXY3Vlk4UGtyT1ptMWI5QnkrS09UClpMc3kyMmtYUllTWGZWVWZkNkV3N0JvYkVncGdLMi9qcjBCeWwrOVU5YzNmcFVBNUhiRFE0SC9jemNvVjhkbUQKWCtxVk4wd3Y4UWxrcFZFRWFVU29PSVpwT3h6TkQ1SnF2eWZTaWlYSnRrdW9Wc2ROcFJNd2xEU3IzOFp5ZUlIOApMZE1Wd2tUd0xnSUU2S1NYZkVUVHY3RXRaWTJWOWhRVlgyYmh4MWRYT3pqSDVYTVE1L05HSjM2bFV5cU5ZNjBHCkxhWmZ4a0NwWXJwRVNNYnFEOWJMWlVOOWgwYVh6bjZFdDZ2bC9sZUp3aDJDUmRxazMrVExjck1vQmptdFF3eXkKQUFyVHFuYXdjZDcwSXhJSGM3TEtCa1J0dnc9PQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==
    server: https://192.168.0.171:16443
  name: microk8s-cluster
contexts:
- context:
    cluster: microk8s-cluster
    user: admin
  name: microk8s
current-context: microk8s
kind: Config
preferences: {}
users:
- name: admin
  user:
    token: c1ZrMHRqQVp4RTZTUEhsT1NJQno0WGRLMytPWjlHb3pSZk93RXZQcVp2QT0K

</sub>
</pre>
