# Persistent Storage

```
09:09:40 ubuntu@7d41300c15fe dev-env ±|chap15 ✗|→ k apply -f chap15/pv.yaml
persistentvolume/manual created
09:12:09 ubuntu@7d41300c15fe dev-env ±|chap15 ✗|→ k apply -f chap15/pvc.yaml
persistentvolumeclaim/manual created
09:12:38 ubuntu@7d41300c15fe dev-env ±|chap15 ✗|→ k get pv manual
NAME     CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM            STORAGECLASS   REASON   AGE
manual   100Mi      RWO            Retain           Bound    default/manual                           110s
09:13:59 ubuntu@7d41300c15fe dev-env ±|chap15 ✗|→ k get pvc manual
NAME     STATUS   VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   AGE
manual   Bound    manual   100Mi      RWO            manual         102s
09:14:20 ubuntu@7d41300c15fe dev-env ±|chap15 ✗|→

```

```
09:37:43 ubuntu@7d41300c15fe dev-env ±|chap15 ✗|→ curl -v http://10.1.0.55
*   Trying 10.1.0.55:80...
* Connected to 10.1.0.55 (10.1.0.55) port 80 (#0)
> GET / HTTP/1.1
> Host: 10.1.0.55
> User-Agent: curl/7.81.0
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 403 Forbidden
< Server: nginx/1.23.3
< Date: Sun, 26 Feb 2023 21:37:52 GMT
< Content-Type: text/html
< Content-Length: 153
< Connection: keep-alive
<
<html>
<head><title>403 Forbidden</title></head>
<body>
<center><h1>403 Forbidden</h1></center>
<hr><center>nginx/1.23.3</center>
</body>
</html>
* Connection #0 to host 10.1.0.55 left intact
09:37:52 ubuntu@7d41300c15fe dev-env ±|chap15 ✗|→ k exec -it pod/nginx-557dff96d-gd7sh
error: you must specify at least one command for the container
09:38:18 ubuntu@7d41300c15fe dev-env ±|chap15 ✗|→ k exec -it pod/nginx-557dff96d-gd7sh /bin/sh
kubectl exec [POD] [COMMAND] is DEPRECATED and will be removed in a future version. Use kubectl exec [POD] -- [COMMAND] instead.
#
#
#
# ls /usr/share/nginx/html
# echo "<h1>Hello</h1>" > /usr/share/nginx/html/index.html
# ls /usr/share/nginx/html
index.html
# exit
09:40:07 ubuntu@7d41300c15fe dev-env ±|chap15 ✗|→ curl -v http://10.1.0.55
*   Trying 10.1.0.55:80...
* Connected to 10.1.0.55 (10.1.0.55) port 80 (#0)
> GET / HTTP/1.1
> Host: 10.1.0.55
> User-Agent: curl/7.81.0
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
< Server: nginx/1.23.3
< Date: Sun, 26 Feb 2023 21:40:09 GMT
< Content-Type: text/html
< Content-Length: 15
< Last-Modified: Sun, 26 Feb 2023 21:39:50 GMT
< Connection: keep-alive
< ETag: "63fbd1a6-f"
< Accept-Ranges: bytes
<
<h1>Hello</h1>
* Connection #0 to host 10.1.0.55 left intact
09:40:09 ubuntu@7d41300c15fe dev-env ±|chap15 ✗|→
```
From the host:
```
Seungs-MacBook-Air-M1:learning_k8s seungyeop$ cat ~/Download/data/index.html
<h1>Hello</h1>
Seungs-MacBook-Air-M1:learning_k8s seungyeop$

```

```
10:25:45 ubuntu@7d41300c15fe dev-env ±|chap15 ✗|→ k apply -f chap15/pv-rwx.yaml
persistentvolume/manual created
10:26:35 ubuntu@7d41300c15fe dev-env ±|chap15 ✗|→ k get pv
NAME     CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM            STORAGECLASS   REASON   AGE
manual   100Mi      RWX            Retain           Available   default/manual                           7s
10:26:43 ubuntu@7d41300c15fe dev-env ±|chap15 ✗|→ k apply -f chap15/pvc-rwx.yaml
persistentvolumeclaim/manual created
10:26:57 ubuntu@7d41300c15fe dev-env ±|chap15 ✗|→ k get pvc
NAME     STATUS   VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   AGE
manual   Bound    manual   100Mi      RWX            manual         5s
10:27:02 ubuntu@7d41300c15fe dev-env ±|chap15 ✗|→ k apply -f chap15/nginx.yaml
deployment.apps/nginx created
10:27:19 ubuntu@7d41300c15fe dev-env ±|chap15 ✗|→ k get pod
NAME                    READY   STATUS    RESTARTS   AGE
nginx-557dff96d-fmn85   1/1     Running   0          5s
10:27:24 ubuntu@7d41300c15fe dev-env ±|chap15 ✗|→ k applyy -f chap15/ngi
nginx-rwx.yaml  nginx.yaml
10:27:24 ubuntu@7d41300c15fe dev-env ±|chap15 ✗|→ k applyy -f chap15/nginx-rwx.yaml
error: unknown command "applyy" for "kubectl"

Did you mean this?
        apply
10:28:25 ubuntu@7d41300c15fe dev-env ±|chap15 ✗|→ k apply -f chap15/nginx-rwx.yaml
deployment.apps/nginx configured
10:28:34 ubuntu@7d41300c15fe dev-env ±|chap15 ✗|→ k get deployment
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   3/3     3            3           82s
10:28:41 ubuntu@7d41300c15fe dev-env ±|chap15 ✗|→ k get pods
NAME                    READY   STATUS    RESTARTS   AGE
nginx-557dff96d-fmn85   1/1     Running   0          91s
nginx-557dff96d-ts7zr   1/1     Running   0          16s
nginx-557dff96d-ztf29   1/1     Running   0          16s
10:28:50 ubuntu@7d41300c15fe dev-env ±|chap15 ✗|→ k get pods -o wide
NAME                    READY   STATUS    RESTARTS   AGE    IP          NODE             NOMINATED NODE   READINESS GATES
nginx-557dff96d-fmn85   1/1     Running   0          100s   10.1.0.56   docker-desktop   <none>           <none>
nginx-557dff96d-ts7zr   1/1     Running   0          25s    10.1.0.58   docker-desktop   <none>           <none>
nginx-557dff96d-ztf29   1/1     Running   0          25s    10.1.0.57   docker-desktop   <none>           <none>
10:28:59 ubuntu@7d41300c15fe dev-env ±|chap15 ✗|→ curl -v http://10.1.0.56
*   Trying 10.1.0.56:80...
* Connected to 10.1.0.56 (10.1.0.56) port 80 (#0)
> GET / HTTP/1.1
> Host: 10.1.0.56
> User-Agent: curl/7.81.0
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
< Server: nginx/1.23.3
< Date: Sun, 26 Feb 2023 22:29:11 GMT
< Content-Type: text/html
< Content-Length: 15
< Last-Modified: Sun, 26 Feb 2023 21:39:50 GMT
< Connection: keep-alive
< ETag: "63fbd1a6-f"
< Accept-Ranges: bytes
<
<h1>Hello</h1>
* Connection #0 to host 10.1.0.56 left intact
10:29:11 ubuntu@7d41300c15fe dev-env ±|chap15 ✗|→ curl -v http://10.1.0.57
*   Trying 10.1.0.57:80...
* Connected to 10.1.0.57 (10.1.0.57) port 80 (#0)
> GET / HTTP/1.1
> Host: 10.1.0.57
> User-Agent: curl/7.81.0
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
< Server: nginx/1.23.3
< Date: Sun, 26 Feb 2023 22:29:17 GMT
< Content-Type: text/html
< Content-Length: 15
< Last-Modified: Sun, 26 Feb 2023 21:39:50 GMT
< Connection: keep-alive
< ETag: "63fbd1a6-f"
< Accept-Ranges: bytes
<
<h1>Hello</h1>
* Connection #0 to host 10.1.0.57 left intact
10:29:17 ubuntu@7d41300c15fe dev-env ±|chap15 ✗|→ curl -v http://10.1.0.58
*   Trying 10.1.0.58:80...
* Connected to 10.1.0.58 (10.1.0.58) port 80 (#0)
> GET / HTTP/1.1
> Host: 10.1.0.58
> User-Agent: curl/7.81.0
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
< Server: nginx/1.23.3
< Date: Sun, 26 Feb 2023 22:29:20 GMT
< Content-Type: text/html
< Content-Length: 15
< Last-Modified: Sun, 26 Feb 2023 21:39:50 GMT
< Connection: keep-alive
< ETag: "63fbd1a6-f"
< Accept-Ranges: bytes
<
<h1>Hello</h1>
* Connection #0 to host 10.1.0.58 left intact
10:29:20 ubuntu@7d41300c15fe dev-env ±|chap15 ✗|→
```
