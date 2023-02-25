```
06:47:07 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→ k apply -f nginx-pod.yaml
pod/nginx created
06:47:15 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→ k get pod
NAME    READY   STATUS    RESTARTS   AGE
nginx   1/1     Running   0          3s
06:47:18 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→ watch kubectl get pod nginx -o wide
06:53:09 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→ k describe pod nginx
Name:             nginx
Namespace:        default
Priority:         0
Service Account:  default
Node:             docker-desktop/192.168.65.4
Start Time:       Sat, 25 Feb 2023 18:47:15 +0000
Labels:           <none>
Annotations:      <none>
Status:           Running
IP:               10.1.0.9
IPs:
  IP:  10.1.0.9
Containers:
  nginx:
    Container ID:   docker://dc1fb92d687e83b41085f33b8464caa59f466b5f1330d82d7afe94438a761223
    Image:          nginx
    Image ID:       docker-pullable://nginx@sha256:6650513efd1d27c1f8a5351cbd33edf85cc7e0d9d0fcb4ffb23d8fa89b601ba8
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Sat, 25 Feb 2023 18:47:16 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-82wb8 (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  kube-api-access-82wb8:
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
  Type    Reason     Age    From               Message
  ----    ------     ----   ----               -------
  Normal  Scheduled  9m52s  default-scheduler  Successfully assigned default/nginx to docker-desktop
  Normal  Pulling    9m52s  kubelet            Pulling image "nginx"
  Normal  Pulled     9m51s  kubelet            Successfully pulled image "nginx" in 845.327959ms
  Normal  Created    9m51s  kubelet            Created container nginx
  Normal  Started    9m51s  kubelet            Started container nginx
06:57:07 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→
06:57:07 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→ k logs nginx
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2023/02/25 18:47:16 [notice] 1#1: using the "epoll" event method
2023/02/25 18:47:16 [notice] 1#1: nginx/1.23.3
2023/02/25 18:47:16 [notice] 1#1: built by gcc 10.2.1 20210110 (Debian 10.2.1-6)
2023/02/25 18:47:16 [notice] 1#1: OS: Linux 5.15.49-linuxkit
2023/02/25 18:47:16 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2023/02/25 18:47:16 [notice] 1#1: start worker processes
2023/02/25 18:47:16 [notice] 1#1: start worker process 29
2023/02/25 18:47:16 [notice] 1#1: start worker process 30
2023/02/25 18:47:16 [notice] 1#1: start worker process 31
2023/02/25 18:47:16 [notice] 1#1: start worker process 32
07:48:18 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→
07:48:18 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→ k delete -f nginx-pod.yaml
pod "nginx" deleted
07:50:15 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→ k logs nginx
Error from server (NotFound): pods "nginx" not found
07:50:19 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→
```

To run a container, we need a Pod, but that doesn't mean we generally wanto to create the Pod directly.
When we create a Pod directly, we don't get all of the scalability and failover that Kubernetes offers,
because Kubernetes will run only one instance of the Pod. This Pod will be allocated to a node only on
creation, with no re-allocation even if the node fails.

We instead need to create a controller to manage the Pod for us. :
There are many, we start with `the Deployment`.

```

```
