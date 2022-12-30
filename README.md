#microk8s installation 

<pre>

mahsan@u22srv:~/microk8s$ sudo snap install microk8s --classic
microk8s (1.24/stable) v1.24.3 from Canonical✓ installed
mahsan@u22srv:~/microk8s$ sudo  microk8s status
microk8s is running
high-availability: no
  datastore master nodes: 127.0.0.1:19001
  datastore standby nodes: none
addons:
  enabled:
    ha-cluster           # (core) Configure high availability on the current node
  disabled:
    community            # (core) The community addons repository
    dashboard            # (core) The Kubernetes dashboard
    dns                  # (core) CoreDNS
    gpu                  # (core) Automatic enablement of Nvidia CUDA
    helm                 # (core) Helm 2 - the package manager for Kubernetes
    helm3                # (core) Helm 3 - Kubernetes package manager
    host-access          # (core) Allow Pods connecting to Host services smoothly
    hostpath-storage     # (core) Storage class; allocates storage from host directory
    ingress              # (core) Ingress controller for external access
    mayastor             # (core) OpenEBS MayaStor
    metallb              # (core) Loadbalancer for your Kubernetes cluster
    metrics-server       # (core) K8s Metrics Server for API access to service metrics
    prometheus           # (core) Prometheus operator for monitoring and logging
    rbac                 # (core) Role-Based Access Control for authorisation
    registry             # (core) Private image registry exposed on localhost:32000
    storage              # (core) Alias to hostpath-storage add-on, deprecated
mahsan@u22srv:~/microk8s$ microk8s config
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUREekNDQWZlZ0F3SUJBZ0lVTnFmS2FNOFNZYzdvVksyY3ZsaXZGYkpOYktZd0RRWUpLb1pJaHZjTkFRRUwKQlFBd0Z6RVZNQk1HQTFVRUF3d01NVEF1TVRVeUxqRTRNeTR4TUI0WERUSXlNRGd5TlRJek1qSXlNMW9YRFRNeQpNRGd5TWpJek1qSXlNMW93RnpFVk1CTUdBMVVFQXd3TU1UQXVNVFV5TGpFNE15NHhNSUlCSWpBTkJna3Foa2lHCjl3MEJBUUVGQUFPQ0FROEFNSUlCQ2dLQ0FRRUF6a09lM2MrOTFmbUpyck5zdDlWbU1QQWgwUWdrelowcU9EeWEKUEwzdWtXQ0ZaWmxQWFJnU1ZseExoWGFXeDU2b3lBODkvcnVxOVpkdVMyMEdNM1pzdDZnY2R5VU4yMHkwUHh2cQp4Vmh0N1Z0Q2UrVGpwZEk0U3h3eTBpT3FVZTQ1QWd5aHBBZ0lrdmVNMjRQVCtNVjAzS2R3WXRHdmVZYmwrUzFKCjFZZnA3T00yVVhTdm1LQ2M3R1RNdFRCYlhVSmRiOVZjeXprWTNYcldQZU8wblZsWkZ6a0lId1lyWmZtS3ZuK1EKd0hsazY0WEU0OGlhMmxUMTJsU21MU01FVTRsWUV5T1VXVEY4SlZ4dWdVSGFzOTRrbFYvQS9BNFBEZ0RaNER0NApmRXRlNzhZSnp2NmQvcDRxelJnS05mQUpnS1lTVVAwUmcyb2NEMStEM1AxeDlsWG9Vd0lEQVFBQm8xTXdVVEFkCkJnTlZIUTRFRmdRVVQ3KysvSWM1ZVVMNFBHcktaQXpHdkhLcjNoa3dId1lEVlIwakJCZ3dGb0FVVDcrKy9JYzUKZVVMNFBHcktaQXpHdkhLcjNoa3dEd1lEVlIwVEFRSC9CQVV3QXdFQi96QU5CZ2txaGtpRzl3MEJBUXNGQUFPQwpBUUVBUFJtcWJXNmQ0cXlDWUhkUGowdk1nWWpaVVRpQ2ZacE9nNGQvZTl5ajZjWk9GOGxMcXRUTURVVEhNTzQxCjZjVmw2T1dQcWQyMzJvSDhiU0EzT1FSanY3RWF2YjhRajloVmVaeGFtMldWZTNPY21uOW4yZlp2M1YzTzNjUGUKNlk0ZVBubkhDei9Ja1ZSK2pmd0dwUGFORS9HNXM0cnNzS0dHWVJBb1d0bjFYekF3a2lNaHl5S2k0SFRjc0dPWgpjcUhyVkhOSk1ka3N6Wld6VHFYVmhlWjBsSHBwVmFBTGZmVzJNaGFXQUs5TDNWY3JkS2lFUTFQTlBXNDQ5aFhNClRsS09xM2NRZVJCWHA1a2l6eU1XblJtK0c2YXZ0bDJITjVFc3d1eGFuZ1NXWmtIWmJHekFxYzRhakNHNTJSMFMKZ0xuVWZWMklCZnROYUxVV1IrZDdiWUROdGc9PQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==
    server: https://192.168.0.169:16443
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
    token: dmF0OTNVZ2pKeEo4eThvUml0cmFwK0x3YVV5ZU0wQ3NoK082ZnNlMGJNND0K

mahsan@u22srv:~/microk8s$ sudo microk8s kubectl get nodes
NAME     STATUS   ROLES    AGE    VERSION
u22srv   Ready    <none>   2m7s   v1.24.3-2+63243a96d1c393
mahsan@u22srv:~/microk8s$ sudo microk8s stop
Run service command "stop" for services ["daemon-apiserver" "daemon-apiserver-kicker" "daemon-cluster-agent" "daemon-containerd" "daemo…
Stopped.
mahsan@u22srv:~/microk8s$ sudo microk8s status
microk8s is not running, try microk8s start
mahsan@u22srv:~/microk8s$ sudo microk8s start
Started.

mahsan@u22srv:~/microk8s$ sudo microk8s status
microk8s is running
high-availability: no
  datastore master nodes: 127.0.0.1:19001
  datastore standby nodes: none
addons:
  enabled:
    ha-cluster           # (core) Configure high availability on the current node
  disabled:
    community            # (core) The community addons repository
    dashboard            # (core) The Kubernetes dashboard
    dns                  # (core) CoreDNS
    gpu                  # (core) Automatic enablement of Nvidia CUDA
    helm                 # (core) Helm 2 - the package manager for Kubernetes
    helm3                # (core) Helm 3 - Kubernetes package manager
    host-access          # (core) Allow Pods connecting to Host services smoothly
    hostpath-storage     # (core) Storage class; allocates storage from host directory
    ingress              # (core) Ingress controller for external access
    mayastor             # (core) OpenEBS MayaStor
    metallb              # (core) Loadbalancer for your Kubernetes cluster
    metrics-server       # (core) K8s Metrics Server for API access to service metrics
    prometheus           # (core) Prometheus operator for monitoring and logging
    rbac                 # (core) Role-Based Access Control for authorisation
    registry             # (core) Private image registry exposed on localhost:32000
    storage              # (core) Alias to hostpath-storage add-on, deprecated



</pre>
