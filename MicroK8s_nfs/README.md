<h1> K8s PV and PVC with NFS Server </h1>
<pre>

<b>  NFS Server </b>
apt -y install nfs-kernel-server 

mahsan@k8snfs:~/nfsshare$ cat /etc/idmapd.conf
[General]

Verbosity = 0
 set your own domain here, if it differs from FQDN minus hostname

 Domain = example.com
[Mapping]

Nobody-User = nobody
Nobody-Group = nogroup
mahsan@k8snfs:/nfsshare$ cat /etc/exports

/home/mahsan/nfsshare 192.168.0.133/24(rw,no_root_squash)

mahsan@k8snfs:/nfsshare$ ls -ltr /home/mahsan/nfsshare/
total 4
-rw-rw-r-- 1 mahsan mahsan    0 May 31 18:59 asd
-rw-rw-r-- 1 mahsan mahsan 3893 May 31 18:59 test1
mahsan@k8snfs:/nfsshare$


systemctl restart nfs-server

mahsan@k8snfs:~/nfsshare$ sudo exportfs
[sudo] password for mahsan:
/home/mahsan/nfsshare
                192.168.0.133/24
mahsan@k8snfs:/nfsshare$


<b> Nfs Client </b>
mahsan@k8sawx:/mnt$ grep /mnt /proc/mounts
192.168.0.133:/home/mahsan/nfsshare /mnt nfs4 rw,relatime,vers=4.2,rsize=131072,wsize=131072,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=192.168.0.131,local_lock=none,addr=192.168.0.133 0 0
mahsan@k8sawx:/mnt$

mahsan@k8sawx:$ sudo mount -t nfs 192.168.0.133:/home/mahsan/nfsshare /mnt/CIFS
mahsan@k8sawx:$ df -h
Filesystem                           Size  Used Avail Use% Mounted on
tmpfs                                795M  1.1M  794M   1% /run
/dev/mapper/ubuntu--vg-ubuntu--lv     48G  8.3G   38G  19% /
tmpfs                                3.9G     0  3.9G   0% /dev/shm
tmpfs                                5.0M     0  5.0M   0% /run/lock
/dev/sda2                            2.0G  127M  1.7G   7% /boot
tmpfs                                795M  4.0K  795M   1% /run/user/1000
192.168.0.133:/home/mahsan/nfsshare   48G  6.9G   39G  16% /mnt/CIFS

mahsan@k8sawx:$ sudo umount -f /mnt/CIFS
mahsan@k8sawx:$ df -h
Filesystem                         Size  Used Avail Use% Mounted on
tmpfs                              795M  1.1M  794M   1% /run
/dev/mapper/ubuntu--vg-ubuntu--lv   48G  8.3G   38G  19% /
tmpfs                              3.9G     0  3.9G   0% /dev/shm
tmpfs                              5.0M     0  5.0M   0% /run/lock
/dev/sda2                          2.0G  127M  1.7G   7% /boot
tmpfs                              795M  4.0K  795M   1% /run/user/1000
mahsan@k8sawx:$



root@k8s:# hostnamectl set-hostname k8sawx.example.com
root@k8s:# cat /etc/netplan/00-installer-config.yaml
network:
  ethernets:
    enp0s3:
      addresses: [192.168.0.131/24]
      gateway4: 192.168.0.1
      nameservers:
        addresses: [4.2.2.2, 8.8.8.8]
  version: 2
root@k8s:#


mahsan@k8sawx:$ sudo snap install microk8s --classic
[sudo] password for mahsan:
Sorry, try again.
[sudo] password for mahsan:
microk8s (1.26/stable) v1.26.4 from Canonical✓ installed
mahsan@k8sawx:$

mahsan@k8sawx:$ sudo microk8s status
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
    minio                # (core) MinIO object storage
    observability        # (core) A lightweight observability stack for logs, traces and metrics
    prometheus           # (core) Prometheus operator for monitoring and logging
    rbac                 # (core) Role-Based Access Control for authorisation
    registry             # (core) Private image registry exposed on localhost:32000
    storage              # (core) Alias to hostpath-storage add-on, deprecated
mahsan@k8sawx:$


mahsan@k8sawx:$ sudo microk8s config
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUREekNDQWZlZ0F3SUJBZ0lVRkVDRFkranJJbEZnSElTaHRrTlRPbi9zZ1A4d0RRWUpLb1pJaHZjTkFRRUwKQlFBd0Z6RVZNQk1HQTFVRUF3d01NVEF1TVRVeUxqRTRNeTR4TUI0WERUSXpNRFV6TVRFNE1EUXlORm9YRFRNegpNRFV5T0RFNE1EUXlORm93RnpFVk1CTUdBMVVFQXd3TU1UQXVNVFV5TGpFNE15NHhNSUlCSWpBTkJna3Foa2lHCjl3MEJBUUVGQUFPQ0FROEFNSUlCQ2dLQ0FRRUFuUEcwNWNDRE5sckgyWmhJNk1kS0FvTFplNDk5VEwvS0FyMjUKU1ROTkFkRm51cUlQVEM4MDA3TGM4aUw2VXZoY1BHTEV6OGphQkVRR3FKOXBSbTdEVE9SaUZCaVlITWZDdU9JbApKOWpFY1BLNTh5NFJ2cHI0YThvZENNTkFQK3VCMDJMT3BkQlljOU8vZmlaOGxzbTFaSmRuSUNhS3lwdkVveHU0CnNjU1k0WHBPN24rTGJibzhaVlYweUp6cUdJSGEvOFpNaUFsNkwxYzV2MWUyTFBxMHpIS0J0UE53OWl1ZTI5aDIKL1BTckFrOVVxQy84b1VZd3lVc3ZrRjF1VGl2aFlRYXIvVXRhSkw5bTBQSzMwNC9hdkRqbjVUeEdKaTFVYmVvNgpzdnZwU1d2NDNjaDZEWjB0Rk5EQzFTUWtCeUM3dGVLYjVXcDFsQm9sOWpUOUoyM25pd0lEQVFBQm8xTXdVVEFkCkJnTlZIUTRFRmdRVThtUVJtMzg0OUVoWW55RHlYK0Zvd3NKWHllWXdId1lEVlIwakJCZ3dGb0FVOG1RUm0zODQKOUVoWW55RHlYK0Zvd3NKWHllWXdEd1lEVlIwVEFRSC9CQVV3QXdFQi96QU5CZ2txaGtpRzl3MEJBUXNGQUFPQwpBUUVBTzZDMkJ0U3JtTUkxT0hxUkpUTHZDekFrYjNOUTBxZnN3UG0zdUY3ckdsTnhOZXZ3TDhWZWJwVXVobFdVCkM4N1MwOW1mMXdNOVV5N1NVY3VmMmZtRURONjdYY1dJeTJWWEhXblFTelk3U0Z6SmExSytNajVRWHhrTlJYU0IKR0x2N2txVGJOWTIrcldCdk5yaGlYaWk2MHVtUWRtTEdlQ0FsUDYwR1lwWDBaYlA2VVQ2ekZubFhXU2VrTGdSegpIcGNaVEplME81eG8xY3ZOdndFODZrTDdHZG9jUStlSFY3RTRXQWpyamFqM2podVB3TXo1WHdSTG42Ui9nQmV6CjBCUU8yckdFcU05RW9aRVJRa1Bva21xZms3ZFZXMWdGUkNyNzF5K0UwSEdWbllLalpoYjBOWFczMGJWRnFXSmMKc0lXOGJDL3A0NSttNTFWeFVGUmJHUkw3UWc9PQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==
    server: https://192.168.0.131:16443
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
    token: ZWdzV2t1S3NDejRCb3JEUGZxdDFCd0Y1RlM5OGZaUk5qR2NnNG04bFl3VT0K

mahsan@k8sawx:$


mahsan@k8sawx:$ sudo microk8s kubectl get all
NAME                 TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.152.183.1   <none>        443/TCP   10m
mahsan@k8sawx:$


root@k8sawx:# microk8s kubectl get nodes
NAME                 STATUS   ROLES    AGE   VERSION
k8sawx.example.com   Ready    <none>   10m   v1.26.4
root@k8sawx:#

root@k8sawx: microk8s stop
Stopped.
root@k8sawx:# microk8s status
microk8s is not running, try microk8s start
root@k8sawx:#


root@k8sawx:# microk8s start
root@k8sawx:#

root@k8sawx:# snap disable microk8s
microk8s disabled
root@k8sawx:# snap enable microk8s
microk8s enabled
root@k8sawx:#

root@k8sawx:# microk8s stop
Stopped.
root@k8sawx:# microk8s status
microk8s is not running, try microk8s start
root@k8sawx:#

## Deployment ##
mahsan@k8sawx:$ microk8s kubectl get deploy
microk8s is not running, try microk8s start
mahsan@k8sawx:$ microk8s start
mahsan@k8sawx:$ microk8s kubectl get deploy
No resources found in default namespace.
mahsan@k8sawx:~$ microk8s kubectl get deploy --all-namespaces
NAMESPACE     NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
kube-system   calico-kube-controllers   1/1     1            1           101m
mahsan@k8sawx:$

mahsan@k8sawx:$ microk8s kubectl get deploy --all-namespaces
NAMESPACE     NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
kube-system   calico-kube-controllers   1/1     1            1           101m
mahsan@k8sawx:$ microk8s kubectl create deployment msa-nginx --image=nginx
deployment.apps/msa-nginx created
mahsan@k8sawx:$ microk8s kubectl get deploy
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
msa-nginx   0/1     1            0           9s
mahsan@k8sawx:$ microk8s kubectl get ns
NAME              STATUS   AGE
kube-system       Active   103m
kube-public       Active   103m
kube-node-lease   Active   103m
default           Active   103m
mahsan@k8sawx:$

mahsan@k8sawx:$ microk8s kubectl get pods
NAME                         READY   STATUS    RESTARTS   AGE
msa-nginx-555b7f4c7c-jx2hb   1/1     Running   0          77s
mahsan@k8sawx:$ microk8s kubectl get pods -o wide
NAME                         READY   STATUS    RESTARTS   AGE   IP           NODE                 NOMINATED NODE   READINESS GATES
msa-nginx-555b7f4c7c-jx2hb   1/1     Running   0          87s   10.1.233.4   k8sawx.example.com   <none>           <none>
mahsan@k8sawx:$

mahsan@k8sawx:$ microk8s kubectl exec msa-nginx-555b7f4c7c-jx2hb -- env
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=msa-nginx-555b7f4c7c-jx2hb
NGINX_VERSION=1.25.0
NJS_VERSION=0.7.12
PKG_RELEASE=1~bullseye
KUBERNETES_PORT_443_TCP=tcp://10.152.183.1:443
KUBERNETES_PORT_443_TCP_PROTO=tcp
KUBERNETES_PORT_443_TCP_PORT=443
KUBERNETES_PORT_443_TCP_ADDR=10.152.183.1
KUBERNETES_SERVICE_HOST=10.152.183.1
KUBERNETES_SERVICE_PORT=443
KUBERNETES_SERVICE_PORT_HTTPS=443
KUBERNETES_PORT=tcp://10.152.183.1:443

mahsan@k8sawx:$ microk8s kubectl exec -it msa-nginx-555b7f4c7c-jx2hb -- bash
root@msa-nginx-555b7f4c7c-jx2hb:/# hostname
msa-nginx-555b7f4c7c-jx2hb
root@msa-nginx-555b7f4c7c-jx2hb:/# df -h
Filesystem                         Size  Used Avail Use% Mounted on
overlay                             48G  8.5G   37G  19% /
tmpfs                               64M     0   64M   0% /dev
/dev/mapper/ubuntu--vg-ubuntu--lv   48G  8.5G   37G  19% /etc/hosts
shm                                 64M     0   64M   0% /dev/shm
tmpfs                              7.7G   12K  7.7G   1% /run/secrets/kubernetes.io/serviceaccount
tmpfs                              3.9G     0  3.9G   0% /proc/acpi
tmpfs                              3.9G     0  3.9G   0% /proc/scsi
tmpfs                              3.9G     0  3.9G   0% /sys/firmware
root@msa-nginx-555b7f4c7c-jx2hb:/#


mahsan@k8sawx:$ microk8s kubectl logs msa-nginx-555b7f4c7c-jx2hb
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2023/05/31 19:48:25 [notice] 1#1: using the "epoll" event method
2023/05/31 19:48:25 [notice] 1#1: nginx/1.25.0
2023/05/31 19:48:25 [notice] 1#1: built by gcc 10.2.1 20210110 (Debian 10.2.1-6)
2023/05/31 19:48:25 [notice] 1#1: OS: Linux 5.15.0-46-generic
2023/05/31 19:48:25 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 65536:65536
2023/05/31 19:48:25 [notice] 1#1: start worker processes
2023/05/31 19:48:25 [notice] 1#1: start worker process 29
2023/05/31 19:48:25 [notice] 1#1: start worker process 30
2023/05/31 19:48:25 [notice] 1#1: start worker process 31
2023/05/31 19:48:25 [notice] 1#1: start worker process 32
mahsan@k8sawx:$


mahsan@k8sawx:$ microk8s kubectl get deploy
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
msa-nginx   1/1     1            1           8m36s
mahsan@k8sawx:$ microk8s kubectl get pods
NAME                         READY   STATUS    RESTARTS   AGE
msa-nginx-555b7f4c7c-jx2hb   1/1     Running   0          8m41s
mahsan@k8sawx:$ microk8s kubectl scale deployment msa-nginx --replicas=3
deployment.apps/msa-nginx scaled
mahsan@k8sawx:$ microk8s kubectl get pods
NAME                         READY   STATUS    RESTARTS   AGE
msa-nginx-555b7f4c7c-jx2hb   1/1     Running   0          9m14s
msa-nginx-555b7f4c7c-wbhz9   1/1     Running   0          3s
msa-nginx-555b7f4c7c-pr7rp   1/1     Running   0          3s
mahsan@k8sawx:$ microk8s kubectl get deploy
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
msa-nginx   3/3     3            3           9m20s
mahsan@k8sawx:$ microk8s kubectl get pods -o wide
NAME                         READY   STATUS    RESTARTS   AGE     IP           NODE                 NOMINATED NODE   READINESS GATES
msa-nginx-555b7f4c7c-jx2hb   1/1     Running   0          9m28s   10.1.233.4   k8sawx.example.com   <none>           <none>
msa-nginx-555b7f4c7c-wbhz9   1/1     Running   0          17s     10.1.233.5   k8sawx.example.com   <none>           <none>
msa-nginx-555b7f4c7c-pr7rp   1/1     Running   0          17s     10.1.233.6   k8sawx.example.com   <none>           <none>
mahsan@k8sawx:~$


mahsan@k8sawx:$ microk8s kubectl expose deployment msa-nginx --type="NodePort" --port 80
service/msa-nginx exposed

mahsan@k8sawx:$ microk8s kubectl get svc
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP   10.152.183.1     <none>        443/TCP        117m
msa-nginx    NodePort    10.152.183.231   <none>        80:32572/TCP   21s
mahsan@k8sawx:$

mahsan@k8sawx:$ curl 10.152.183.231
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
mahsan@k8sawx:$

mahsan@k8sawx:$ microk8s kubectl delete svc msa-nginx
service "msa-nginx" deleted
mahsan@k8sawx:$ microk8s kubectl get svc
NAME         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.152.183.1   <none>        443/TCP   120m
mahsan@k8sawx:$ microk8s kubectl delete deployment  msa-nginx
deployment.apps "msa-nginx" deleted
mahsan@k8sawx:$ microk8s kubectl get deployment
No resources found in default namespace.


 <b> MicroK8s : Add Nodes </b>


mahsan@k8sawx:~$ microk8s add-node
From the node you wish to join to this cluster, run the following:
microk8s join 192.168.0.131:25000/16e4673e572df7a72a10677860e30364/b2760408afd5

Use the '--worker' flag to join a node as a worker not running the control plane, eg:
microk8s join 192.168.0.131:25000/16e4673e572df7a72a10677860e30364/b2760408afd5 --worker

If the node you are adding is not reachable through the default interface you can use one of the following:
microk8s join 192.168.0.131:25000/16e4673e572df7a72a10677860e30364/b2760408afd5
microk8s join fd00:a84e:3f64:bab2:a00:27ff:fe52:1bf4:25000/16e4673e572df7a72a10677860e30364/b2760408afd5
mahsan@k8sawx:~$

mahsan@k8sawsnode1:$ sudo snap install microk8s --classic
[sudo] password for mahsan:
microk8s (1.26/stable) v1.26.4 from Canonical✓ installed
mahsan@k8sawsnode1:$


mahsan@k8sawx:/YAML$ cat nfs-pv.yml
apiVersion: v1
kind: PersistentVolume
metadata:
   any PV name
  name: nfs-pv
spec:
  capacity:
     storage size
    storage: 10Gi
  accessModes:
    # Access Modes:
    # - ReadWriteMany (RW from multi nodes)
    # - ReadWriteOnce (RW from a node)
    # - ReadOnlyMany (R from multi nodes)
    - ReadWriteMany
  persistentVolumeReclaimPolicy:
    # retain even if pods terminate
    Retain
  storageClassName: nfs-server
  nfs:
    # NFS server's definition
    path: /home/mahsan/nfsshare
    server: 192.168.0.133
    readOnly: false
mahsan@k8sawx:/YAML$

mahsan@k8sawx:/YAML$ sudo microk8s kubectl apply -f nfs-pv.yml
persistentvolume/nfs-pv created
mahsan@k8sawx:/YAML$ microk8s kubectl get pv
NAME     CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
nfs-pv   10Gi       RWX            Retain           Available           nfs-server              16s

mahsan@k8sawx:/YAML$ m kubectl describe  pv nfs-pv
Name:            nfs-pv
Labels:          <none>
Annotations:     <none>
Finalizers:      [kubernetes.io/pv-protection]
StorageClass:    nfs-server
Status:          Available
Claim:
Reclaim Policy:  Retain
Access Modes:    RWX
VolumeMode:      Filesystem
Capacity:        10Gi
Node Affinity:   <none>
Message:
Source:
    Type:      NFS (an NFS mount that lasts the lifetime of a pod)
    Server:    192.168.0.133
    Path:      /home/mahsan/nfsshare
    ReadOnly:  false
Events:        <none>
mahsan@k8sawx:/YAML$

<b> Create PVC </b>

mahsan@k8sawx:/YAML$ cat nfs-pvc.yml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
   <em>any PVC name</em>
  name: nfs-pvc
spec:
  accessModes:
  - ReadWriteMany
  resources:
     requests:
       storage: 10Gi
  storageClassName: nfs-server


mahsan@k8sawx:/YAML$ m kubectl get pvc
NAME      STATUS   VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   AGE
nfs-pvc   Bound    nfs-pv   10Gi       RWX            nfs-server     11s


mahsan@k8sawx:/YAML$ m kubectl get pv,pvc
NAME                      CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM             STORAGECLASS   REASON   AGE
persistentvolume/nfs-pv   10Gi       RWX            Retain           Bound    default/nfs-pvc   nfs-server              7m58s

NAME                            STATUS   VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   AGE
persistentvolumeclaim/nfs-pvc   Bound    nfs-pv   10Gi       RWX            nfs-server     32s
 
 mahsan@k8sawx:/YAML$ m kubectl describe pvc  nfs-pvc
Name:          nfs-pvc
Namespace:     default
StorageClass:  nfs-server
Status:        Bound
Volume:        nfs-pv
Labels:        <none>
Annotations:   pv.kubernetes.io/bind-completed: yes
               pv.kubernetes.io/bound-by-controller: yes
Finalizers:    [kubernetes.io/pvc-protection]
Capacity:      10Gi
Access Modes:  RWX
VolumeMode:    Filesystem
Used By:       <none>
Events:        <none>
mahsan@k8sawx:/YAML$ m kubectl get pv,pvc
NAME                      CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM             STORAGECLASS   REASON   AGE
persistentvolume/nfs-pv   10Gi       RWX            Retain           Bound    default/nfs-pvc   nfs-server              10m

NAME                            STATUS   VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   AGE
persistentvolumeclaim/nfs-pvc   Bound    nfs-pv   10Gi       RWX            nfs-server     2m43s
mahsan@k8sawx:/YAML$


<b> NFS POD Creation </b>

mahsan@k8sawx:/YAML$ cat nginx-nfs.yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-nfs
  labels:
    name: nginx-nfs
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx-nfs
  template:
    metadata:
      labels:
        app: nginx-nfs
    spec:
      containers:
      - name: nginx-nfs
        image: nginx
        ports:
          - name: web
            containerPort: 80
        volumeMounts:
          - name: nfs-share
            # mount point in container
            mountPath: /usr/share/nginx/html
      volumes:
        - name: nfs-share
          persistentVolumeClaim:
            # PVC name you created
            claimName: nfs-pvc


sudo microk8s kubectl apply -f nginx-nfs.yml


mahsan@k8sawx:/YAML$ microk8s kubectl get pods
NAME                        READY   STATUS    RESTARTS   AGE
nginx-nfs-6bbbb89cd-78982   1/1     Running   0          11s
nginx-nfs-6bbbb89cd-q6gb7   1/1     Running   0          11s
nginx-nfs-6bbbb89cd-hd9bn   1/1     Running   0          11s
mahsan@k8sawx:/YAML$ microk8s kubectl get deploy
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
nginx-nfs   3/3     3            3           21s

mahsan@k8sawx:/YAML$ microk8s kubectl describe pod nginx-nfs-6bbbb89cd-78982
Name:             nginx-nfs-6bbbb89cd-78982
Namespace:        default
Priority:         0
Service Account:  default
Node:             k8sawx.example.com/192.168.0.131
Start Time:       Thu, 01 Jun 2023 18:17:13 +0000
Labels:           app=nginx-nfs
                  pod-template-hash=6bbbb89cd
Annotations:      cni.projectcalico.org/containerID: 624f484e39ff8c5b4ec49784f0b52d8ab110d4729ea85f6bff7b96e64fe25706
                  cni.projectcalico.org/podIP: 10.1.233.8/32
                  cni.projectcalico.org/podIPs: 10.1.233.8/32
Status:           Running
IP:               10.1.233.8
IPs:
  IP:           10.1.233.8
Controlled By:  ReplicaSet/nginx-nfs-6bbbb89cd
Containers:
  nginx-nfs:
    Container ID:   containerd://b248e421f9365b7829bcde1708d79e3a2678f4369d2fa79ea80e239402ff0abb
    Image:          nginx
    Image ID:       docker.io/library/nginx@sha256:af296b188c7b7df99ba960ca614439c99cb7cf252ed7bbc23e90cfda59092305
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Thu, 01 Jun 2023 18:17:16 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /usr/share/nginx/html from nfs-share (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-p8rlq (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  nfs-share:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  nfs-pvc
    ReadOnly:   false
  kube-api-access-p8rlq:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason             Age                 From     Message
  ----     ------             ----                ----     -------
  Warning  MissingClusterDNS  2s (x9 over 5m54s)  kubelet  pod: "nginx-nfs-6bbbb89cd-78982_default(f2151ee4-1b77-4893-89e7-815d5173d1ca)". kubelet does not have ClusterDNS IP configured and cannot create Pod using "ClusterFirst" policy. Falling back to "Default" policy.
mahsan@k8sawx:~/YAML$ microk8s kubectl get deploy
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
nginx-nfs   3/3     3            3           6m41s

mahsan@k8sawx:/YAML$ microk8s kubectl describe deploy nginx-nfs
Name:                   nginx-nfs
Namespace:              default
CreationTimestamp:      Thu, 01 Jun 2023 18:17:13 +0000
Labels:                 name=nginx-nfs
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=nginx-nfs
Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=nginx-nfs
  Containers:
   nginx-nfs:
    Image:        nginx
    Port:         80/TCP
    Host Port:    0/TCP
    Environment:  <none>
    Mounts:
      /usr/share/nginx/html from nfs-share (rw)
  Volumes:
   nfs-share:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  nfs-pvc
    ReadOnly:   false
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   nginx-nfs-6bbbb89cd (3/3 replicas created)
Events:          <none>

mahsan@k8sawx:/YAML$ microk8s kubectl get pods
NAME                        READY   STATUS    RESTARTS   AGE
nginx-nfs-6bbbb89cd-78982   1/1     Running   0          64m
nginx-nfs-6bbbb89cd-q6gb7   1/1     Running   0          64m
nginx-nfs-6bbbb89cd-hd9bn   1/1     Running   0          64m
mahsan@k8sawx:/YAML$ microk8s kubectl get deploy
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
nginx-nfs   3/3     3            3           64m

mahsan@k8sawx:/YAML$ microk8s kubectl expose deployment nginx-nfs --type="NodePort" --port 80
service/nginx-nfs exposed
mahsan@k8sawx:/YAML$ microk8s kubectl get svc
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP   10.152.183.1    <none>        443/TCP        25h
nginx-nfs    NodePort    10.152.183.62   <none>        80:31659/TCP   7s
mahsan@k8sawx:/YAML$ microk8s kubectl describe  svc nginx-nfs
Name:                     nginx-nfs
Namespace:                default
Labels:                   name=nginx-nfs
Annotations:              <none>
Selector:                 app=nginx-nfs
Type:                     NodePort
IP Family Policy:         SingleStack
IP Families:              IPv4
IP:                       10.152.183.62
IPs:                      10.152.183.62
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  31659/TCP
Endpoints:                10.1.233.10:80,10.1.233.8:80,10.1.233.9:80
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
mahsan@k8sawx:/YAML$

mahsan@k8sawx:/YAML$ microk8s kubectl get deploy -o wide
NAME        READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES   SELECTOR
nginx-nfs   3/3     3            3           66m   nginx-nfs    nginx    app=nginx-nfs
mahsan@k8sawx:/YAML$ microk8s kubectl get pod -o wide
NAME                        READY   STATUS    RESTARTS   AGE   IP            NODE                 NOMINATED NODE   READINESS GATES
nginx-nfs-6bbbb89cd-78982   1/1     Running   0          66m   10.1.233.8    k8sawx.example.com   <none>           <none>
nginx-nfs-6bbbb89cd-q6gb7   1/1     Running   0          66m   10.1.233.9    k8sawx.example.com   <none>           <none>
nginx-nfs-6bbbb89cd-hd9bn   1/1     Running   0          66m   10.1.233.10   k8sawx.example.com   <none>           <none>
mahsan@k8sawx:/YAML$
mahsan@k8sawx:/YAML$ microk8s kubectl get service nginx-nfs
NAME        TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
nginx-nfs   NodePort   10.152.183.62   <none>        80:31659/TCP   3m6s
mahsan@k8sawx:/YAML$

<b> on NFS server, create a test file under the NFS shared directory </b>

mahsan@k8snfs:/nfsshare$  echo 'NFS Persistent Storage Test' > /home/mahsan/nfsshare/index.html
mahsan@k8snfs:/nfsshare$ ll
total 16
drwxrwxr-x 2 mahsan mahsan 4096 Jun  1 19:34 ./
drwxr-x--- 5 mahsan mahsan 4096 Jun  1 17:23 ../
-rw-rw-r-- 1 mahsan mahsan    0 May 31 18:59 asd
-rw-rw-r-- 1 mahsan mahsan   28 Jun  1 19:34 index.html
-rw-rw-r-- 1 mahsan mahsan 3893 May 31 18:59 test1

<b>  verify accesses on a node which users can access to microk8s cluster </b>

</pre>

