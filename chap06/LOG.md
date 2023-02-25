Under Docker Desktop & kubernetes cluster: docker-desktop
```
05:23:29 ubuntu@01a688cab5a1 dev-env ±|init|→ k get node
NAME             STATUS   ROLES           AGE     VERSION
docker-desktop   Ready    control-plane   4h18m   v1.25.4
05:23:48 ubuntu@01a688cab5a1 dev-env ±|init|→
```
<img width="399" alt="image" src="https://user-images.githubusercontent.com/32415268/221340152-3d4dc662-7fea-4836-9559-30b60f3b579b.png">

```
05:32:54 ubuntu@01a688cab5a1 dev-env ±|init|→ k get namespaces
NAME                   STATUS   AGE
default                Active   4h28m
kube-node-lease        Active   4h28m
kube-public            Active   4h28m
kube-system            Active   4h28m
kubernetes-dashboard   Active   4h13m
05:33:46 ubuntu@01a688cab5a1 dev-env ±|init|→ k get ns
NAME                   STATUS   AGE
default                Active   4h28m
kube-node-lease        Active   4h29m
kube-public            Active   4h29m
kube-system            Active   4h29m
kubernetes-dashboard   Active   4h13m
05:33:49 ubuntu@01a688cab5a1 dev-env ±|init|→ k get pods
No resources found in default namespace.
05:34:37 ubuntu@01a688cab5a1 dev-env ±|init|→
05:34:37 ubuntu@01a688cab5a1 dev-env ±|init|→ k run nginx --image=nginx
pod/nginx created
05:35:53 ubuntu@01a688cab5a1 dev-env ±|init|→ k get pods -o wide
NAME    READY   STATUS    RESTARTS   AGE   IP         NODE             NOMINATED NODE   READINESS GATES
nginx   1/1     Running   0          9s    10.1.0.8   docker-desktop   <none>           <none>
05:36:02 ubuntu@01a688cab5a1 dev-env ±|init|→
05:36:02 ubuntu@01a688cab5a1 dev-env ±|init|→ ping 10.1.0.8
PING 10.1.0.8 (10.1.0.8) 56(84) bytes of data.
64 bytes from 10.1.0.8: icmp_seq=1 ttl=63 time=0.303 ms
64 bytes from 10.1.0.8: icmp_seq=2 ttl=63 time=0.187 ms
64 bytes from 10.1.0.8: icmp_seq=3 ttl=63 time=0.184 ms
64 bytes from 10.1.0.8: icmp_seq=4 ttl=63 time=0.188 ms
64 bytes from 10.1.0.8: icmp_seq=5 ttl=63 time=0.168 ms
^C
--- 10.1.0.8 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4135ms
rtt min/avg/max/mdev = 0.168/0.206/0.303/0.049 ms
05:36:36 ubuntu@01a688cab5a1 dev-env ±|init|→
05:36:36 ubuntu@01a688cab5a1 dev-env ±|init|→ curl http://10.1.0.8
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
05:37:09 ubuntu@01a688cab5a1 dev-env ±|init|→
```
