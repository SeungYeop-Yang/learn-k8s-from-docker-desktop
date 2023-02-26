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
<img width="555" alt="image" src="https://user-images.githubusercontent.com/32415268/221424875-963615aa-a774-4dcd-8da4-4f3be84e0607.png">

# Ingress Services

failed on testing ingress....

```
05:02:08 ubuntu@86358bd60c1e dev-env ±|chap09 ✗|→ kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.41.2/deploy/static/provider/cloud/deploy.yaml
namespace/ingress-nginx created
serviceaccount/ingress-nginx created
configmap/ingress-nginx-controller created
clusterrole.rbac.authorization.k8s.io/ingress-nginx created
clusterrolebinding.rbac.authorization.k8s.io/ingress-nginx created
role.rbac.authorization.k8s.io/ingress-nginx created
rolebinding.rbac.authorization.k8s.io/ingress-nginx created
service/ingress-nginx-controller-admission created
service/ingress-nginx-controller created
deployment.apps/ingress-nginx-controller created
validatingwebhookconfiguration.admissionregistration.k8s.io/ingress-nginx-admission created
serviceaccount/ingress-nginx-admission created
clusterrole.rbac.authorization.k8s.io/ingress-nginx-admission created
clusterrolebinding.rbac.authorization.k8s.io/ingress-nginx-admission created
role.rbac.authorization.k8s.io/ingress-nginx-admission created
rolebinding.rbac.authorization.k8s.io/ingress-nginx-admission created
job.batch/ingress-nginx-admission-create created
job.batch/ingress-nginx-admission-patch created
05:05:38 ubuntu@86358bd60c1e dev-env ±|chap09 ✗|→
05:05:40 ubuntu@86358bd60c1e dev-env ±|chap09 ✗|→
05:05:40 ubuntu@86358bd60c1e dev-env ±|chap09 ✗|→
05:05:40 ubuntu@86358bd60c1e dev-env ±|chap09 ✗|→ k -n ingress-nginx get deploy
NAME                       READY   UP-TO-DATE   AVAILABLE   AGE
ingress-nginx-controller   0/1     1            0           12s
05:05:49 ubuntu@86358bd60c1e dev-env ±|chap09 ✗|→
05:15:12 ubuntu@86358bd60c1e dev-env ±|chap09 ✗|→ kubectl delete -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.41.2/deploy/static/provider/cloud/deploy.yaml
namespace "ingress-nginx" deleted
serviceaccount "ingress-nginx" deleted
configmap "ingress-nginx-controller" deleted
clusterrole.rbac.authorization.k8s.io "ingress-nginx" deleted
clusterrolebinding.rbac.authorization.k8s.io "ingress-nginx" deleted
role.rbac.authorization.k8s.io "ingress-nginx" deleted
rolebinding.rbac.authorization.k8s.io "ingress-nginx" deleted
service "ingress-nginx-controller-admission" deleted
service "ingress-nginx-controller" deleted
deployment.apps "ingress-nginx-controller" deleted
validatingwebhookconfiguration.admissionregistration.k8s.io "ingress-nginx-admission" deleted
serviceaccount "ingress-nginx-admission" deleted
clusterrole.rbac.authorization.k8s.io "ingress-nginx-admission" deleted
clusterrolebinding.rbac.authorization.k8s.io "ingress-nginx-admission" deleted
role.rbac.authorization.k8s.io "ingress-nginx-admission" deleted
rolebinding.rbac.authorization.k8s.io "ingress-nginx-admission" deleted
job.batch "ingress-nginx-admission-create" deleted
job.batch "ingress-nginx-admission-patch" deleted
05:15:57 ubuntu@86358bd60c1e dev-env ±|chap09 ✗|→ helm upgrade --install ingress-nginx ingress-nginx   --repo https://kubernetes.github.io/ingress-nginx   --namespace ingress-nginx --create-namespace
Release "ingress-nginx" does not exist. Installing it now.
NAME: ingress-nginx
LAST DEPLOYED: Sun Feb 26 17:16:00 2023
NAMESPACE: ingress-nginx
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The ingress-nginx controller has been installed.
It may take a few minutes for the LoadBalancer IP to be available.
You can watch the status by running 'kubectl --namespace ingress-nginx get services -o wide -w ingress-nginx-controller'

An example Ingress that makes use of the controller:
  apiVersion: networking.k8s.io/v1
  kind: Ingress
  metadata:
    name: example
    namespace: foo
  spec:
    ingressClassName: nginx
    rules:
      - host: www.example.com
        http:
          paths:
            - pathType: Prefix
              backend:
                service:
                  name: exampleService
                  port:
                    number: 80
              path: /
    # This section is only required if TLS is to be enabled for the Ingress
    tls:
      - hosts:
        - www.example.com
        secretName: example-tls

If TLS is enabled for the Ingress, a Secret containing the certificate and key must also be provided:

  apiVersion: v1
  kind: Secret
  metadata:
    name: example-tls
    namespace: foo
  data:
    tls.crt: <base64 encoded cert>
    tls.key: <base64 encoded key>
  type: kubernetes.io/tls
05:16:15 ubuntu@86358bd60c1e dev-env ±|chap09 ✗|→
05:16:15 ubuntu@86358bd60c1e dev-env ±|chap09 ✗|→ kubectl get pods --namespace=ingress-nginx
NAME                                        READY   STATUS    RESTARTS   AGE
ingress-nginx-controller-6b94c75599-jvf74   1/1     Running   0          51s
05:17:01 ubuntu@86358bd60c1e dev-env ±|chap09 ✗|→

```
