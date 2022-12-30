<pre>
<h1> Add microk8s Worker Node </h1>
<b> On Master node </b>

root@microk8s:~/microk8s# microk8s add-node
From the node you wish to join to this cluster, run the following:
microk8s join 192.168.0.171:25000/79b158cc26a51b4c478a1701c14f8179/32d494d7cebf

Use the '--worker' flag to join a node as a worker not running the control plane, eg:
microk8s join 192.168.0.171:25000/79b158cc26a51b4c478a1701c14f8179/32d494d7cebf --worker

If the node you are adding is not reachable through the default interface you can use one of the following:
microk8s join 192.168.0.171:25000/79b158cc26a51b4c478a1701c14f8179/32d494d7cebf
microk8s join fd00:a84e:3f64:bab2:a00:27ff:fec9:a359:25000/79b158cc26a51b4c478a1701c14f8179/32d494d7cebf
root@microk8s:~/microk8s#

<b> On Worker Node </b>

root@microk8s-worker:~# microk8s join 192.168.0.171:25000/39d520b906de554827427a99b19fd4c8/32d494d7cebf --worker
Contacting cluster at 192.168.0.171

The node has joined the cluster and will appear in the nodes list in a few seconds.

This worker node gets automatically configured with the API server endpoints.
If the API servers are behind a loadbalancer please set the '--refresh-interval' to '0s' in:
    /var/snap/microk8s/current/args/apiserver-proxy
and replace the API server endpoints with the one provided by the loadbalancer in:
    /var/snap/microk8s/current/args/traefik/provider.yaml

root@microk8s-worker:~#



<b> Master node</b>
root@microk8s:~/microk8s# microk8s kubectl get nodes
NAME                          STATUS   ROLES    AGE   VERSION
microk8s.example.com          Ready    <none>   65m   v1.25.4
microk8s-worker.example.com   Ready    <none>   27s   v1.25.4
root@microk8s:~/microk8s#

root@microk8s-worker:~# microk8s leave
Configuring services.
Generating new cluster certificates.
Waiting for node to start. . . . . . . . . . .
root@microk8s-worker:~#

root@microk8s:~/microk8s# microk8s kubectl get nodes
NAME                          STATUS     ROLES    AGE     VERSION
microk8s.example.com          Ready      <none>   69m     v1.25.4
microk8s-worker.example.com   NotReady   <none>   4m59s   v1.25.4
root@microk8s:~/microk8s#

root@microk8s:~/microk8s# microk8s kubectl get nodes
NAME                          STATUS     ROLES    AGE     VERSION
microk8s.example.com          Ready      <none>   69m     v1.25.4
microk8s-worker.example.com   NotReady   <none>   4m59s   v1.25.4
root@microk8s:~/microk8s# microk8s remove-node microk8s-worker.example.com
root@microk8s:~/microk8s# microk8s kubectl get nodes
NAME                   STATUS   ROLES    AGE   VERSION
microk8s.example.com   Ready    <none>   71m   v1.25.4
root@microk8s:~/microk8s#


</pre>


