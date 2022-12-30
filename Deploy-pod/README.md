<h1>microk8s Deploy Pods </h1>

<pre>

root@microk8s:~# microk8s kubectl get all -o wide
NAME                 TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE    SELECTOR
service/kubernetes   ClusterIP   10.152.183.1   <none>        443/TCP   111s   <none>
root@microk8s:~# microk8s kubectl get nodes -o wide
NAME                   STATUS   ROLES    AGE     VERSION   INTERNAL-IP     EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
microk8s.example.com   Ready    <none>   2m10s   v1.25.4   192.168.0.171   <none>        Ubuntu 22.04.1 LTS   5.15.0-46-generic   containerd://1.6.9

root@microk8s:~/microk8s# microk8s kubectl create deployment test-nginx --image=nginx
deployment.apps/test-nginx created
root@microk8s:~/microk8s# microk8s kubectl get pods
NAME                          READY   STATUS              RESTARTS   AGE
test-nginx-5d84c8d9d4-p2l9v   0/1     ContainerCreating   0          8s
root@microk8s:~/microk8s# microk8s kubectl get pods
NAME                          READY   STATUS              RESTARTS   AGE
test-nginx-5d84c8d9d4-p2l9v   0/1     ContainerCreating   0          13s
root@microk8s:~/microk8s# microk8s kubectl get pods
NAME                          READY   STATUS              RESTARTS   AGE
test-nginx-5d84c8d9d4-p2l9v   0/1     ContainerCreating   0          14s
root@microk8s:~/microk8s# microk8s kubectl get pods
NAME                          READY   STATUS    RESTARTS   AGE
test-nginx-5d84c8d9d4-p2l9v   1/1     Running   0          16s
root@microk8s:~/microk8s# microk8s kubectl exec test-nginx-7f4c594655-nh9mb -- env
Error from server (NotFound): pods "test-nginx-7f4c594655-nh9mb" not found
root@microk8s:~/microk8s# microk8s kubectl exec test-nginx-5d84c8d9d4-p2l9v -- env
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=test-nginx-5d84c8d9d4-p2l9v
NGINX_VERSION=1.23.3
NJS_VERSION=0.7.9
PKG_RELEASE=1~bullseye
KUBERNETES_PORT_443_TCP_ADDR=10.152.183.1
KUBERNETES_SERVICE_HOST=10.152.183.1
KUBERNETES_SERVICE_PORT=443
KUBERNETES_SERVICE_PORT_HTTPS=443
KUBERNETES_PORT=tcp://10.152.183.1:443
KUBERNETES_PORT_443_TCP=tcp://10.152.183.1:443
KUBERNETES_PORT_443_TCP_PROTO=tcp
KUBERNETES_PORT_443_TCP_PORT=443
HOME=/root
root@microk8s:~/microk8s#


root@microk8s:~/microk8s# microk8s kubectl exec -it test-nginx-5d84c8d9d4-p2l9v -- bash
root@test-nginx-5d84c8d9d4-p2l9v:/# df -h
Filesystem                         Size  Used Avail Use% Mounted on
overlay                             48G  8.6G   37G  19% /
tmpfs                               64M     0   64M   0% /dev
/dev/mapper/ubuntu--vg-ubuntu--lv   48G  8.6G   37G  19% /etc/hosts
shm                                 64M     0   64M   0% /dev/shm
tmpfs                              3.8G   12K  3.8G   1% /run/secrets/kubernetes.io/serviceaccount
tmpfs                              2.0G     0  2.0G   0% /proc/acpi
tmpfs                              2.0G     0  2.0G   0% /proc/scsi
tmpfs                              2.0G     0  2.0G   0% /sys/firmware
root@test-nginx-5d84c8d9d4-p2l9v:/# uname -r
5.15.0-46-generic
root@test-nginx-5d84c8d9d4-p2l9v:/# cat /etc/hosts
# Kubernetes-managed hosts file.
127.0.0.1       localhost
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
fe00::0 ip6-mcastprefix
fe00::1 ip6-allnodes
fe00::2 ip6-allrouters
10.1.156.67     test-nginx-5d84c8d9d4-p2l9v
root@test-nginx-5d84c8d9d4-p2l9v:/# exit
exit
root@microk8s:~/microk8s# microk8s kubectl scale deployment test-nginx --replicas=5
deployment.apps/test-nginx scaled
root@microk8s:~/microk8s# microk8s kubectl get pods
NAME                          READY   STATUS    RESTARTS   AGE
test-nginx-5d84c8d9d4-p2l9v   1/1     Running   0          4m8s
test-nginx-5d84c8d9d4-xrvqs   1/1     Running   0          10s
test-nginx-5d84c8d9d4-zwvjq   1/1     Running   0          10s
test-nginx-5d84c8d9d4-q52t4   1/1     Running   0          10s
test-nginx-5d84c8d9d4-nw92r   1/1     Running   0          10s
root@microk8s:~/microk8s# microk8s kubectl expose deployment test-nginx --type="NodePort" --port 80
service/test-nginx exposed
root@microk8s:~/microk8s# microk8s kubectl get services test-nginx
NAME         TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
test-nginx   NodePort   10.152.183.79   <none>        80:32496/TCP   14s
root@microk8s:~/microk8s# curl 10.152.183.79
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
root@microk8s:~/microk8s# microk8s kubectl delete services test-nginx
service "test-nginx" deleted
root@microk8s:~/microk8s# microk8s kubectl delete deployment test-nginx
deployment.apps "test-nginx" deleted
root@microk8s:~/microk8s#



</pre>
