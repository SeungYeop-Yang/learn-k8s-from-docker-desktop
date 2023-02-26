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

Background:
Kubernetes has evolved its controller resources over time. The first type of controller, RelicationController,
provided only basic functionality. It was replaced by the RelicaSet, which has improvements in how it identifies
which Pods to manage.
Part of the reason to replace RelicationControllers with ReplicaSets is that ReplicationControllers were
becoming more and more complicated, making the code difficult to maintain. The new approach splits up
controller responsibility between ReplicaSets and Deployments. ReplicaSets are responsible for basic Pod
management, including monitoring Pod status and performing failover. Deployments are responsible for
tracking changes to the Pod template caused by configuration changes or container image updates.
Deployments and ReplicaSets work together, but the Deployment creates its own ReplicaSet, so we usually need to
interact only with Deployments.

-The Book of Kubernetes, Alan Hohn, 2022, No Starch Press

```
08:09:14 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→ k apply -f nginx-deploy.yaml
deployment.apps/nginx created
08:09:30 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→ k get deployment nginx
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   3/3     3            3           8s
08:09:38 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→ k get pod nginx
Error from server (NotFound): pods "nginx" not found
08:09:45 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→ k get pod
NAME                     READY   STATUS    RESTARTS   AGE
nginx-6bdbc5b656-fmgmk   1/1     Running   0          20s
nginx-6bdbc5b656-n76gt   1/1     Running   0          20s
nginx-6bdbc5b656-x7jl2   1/1     Running   0          20s
08:09:50 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→ k get replicasets
NAME               DESIRED   CURRENT   READY   AGE
nginx-6bdbc5b656   3         3         3       63s
08:10:33 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→ k describe deployment nginx
Name:                   nginx
Namespace:              default
CreationTimestamp:      Sat, 25 Feb 2023 20:09:30 +0000
Labels:                 <none>
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=nginx
Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=nginx
  Containers:
   nginx:
    Image:      nginx
    Port:       <none>
    Host Port:  <none>
    Requests:
      cpu:        100m
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   nginx-6bdbc5b656 (3/3 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  104s  deployment-controller  Scaled up replica set nginx-6bdbc5b656 to 3
08:11:14 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→ k get --help
    -l, --selector='':
        Selector (label query) to filter on, supports '=', '==', and '!='.(e.g. -l key1=value1,key2=value2). Matching
        objects must satisfy all of the specified label constraints.
08:21:12 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→ k get all -l app=nginx
NAME                         READY   STATUS    RESTARTS   AGE
pod/nginx-6bdbc5b656-fmgmk   1/1     Running   0          12m
pod/nginx-6bdbc5b656-n76gt   1/1     Running   0          12m
pod/nginx-6bdbc5b656-x7jl2   1/1     Running   0          12m

NAME                               DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-6bdbc5b656   3         3         3       12m
08:22:22 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→
08:25:12 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→ k delete pod/nginx-6bdbc5b656-fmgmk
pod "nginx-6bdbc5b656-fmgmk" deleted
08:25:31 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→ k get all -l app=nginx
NAME                         READY   STATUS    RESTARTS   AGE
pod/nginx-6bdbc5b656-gkln8   1/1     Running   0          6s
pod/nginx-6bdbc5b656-n76gt   1/1     Running   0          16m
pod/nginx-6bdbc5b656-x7jl2   1/1     Running   0          16m

NAME                               DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-6bdbc5b656   3         3         3       16m
08:25:36 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→
08:25:36 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→ k scale --replicas=4 deployment nginx
deployment.apps/nginx scaled
08:26:23 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→ k get all -l app=nginx
NAME                         READY   STATUS    RESTARTS   AGE
pod/nginx-6bdbc5b656-gkln8   1/1     Running   0          57s
pod/nginx-6bdbc5b656-n76gt   1/1     Running   0          16m
pod/nginx-6bdbc5b656-qbl2n   1/1     Running   0          4s
pod/nginx-6bdbc5b656-x7jl2   1/1     Running   0          16m

NAME                               DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-6bdbc5b656   4         4         4       16m
08:26:27 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→
08:27:51 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→ k scale --replicas=2 deployment nginx
deployment.apps/nginx scaled
08:28:05 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→ k get all -l app=nginx
NAME                         READY   STATUS    RESTARTS   AGE
pod/nginx-6bdbc5b656-n76gt   1/1     Running   0          18m
pod/nginx-6bdbc5b656-x7jl2   1/1     Running   0          18m

NAME                               DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-6bdbc5b656   2         2         2       18m
08:28:15 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→
08:31:29 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→ k delete -f nginx-deploy.yaml
deployment.apps "nginx" deleted
08:31:38 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→ k get deployment
No resources found in default namespace.
08:31:45 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→ k get pod
No resources found in default namespace.
08:31:50 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→ k get replicasets
No resources found in default namespace.
08:31:55 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→

04:46:34 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→ k apply -f sleep-job.yaml
job.batch/sleep created
04:46:48 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→ k get job
NAME    COMPLETIONS   DURATION   AGE
sleep   0/1           7s         7s
04:46:55 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→ k get pods
NAME                     READY   STATUS    RESTARTS   AGE
nginx-6bdbc5b656-7v6bb   1/1     Running   0          8h
nginx-6bdbc5b656-gfh8t   1/1     Running   0          8h
nginx-6bdbc5b656-swhqr   1/1     Running   0          8h
sleep-m9x84              1/1     Running   0          14s
04:47:02 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→ k delete -f nginx-scaler.yaml
horizontalpodautoscaler.autoscaling "nginx" deleted
04:47:27 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→ k delete -f nginx-deploy.yaml
deployment.apps "nginx" deleted
04:47:40 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→ k get pod
NAME          READY   STATUS      RESTARTS   AGE
sleep-m9x84   0/1     Completed   0          55s
04:46:34 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→ k apply -f sleep-job.yaml
job.batch/sleep created
04:46:48 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→ k get job
NAME    COMPLETIONS   DURATION   AGE
sleep   0/1           7s         7s
04:46:55 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→ k get pods
NAME                     READY   STATUS    RESTARTS   AGE
nginx-6bdbc5b656-7v6bb   1/1     Running   0          8h
nginx-6bdbc5b656-gfh8t   1/1     Running   0          8h
nginx-6bdbc5b656-swhqr   1/1     Running   0          8h
sleep-m9x84              1/1     Running   0          14s
04:47:02 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→ k delete -f ngin
nginx-deploy.yaml  nginx-pod.yaml     nginx-scaler.yaml
04:47:02 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→ k delete -f nginx-scaler.yaml
horizontalpodautoscaler.autoscaling "nginx" deleted
04:47:27 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→ k delete -f ngi
nginx-deploy.yaml  nginx-pod.yaml     nginx-scaler.yaml
04:47:27 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→ k delete -f nginx-deploy.yaml
deployment.apps "nginx" deleted
04:47:40 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→ k get pod
NAME          READY   STATUS      RESTARTS   AGE
sleep-m9x84   0/1     Completed   0          55s
04:56:39 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→ k delete -f sleep-cronjob.yaml
cronjob.batch "sleep" deleted
04:56:53 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→ k get cronjobs
No resources found in default namespace.
04:56:56 ubuntu@01a688cab5a1 chap07 ±|chap07 ✗|→
```
