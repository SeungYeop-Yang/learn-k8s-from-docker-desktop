# Service and Ingress

```
04:14:56 ubuntu@86358bd60c1e dev-env ±|chap09 ✗|→ k apply -f chap09/nginx-deploy.yaml
deployment.apps/nginx created
04:15:01 ubuntu@86358bd60c1e dev-env ±|chap09 ✗|→ k get pods -o wide
NAME                    READY   STATUS    RESTARTS   AGE   IP          NODE             NOMINATED NODE   READINESS GATES
nginx-76d6c9b8c-9dshg   1/1     Running   0          10s   10.1.0.28   docker-desktop   <none>           <none>
nginx-76d6c9b8c-9jcdr   1/1     Running   0          10s   10.1.0.26   docker-desktop   <none>           <none>
nginx-76d6c9b8c-k9mcl   1/1     Running   0          10s   10.1.0.27   docker-desktop   <none>           <none>
nginx-76d6c9b8c-lfdcg   1/1     Running   0          10s   10.1.0.24   docker-desktop   <none>           <none>
nginx-76d6c9b8c-wzwdl   1/1     Running   0          10s   10.1.0.25   docker-desktop   <none>           <none>
04:15:11 ubuntu@86358bd60c1e dev-env ±|chap09 ✗|→ curl -v http://10.1.0.28
*   Trying 10.1.0.28:80...
* Connected to 10.1.0.28 (10.1.0.28) port 80 (#0)
> GET / HTTP/1.1
> Host: 10.1.0.28
> User-Agent: curl/7.81.0
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
< Server: nginx/1.23.3
< Date: Sun, 26 Feb 2023 16:16:11 GMT
< Content-Type: text/html
< Content-Length: 615
< Last-Modified: Tue, 13 Dec 2022 15:53:53 GMT
< Connection: keep-alive
< ETag: "6398a011-267"
< Accept-Ranges: bytes
<
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
* Connection #0 to host 10.1.0.28 left intact
04:16:11 ubuntu@86358bd60c1e de
04:16:11 ubuntu@86358bd60c1e dev-env ±|chap09 ✗|→
```

- We need to have a well-known name that clients can use to find a server.
- we need a consistent IP address so that when a cliet has identified
a server, it can continue to use the same address for connections
even as Pod instances come and go.

## Service

```
04:24:44 ubuntu@86358bd60c1e dev-env ±|chap09 ✗|→ k apply -f chap09/pod.yaml
pod/pod created
04:26:31 ubuntu@86358bd60c1e dev-env ±|chap09 ✗|→ k get pods
NAME                    READY   STATUS              RESTARTS   AGE
nginx-76d6c9b8c-6jqmd   1/1     Running             0          4m56s
nginx-76d6c9b8c-gtdb2   1/1     Running             0          4m57s
nginx-76d6c9b8c-hb8hs   1/1     Running             0          4m57s
nginx-76d6c9b8c-rkkqq   1/1     Running             0          4m57s
nginx-76d6c9b8c-zj7c7   1/1     Running             0          4m56s
pod                     0/1     ContainerCreating   0          4s
04:26:35 ubuntu@86358bd60c1e dev-env ±|chap09 ✗|→ k get pods
NAME                    READY   STATUS    RESTARTS   AGE
nginx-76d6c9b8c-6jqmd   1/1     Running   0          5m1s
nginx-76d6c9b8c-gtdb2   1/1     Running   0          5m2s
nginx-76d6c9b8c-hb8hs   1/1     Running   0          5m2s
nginx-76d6c9b8c-rkkqq   1/1     Running   0          5m2s
nginx-76d6c9b8c-zj7c7   1/1     Running   0          5m1s
pod                     1/1     Running   0          9s
04:33:13 ubuntu@86358bd60c1e dev-env ±|chap09 ✗|→ k exec -ti pod -- wget -O - http://nginx
Connecting to nginx (10.105.78.8:80)
writing to stdout
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
-                    100% |********************************|   615  0:00:00 ETA
written to stdout
04:33:26 ubuntu@86358bd60c1e dev-env ±|chap09 ✗|→

```

# Name Resolution and Namespaces

DNS names in a Kubernetes cluster are based on the Namespace as well
as the cluster domain. Because the Pod, pod, is in the default Namespace,
it has been configured with a search path of `default.svc.cluster.local`
as the first entry in the list, so it will search the default Namespace
first when looking for Services.
```
04:33:26 ubuntu@86358bd60c1e dev-env ±|chap09 ✗|→ k exec -ti pod -- wget -O - http://nginx.default.svc
Connecting to nginx.default.svc (10.105.78.8:80)
writing to stdout
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
-                    100% |********************************|   615  0:00:00 ETA
written to stdout
04:35:14 ubuntu@86358bd60c1e dev-env ±|chap09 ✗|→
04:35:14 ubuntu@86358bd60c1e dev-env ±|chap09 ✗|→ k exec -ti pod -- cat /etc/resolv.conf
nameserver 10.96.0.10
search default.svc.cluster.local svc.cluster.local cluster.local
options ndots:5
04:39:26 ubuntu@86358bd60c1e dev-env ±|chap09 ✗|→
```

The ndots entry tells the hostname resolver that when it sees a hostname
with four or fewer dots, it should try appending the various search
domains prior to performing an absolute search without any domain appended.

# External Networking

```
04:52:47 ubuntu@86358bd60c1e dev-env ±|chap09 ✗|→ k apply -f chap09/nginx-nodeport.yaml
service/nginx created
04:52:58 ubuntu@86358bd60c1e dev-env ±|chap09 ✗|→ k describe service/nginx
Name:                     nginx
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=nginx
Type:                     NodePort
IP Family Policy:         SingleStack
IP Families:              IPv4
IP:                       10.103.28.72
IPs:                      10.103.28.72
LoadBalancer Ingress:     localhost
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  30237/TCP
Endpoints:                10.1.0.29:80,10.1.0.30:80,10.1.0.31:80 + 2 more...
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
04:53:10 ubuntu@86358bd60c1e dev-env ±|chap09 ✗|→
Seungs-MacBook-Air-M1:~ seungyeop$ wget -O - http://localhost:30237
--2023-02-26 10:56:52--  http://localhost:30237/
Resolving localhost (localhost)... ::1, 127.0.0.1
Connecting to localhost (localhost)|::1|:30237... connected.
HTTP request sent, awaiting response... 200 OK
Length: 615 [text/html]
Saving to: ‘STDOUT’

-                     0%[                    ]       0  --.-KB/s               <!DOCTYPE html>
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
-                   100%[===================>]     615  --.-KB/s    in 0s

2023-02-26 10:56:52 (196 MB/s) - written to stdout [615/615]

Seungs-MacBook-Air-M1:~ seungyeop$
```
