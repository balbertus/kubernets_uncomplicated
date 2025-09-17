This is based on LinuxTips - Descomplicando o Kubernetes

Day - 1 ! Install Kubectl and Kind to Starting play with Clusters

Follow Documentation page https://kubernetes.io/docs/tasks/tools/ .I'm using Linux but We can run on Mac and Windows as well

https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/#install-kubectl-binary-with-curl-on-linux

$ kubectl version
Client Version: v1.33.2
Kustomize Version: v5.6.0
Server Version: v1.33.1

Kind 
https://kind.sigs.k8s.io/docs/user/quick-start/#installation
$ kind version
kind v0.29.0 go1.24.2 linux/amd64

#########################CREATE A CLUSTER################################################
$ kind create cluster # Default cluster context name is `kind`.
...
kind create cluster --name kind-2

When you list your kind clusters, you will see something like the following:

kind get clusters
kind
kind-2
In order to interact with a specific cluster, you only need to specify the cluster name as a context in kubectl:

kubectl cluster-info --context kind-kind
kubectl cluster-info --context kind-kind-2
#################################Hadling Cluster with kubectl##############################
$ kubectl get node -n kube-system
NAME                 STATUS   ROLES           AGE     VERSION
kind-control-plane   Ready    control-plane   2m55s   v1.33.1
kind-worker          Ready    <none>          2m39s   v1.33.1
kind-worker2         Ready    <none>          2m39s   v1.33.1
$ kubectl get node -n kube-system -o wide
NAME                 STATUS   ROLES           AGE     VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                         KERNEL-VERSION     CONTAINER-RUNTIME
kind-control-plane   Ready    control-plane   2m59s   v1.33.1   172.18.0.3    <none>        Debian GNU/Linux 12 (bookworm)   6.8.0-63-generic   containerd://2.1.1
kind-worker          Ready    <none>          2m43s   v1.33.1   172.18.0.4    <none>        Debian GNU/Linux 12 (bookworm)   6.8.0-63-generic   containerd://2.1.1
kind-worker2         Ready    <none>          2m43s   v1.33.1   172.18.0.2    <none>        Debian GNU/Linux 12 (bookworm)   6.8.0-63-generic   containerd://2.1.1

$ kubectl get pods -o wide -n kube-system # -n means namespace
NAME                                         READY   STATUS    RESTARTS   AGE     IP           NODE                 NOMINATED NODE   READINESS GATES
coredns-674b8bbfcf-9l8z4                     1/1     Running   0          4m12s   10.244.0.4   kind-control-plane   <none>           <none>
coredns-674b8bbfcf-nvh4b                     1/1     Running   0          4m12s   10.244.0.2   kind-control-plane   <none>           <none>
etcd-kind-control-plane                      1/1     Running   0          4m17s   172.18.0.3   kind-control-plane   <none>           <none>
kindnet-fngnx                                1/1     Running   0          4m5s    172.18.0.2   kind-worker2         <none>           <none>
kindnet-hnwh8                                1/1     Running   0          4m5s    172.18.0.4   kind-worker          <none>           <none>
kindnet-wpdvn                                1/1     Running   0          4m13s   172.18.0.3   kind-control-plane   <none>           <none>
kube-apiserver-kind-control-plane            1/1     Running   0          4m17s   172.18.0.3   kind-control-plane   <none>           <none>
kube-controller-manager-kind-control-plane   1/1     Running   0          4m17s   172.18.0.3   kind-control-plane   <none>           <none>
kube-proxy-67g7w                             1/1     Running   0          4m5s    172.18.0.2   kind-worker2         <none>           <none>
kube-proxy-bvvhs                             1/1     Running   0          4m5s    172.18.0.4   kind-worker          <none>           <none>
kube-proxy-vpwlt                             1/1     Running   0          4m13s   172.18.0.3   kind-control-plane   <none>           <none>
kube-scheduler-kind-control-plane            1/1     Running   0          4m19s   172.18.0.3   kind-control-plane   <none>           <none>

###########################get ignoring namespace - get by resources########################################################################
$ kubectl get pods -A
NAMESPACE            NAME                                         READY   STATUS    RESTARTS   AGE
kube-system          coredns-674b8bbfcf-9l8z4                     1/1     Running   0          7m11s
kube-system          coredns-674b8bbfcf-nvh4b                     1/1     Running   0          7m11s
kube-system          etcd-kind-control-plane                      1/1     Running   0          7m16s
kube-system          kindnet-fngnx                                1/1     Running   0          7m4s
kube-system          kindnet-hnwh8                                1/1     Running   0          7m4s
kube-system          kindnet-wpdvn                                1/1     Running   0          7m12s
kube-system          kube-apiserver-kind-control-plane            1/1     Running   0          7m16s
kube-system          kube-controller-manager-kind-control-plane   1/1     Running   0          7m16s
kube-system          kube-proxy-67g7w                             1/1     Running   0          7m4s
kube-system          kube-proxy-bvvhs                             1/1     Running   0          7m4s
kube-system          kube-proxy-vpwlt                             1/1     Running   0          7m12s
kube-system          kube-scheduler-kind-control-plane            1/1     Running   0          7m18s
local-path-storage   local-path-provisioner-7dc846544d-sgg6v      1/1     Running   0          7m11s
$ kubectl get deployment -A
NAMESPACE            NAME                     READY   UP-TO-DATE   AVAILABLE   AGE
kube-system          coredns                  2/2     2            2           8m8s
local-path-storage   local-path-provisioner   1/1     1            1           8m5s
$ kubectl get service -A
NAMESPACE     NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)                  AGE
default       kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP                  8m17s
kube-system   kube-dns     ClusterIP   10.96.0.10   <none>        53/UDP,53/TCP,9153/TCP   8m16s
$ kubectl get replicaset -A
NAMESPACE            NAME                                DESIRED   CURRENT   READY   AGE
kube-system          coredns-674b8bbfcf                  2         2         2       8m28s
local-path-storage   local-path-provisioner-7dc846544d   1         1         1       8m28s
user1@balbertus-virtual-machine:~/PICK/kubernets_uncomplicated/day-1

##################################CREATE A YAML FILE###############################################################################
$ kubectl run --image nginx --port 80 mynginx --dry-run=client -o yaml > pod.yaml
$ ls -la
total 24
drwxrwxr-x 2 user1 user1 4096 Jul 14 15:45 .
drwxrwxr-x 4 user1 user1 4096 Jul 12 11:59 ..
-rw-rw-r-- 1 user1 user1  150 Jul 12 11:57 kind-cluster.yaml
-rw-rw-r-- 1 user1 user1 6885 Jul 14 15:23 notes.txt
-rw-rw-r-- 1 user1 user1  273 Jul 14 15:45 pod.yaml
>>> TEMPLATE CREATE by --dry-run=client -o yaml<<<<
$ cat pod.yaml 
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: mynginx
  name: mynginx
spec:
  containers:
  - image: nginx
    name: mynginx
    ports:
    - containerPort: 80
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
################################APPLY by YAML file#########################################################################
$ kubectl apply -f pod.yaml 
pod/mynginx created
$ kubectl get pods
NAME      READY   STATUS              RESTARTS   AGE
mynginx   0/1     ContainerCreating   0          8s

###########################################################################################################################

DAY -2 - Hadling a manifest file and PODs 
pod yaml manifest

$ cat pod.yaml 
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: balbertus
    service: webserver
  name: balbertus
spec:
  containers:
  - image: nginx
    name: nginx
    resources: {}
  - image: httpd
    name: apache
  dnsPolicy: ClusterFirst
  restartPolicy: Always

##########Getting Containers name at a pod####################################
#kubectl get pods
#kubectl describe #"pod "pod name"'

$ kubectl get pods
NAME        READY   STATUS             RESTARTS      AGE
balbertus   1/2     CrashLoopBackOff   6 (43s ago)   7m3s
mynginx     1/1     Running            1 (80m ago)   24h
$ kubectl describe pod balbertus
Name:             balbertus
Namespace:        default
Priority:         0
Service Account:  default
Node:             kind-worker/172.18.0.3
Start Time:       Tue, 15 Jul 2025 15:49:43 -0300
Labels:           run=balbertus
                  service=webserver
Annotations:      <none>
Status:           Running
IP:               10.244.2.3
IPs:
  IP:  10.244.2.3
Containers:
  nginx:
    Container ID:   containerd://adb6918d671c1cb9bb0253995e03f65548f56a9cb876dd610d287c462a36c8b7
    Image:          nginx
    Image ID:       docker.io/library/nginx@sha256:f5c017fb33c6db484545793ffb67db51cdd7daebee472104612f73a85063f889
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Tue, 15 Jul 2025 15:50:02 -0300
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-pcfqj (ro)
  apache:
    Container ID:   containerd://81e866672a50fc08f2e8a79fd2bb0e23c64313fc665c785db61e3f32c5a760d5
    Image:          httpd
    Image ID:       docker.io/library/httpd@sha256:f84fe51ff5d35124e024f51215b443b16c939b24eae747025a515200e71c7d07
    Port:           <none>
    Host Port:      <none>
    State:          Waiting
      Reason:       CrashLoopBackOff
    Last State:     Terminated
      Reason:       Error
      Exit Code:    1
      Started:      Tue, 15 Jul 2025 15:56:03 -0300
      Finished:     Tue, 15 Jul 2025 15:56:03 -0300
    Ready:          False
    Restart Count:  6
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-pcfqj (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       False 
  ContainersReady             False 
  PodScheduled                True 
Volumes:
  kube-api-access-pcfqj:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    Optional:                false
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason     Age                    From               Message
  ----     ------     ----                   ----               -------
  Normal   Scheduled  7m19s                  default-scheduler  Successfully assigned default/balbertus to kind-worker
  Normal   Pulling    7m18s                  kubelet            Pulling image "nginx"
  Normal   Pulled     7m                     kubelet            Successfully pulled image "nginx" in 17.973s (17.973s including waiting). Image size: 72223778 bytes.
  Normal   Created    7m                     kubelet            Created container: nginx
  Normal   Started    7m                     kubelet            Started container nginx
  Normal   Pulled     6m51s                  kubelet            Successfully pulled image "httpd" in 9.435s (9.435s including waiting). Image size: 58526745 bytes.
  Normal   Pulled     6m48s                  kubelet            Successfully pulled image "httpd" in 1.409s (1.409s including waiting). Image size: 58526745 bytes.
  Normal   Pulled     6m33s                  kubelet            Successfully pulled image "httpd" in 1.465s (1.465s including waiting). Image size: 58526745 bytes.
  Normal   Pulled     6m7s                   kubelet            Successfully pulled image "httpd" in 1.379s (1.379s including waiting). Image size: 58526745 bytes.
  Normal   Created    5m13s (x5 over 6m50s)  kubelet            Created container: apache
  Normal   Started    5m13s (x5 over 6m50s)  kubelet            Started container apache
  Normal   Pulled     5m13s                  kubelet            Successfully pulled image "httpd" in 1.471s (1.471s including waiting). Image size: 58526745 bytes.
  Warning  BackOff    83s (x26 over 6m47s)   kubelet            Back-off restarting failed container apache in pod balbertus_default(67ee3db3-90c9-4df2-8d14-fc2ac788e28a)
  Normal   Pulling    61s (x7 over 7m)       kubelet            Pulling image "httpd"

##########Getting erros and Logs at pod.yaml file#############################
#kubectl logs <pod> -c <container>
$ kubectl logs balbertus -c apache
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 10.244.2.3. Set the 'ServerName' directive globally to suppress this message
(98)Address already in use: AH00072: make_sock: could not bind to address [::]:80 'essa linha mostra conflito da porta 80 para o apache já em uso pelo nginx'
(98)Address already in use: AH00072: make_sock: could not bind to address 0.0.0.0:80
no listening sockets available, shutting down
AH00015: Unable to open logs
$ kubectl logs balbertus -c nginx
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2025/07/15 18:50:02 [notice] 1#1: using the "epoll" event method
2025/07/15 18:50:02 [notice] 1#1: nginx/1.29.0
2025/07/15 18:50:02 [notice] 1#1: built by gcc 12.2.0 (Debian 12.2.0-14+deb12u1) 
2025/07/15 18:50:02 [notice] 1#1: OS: Linux 6.8.0-63-generic
2025/07/15 18:50:02 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2025/07/15 18:50:02 [notice] 1#1: start worker processes
2025/07/15 18:50:02 [notice] 1#1: start worker process 35
2025/07/15 18:50:02 [notice] 1#1: start worker process 36

###########see 1/2 instead of 2/2##############################################
$ kubectl get pods
NAME        READY   STATUS             RESTARTS       AGE
balbertus   1/2     CrashLoopBackOff   7 (3m3s ago)   14m

############runing 2 containers just one webservices###########################
$ kubectl create -f pod.yaml
pod/balbertus created
$ kubectl get pods
NAME        READY   STATUS    RESTARTS   AGE
balbertus   2/2     Running   0          9s
$ kubectl describe pod balbertus
Name:             balbertus
Namespace:        default
Priority:         0
Service Account:  default
Node:             kind-worker2/172.18.0.2
Start Time:       Tue, 15 Jul 2025 16:46:14 -0300
Labels:           run=balbertus
Annotations:      <none>
Status:           Running
IP:               10.244.1.10
IPs:
  IP:  10.244.1.10
Containers:
  nginx:
    Container ID:   containerd://2fd3a394b431d6059dbe20163dfce80ea5c9293de4007778ce1232ee84ceac12
    Image:          nginx
    Image ID:       docker.io/library/nginx@sha256:f5c017fb33c6db484545793ffb67db51cdd7daebee472104612f73a85063f889
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Tue, 15 Jul 2025 16:46:16 -0300
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-7dxf5 (ro)
  busybox:
    Container ID:  containerd://0f170349e577c6df2de4cc13fc22c8fa0dca4d103f3582e6a29ef839d13a7950
    Image:         busybox
    Image ID:      docker.io/library/busybox@sha256:f85340bf132ae937d2c2a763b8335c9bab35d6e8293f70f606b9c6178d84f42b
    Port:          <none>
    Host Port:     <none>
    Args:
      sleep
      200
    State:          Running
      Started:      Tue, 15 Jul 2025 16:46:20 -0300
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-7dxf5 (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       True 
  ContainersReady             True 
  PodScheduled                True 
Volumes:
  kube-api-access-7dxf5:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    Optional:                false
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  42s   default-scheduler  Successfully assigned default/balbertus to kind-worker2
  Normal  Pulling    42s   kubelet            Pulling image "nginx"
  Normal  Pulled     40s   kubelet            Successfully pulled image "nginx" in 1.497s (1.497s including waiting). Image size: 72223778 bytes.
  Normal  Created    40s   kubelet            Created container: nginx
  Normal  Started    40s   kubelet            Started container nginx
  Normal  Pulling    40s   kubelet            Pulling image "busybox"
  Normal  Pulled     36s   kubelet            Successfully pulled image "busybox" in 4.361s (4.361s including waiting). Image size: 2156518 bytes.
  Normal  Created    36s   kubelet            Created container: busybox
  Normal  Started    36s   kubelet            Started container busybox
user1@balbertus-virtual-machine:~/PICK/kubernets_uncomplicated/day-2$

#######################################pod limits####################################################################################################
Limits on Container inside the pods
# manifest
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: balbertus
  name: balbertus
spec:
  containers:
  - image: ubuntu
    name: ubuntu
    args:
    - sleep
    - "1200"
    resources:
      limits:
        cpu: "0.5"
        memory: "128Mi"
      requests:
        cpu: "0.3"
        memory: "64Mi"
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}

>> pod describe output

   Args:
      sleep
      1200
    State:          Running
      Started:      Wed, 16 Jul 2025 09:36:02 -0300
    Ready:          True
    Restart Count:  0
    Limits:
      cpu:     500m
      memory:  128Mi
    Requests:
      cpu:        300m
      memory:     64Mi
    Environment:  <none>

>>> connect into pod/container (exec bash) and instal stress test to validate limits <<<

    #kubectl exec -ti balbertus -- bash
    #apt-get update && apt-get install stress

root@balbertus:/# stress --vm 1 --vm-bytes 128M --timeout 10s
stress: info: [175] dispatching hogs: 0 cpu, 0 io, 1 vm, 0 hdd
command terminated with exit code 137

root@balbertus:/# stress --vm 1 --vm-bytes 60M --timeout 10s
stress: info: [181] dispatching hogs: 0 cpu, 0 io, 1 vm, 0 hdd
stress: info: [181] successful run completed in 10s
root@balbertus:/# 

DUE CPU LIMITATION

root@balbertus:/# stress --vm 1 --cpu 1 --timeout 10s
stress: info: [183] dispatching hogs: 1 cpu, 0 io, 1 vm, 0 hdd
command terminated with exit code 137
user1@balbertus-virtual-machine:~/PICK/kubernets_uncomplicated/day-2$ 

##################pod persistence############################################
empty dir concept > do not garantee in case of removal - just for restarting situations

#manifest
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: balbertus
  name: balbertus
spec:
  containers:
  - image: nginx
    name: webserver
    volumeMounts:
    - mountPath: /balbertus
      name: first-emptydir 
    resources:
      limits:
        cpu: "0.5"
        memory: "128Mi"
      requests:
        cpu: "0.3"
        memory: "64Mi"
  dnsPolicy: ClusterFirst
  restartPolicy: Always
  volumes:
  - name: first-emptydir
    emptyDir:
      sizeLimit: "256Mi"
status: {}

# describe output
  IP:  10.244.2.4
Containers:
  webserver:
    Container ID:   containerd://8f725b61e7e95e9e11707637f39ec155a90c8ca5ffa920215e77bd306f738f84
    Image:          nginx
    Image ID:       docker.io/library/nginx@sha256:f5c017fb33c6db484545793ffb67db51cdd7daebee472104612f73a85063f889
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Wed, 16 Jul 2025 10:13:49 -0300
    Ready:          True
    Restart Count:  0
    Limits:
      cpu:     500m
      memory:  128Mi
    Requests:
      cpu:        300m
      memory:     64Mi
    Environment:  <none>
    Mounts:
      /balbertus from first-emptydir (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-szsvd (ro)

$ kubectl exec -ti balbertus -- bash
root@balbertus:/# ls
balbertus  bin	boot  dev  docker-entrypoint.d	docker-entrypoint.sh  etc  home  lib  lib64  media  mnt  opt  proc  product_name  product_uuid	root  run  sbin  srv  sys  tmp	usr  var
root@balbertus:/# ls -la
total 76
drwxr-xr-x   1 root   root    4096 Jul 16 13:13 .
drwxr-xr-x   1 root   root    4096 Jul 16 13:13 ..
drwxrwxrwx   2 root   root    4096 Jul 16 13:13 balbertus
lrwxrwxrwx   1 root   root       7 Jun 30 00:00 bin -> usr/bin
drwxr-xr-x   2 root   root    4096 May  9 14:50 boot
drwxr-xr-x   5 root   root     360 Jul 16 13:13 dev
drwxr-xr-x   1 root   root    4096 Jul 15 01:14 docker-entrypoint.d
-rwxr-xr-x   1 root   root    1620 Jul 15 01:14 docker-entrypoint.sh
drwxr-xr-x   1 root   root    4096 Jul 16 13:13 etc
drwxr-xr-x   2 root   root    4096 May  9 14:50 home
lrwxrwxrwx   1 root   root       7 Jun 30 00:00 lib -> usr/lib
lrwxrwxrwx   1 root   root       9 Jun 30 00:00 lib64 -> usr/lib64
drwxr-xr-x   2 root   root    4096 Jun 30 00:00 media
drwxr-xr-x   2 root   root    4096 Jun 30 00:00 mnt
drwxr-xr-x   2 root   root    4096 Jun 30 00:00 opt
dr-xr-xr-x 429 nobody nogroup    0 Jul 16 13:13 proc
-rw-r--r--   1 root   root       5 Jul 16 13:13 product_name
-rw-r--r--   1 root   root      37 Jul 16 13:13 product_uuid
drwx------   2 root   root    4096 Jun 30 00:00 root
drwxr-xr-x   1 root   root    4096 Jul 16 13:13 run
lrwxrwxrwx   1 root   root       8 Jun 30 00:00 sbin -> usr/sbin
drwxr-xr-x   2 root   root    4096 Jun 30 00:00 srv
dr-xr-xr-x  13 nobody nogroup    0 Jul 16 13:13 sys
drwxrwxrwt   2 root   root    4096 Jun 30 00:00 tmp
drwxr-xr-x   1 root   root    4096 Jun 30 00:00 usr
drwxr-xr-x   1 root   root    4096 Jun 30 00:00 var
root@balbertus:/# ls
balbertus  bin	boot  dev  docker-entrypoint.d	docker-entrypoint.sh  etc  home  lib  lib64  media  mnt  opt  proc  product_name  product_uuid	root  run  sbin  srv  sys  tmp	usr  var
root@balbertus:/# cd balbertus/
root@balbertus:/balbertus# pwd
/balbertus
root@balbertus:/balbertus# mount |grep balbertus
/dev/mapper/vgapp-home1 on /balbertus type ext4 (rw,relatime,errors=remount-ro)
root@balbertus:/balbertus# 

################################################################################################################

DAY - 3 - DEPLOYMENTS

Manifest
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    apps: nginx-balbertus
  name: nginx-balbertus
spec:
  replicas: 3 
  selector:
    matchLabels: nginx-balbertus
  strategy: {}
  template:
    metadata:
      labels: 
        apps: nginx-balbertus
    spec:
      containers:
      - image: nginx
        name: webserver
        resources:
          limits:
            cpu: "0.5"
            memory: "128Mi"
          requests:
            cpu: "0.3"
            memory: "64Mi"

>> To apply the Deployment:

$ kubectl apply -f deployment.yaml
 
>> To validate Deployment creation:

$ kubectl get deployment -l app=nginx-balbertus
NAME              READY   UP-TO-DATE   AVAILABLE   AGE
nginx-balbertus   3/3     3            3           51s

>> Getting pod information that was created by Deployment

$ kubectl get pods -l app=nginx-balbertus
NAME                               READY   STATUS    RESTARTS   AGE
nginx-balbertus-6f48b9c64d-28f7m   1/1     Running   0          3m50s
nginx-balbertus-6f48b9c64d-86v2n   1/1     Running   0          3m50s
nginx-balbertus-6f48b9c64d-xshc8   1/1     Running   0          3m50s

>> ReplicaSet infos

$ kubectl get replicaset -l app=nginx-balbertus
NAME                         DESIRED   CURRENT   READY   AGE
nginx-balbertus-6f48b9c64d   3         3         3       8m44s

>> Details using Describe output

$ kubectl describe deployment -l app=nginx-balbertus
Name:                   nginx-balbertus
Namespace:              default
CreationTimestamp:      Thu, 17 Jul 2025 15:30:26 -0300
Labels:                 app=nginx-balbertus
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=nginx-balbertus
Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=nginx-balbertus
  Containers:
   webserver:
    Image:      nginx
    Port:       <none>
    Host Port:  <none>
    Limits:
      cpu:     500m
      memory:  128Mi
    Requests:
      cpu:         300m
      memory:      64Mi
    Environment:   <none>
    Mounts:        <none>
  Volumes:         <none>
  Node-Selectors:  <none>
  Tolerations:     <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   nginx-balbertus-6f48b9c64d (3/3 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  10m   deployment-controller  Scaled up replica set nginx-balbertus-6f48b9c64d from 0 to 3

  >> Changing Deployment - APPLICATION VERSION ...

  SEE THIS EXAMPLE BELLOW

  Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=nginx-balbertus
  Containers:
   webserver:
    Image:      nginx
    Port:       <none>

>> ADDING NGINX VERSION

$ kubectl apply -f deployment.yaml 

deployment.apps/nginx-balbertus configured

$ kubectl describe deployment -l nginx-balbertus
No resources found in default namespace.

$ kubectl describe deployment nginx-balbertus
Name:                   nginx-balbertus
Namespace:              default
CreationTimestamp:      Thu, 17 Jul 2025 15:30:26 -0300
Labels:                 app=nginx-balbertus
Annotations:            deployment.kubernetes.io/revision: 2
Selector:               app=nginx-balbertus
Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=nginx-balbertus
  Containers:
   nginx:
    Image:      nginx:1.16.0
    Port:       <none>

>> We have 2 strategies for Deployment:

RollingUpdate
Recreate

By Default RollingUpdate with one pod or a group of pods at a time.

>> Changing this strategies:

Manifest changes

spec:
  replicas: 5
  selector:
    matchLabels: 
      app: nginx-balbertus
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 2
    type: RollingUpdate

$ kubectl apply -f deployment.yaml 
deployment.apps/nginx-balbertus configured

$ kubectl describe deployment nginx-balbertus
Name:                   nginx-balbertus
Namespace:              default
CreationTimestamp:      Thu, 17 Jul 2025 15:30:26 -0300
Labels:                 app=nginx-balbertus
Annotations:            deployment.kubernetes.io/revision: 3
Selector:               app=nginx-balbertus
Replicas:               5 desired | 5 updated | 5 total | 5 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  2 max unavailable, 1 max surge
Pod Template:
  Labels:  app=nginx-balbertus
  Containers:
   nginx:
    Image:      nginx:1.17.0

>> With kubectl rollout you can follow update strategies and stages.

$ kubectl rollout status deployment nginx-balbertus
Waiting for deployment "nginx-balbertus" rollout to finish: 3 out of 5 new replicas have been updated...
Waiting for deployment "nginx-balbertus" rollout to finish: 3 out of 5 new replicas have been updated...
Waiting for deployment "nginx-balbertus" rollout to finish: 3 out of 5 new replicas have been updated...
Waiting for deployment "nginx-balbertus" rollout to finish: 3 out of 5 new replicas have been updated...
Waiting for deployment "nginx-balbertus" rollout to finish: 3 out of 5 new replicas have been updated...
Waiting for deployment "nginx-balbertus" rollout to finish: 4 out of 5 new replicas have been updated...
Waiting for deployment "nginx-balbertus" rollout to finish: 4 out of 5 new replicas have been updated...
Waiting for deployment "nginx-balbertus" rollout to finish: 4 out of 5 new replicas have been updated...
Waiting for deployment "nginx-balbertus" rollout to finish: 1 old replicas are pending termination...
Waiting for deployment "nginx-balbertus" rollout to finish: 1 old replicas are pending termination...
Waiting for deployment "nginx-balbertus" rollout to finish: 1 old replicas are pending termination...
Waiting for deployment "nginx-balbertus" rollout to finish: 3 of 5 updated replicas are available...
Waiting for deployment "nginx-balbertus" rollout to finish: 4 of 5 updated replicas are available...
deployment "nginx-balbertus" successfully rolled out

>> see now 5 Replicas

$ kubectl get pods 
NAME                               READY   STATUS    RESTARTS   AGE
nginx-balbertus-76f7cf59cd-45lv2   1/1     Running   0          7m4s
nginx-balbertus-76f7cf59cd-7q6bq   1/1     Running   0          7m6s
nginx-balbertus-76f7cf59cd-cq8kp   1/1     Running   0          7m25s
nginx-balbertus-76f7cf59cd-nbqmm   1/1     Running   0          7m25s
nginx-balbertus-76f7cf59cd-q949m   1/1     Running   0          7m25s

# kubectl get pods -l app=nginx-balbertus -o yaml

      image: docker.io/library/nginx:1.17.0

Or 
$ kubectl exec -ti nginx-balbertus-76f7cf59cd-45lv2 -- nginx -v
nginx version: nginx/1.17.0
user1@balbertus-virtual-machine:~/PICK$

>>>MOVE TO RECRIATE strategy<<<

spec:
  replicas: 6
  selector:
    matchLabels: 
      app: nginx-balbertus
  strategy:
    type: Recreate
  template:

>>>kubectl apply -f deployment.yaml<<<

$ kubectl rollout status deployment nginx-balbertus
Waiting for deployment "nginx-balbertus" rollout to finish: 0 of 6 updated replicas are available...
Waiting for deployment "nginx-balbertus" rollout to finish: 1 of 6 updated replicas are available...
Waiting for deployment "nginx-balbertus" rollout to finish: 2 of 6 updated replicas are available...
Waiting for deployment "nginx-balbertus" rollout to finish: 3 of 6 updated replicas are available...
Waiting for deployment "nginx-balbertus" rollout to finish: 4 of 6 updated replicas are available...
Waiting for deployment "nginx-balbertus" rollout to finish: 5 of 6 updated replicas are available...
deployment "nginx-balbertus" successfully rolled out

$ kubectl get pods -l app=nginx-balbertus
NAME                               READY   STATUS    RESTARTS   AGE
nginx-balbertus-678bff7b6c-5qmtf   1/1     Running   0          53s
nginx-balbertus-678bff7b6c-c46nw   1/1     Running   0          53s
nginx-balbertus-678bff7b6c-rzkgf   1/1     Running   0          53s
nginx-balbertus-678bff7b6c-s8zgk   1/1     Running   0          53s
nginx-balbertus-678bff7b6c-txpjr   1/1     Running   0          53s
nginx-balbertus-678bff7b6c-vzx6w   1/1     Running   0          53s

$ kubectl exec -ti nginx-balbertus-678bff7b6c-5qmtf -- nginx -v
nginx version: nginx/1.18.0

>>>SEE ABOVE NEW VERSION AND 6 REPLICAS HAS BEEN APPLIED<<<

>>>TO GET BACK PREVIOUS VERSION OF Deployment APP USE kubectl rollout undo<<<

$ kubectl rollout undo deployment nginx-balbertus
deployment.apps/nginx-balbertus rolled back

$ kubectl get pods -l app=nginx-balbertus
NAME                               READY   STATUS    RESTARTS   AGE
nginx-balbertus-76f7cf59cd-5l7k2   1/1     Running   0          7s
nginx-balbertus-76f7cf59cd-67fgb   1/1     Running   0          7s
nginx-balbertus-76f7cf59cd-gbpz4   1/1     Running   0          7s
nginx-balbertus-76f7cf59cd-hn7bf   1/1     Running   0          8s
nginx-balbertus-76f7cf59cd-sfmht   1/1     Running   0          7s
nginx-balbertus-76f7cf59cd-stv2b   1/1     Running   0          7s
user1@balbertus-virtual-machine:~/PICK/kubernets_uncomplicated/day-3$ kubectl exec -ti nginx-balbertus-76f7cf59cd-5l7k2 -- nginx -v
nginx version: nginx/1.17.0
user1@balbertus-virtual-machine:~/PICK/kubernets_uncomplicated/day-3$

>>>SEE HISTORY OF VERSIONS / REVISIONS<<<

$ kubectl rollout history deployment nginx-balbertus
deployment.apps/nginx-balbertus 
REVISION  CHANGE-CAUSE
1         <none>
2         <none>
4         <none>
5         <none>

>>>COMPARE REVISION 4 and 5 APP NGINX VERSION BEFORE AND AFTER ROLLBACK<<<

$ kubectl rollout history deployment nginx-balbertus --revision=4
deployment.apps/nginx-balbertus with revision #4
Pod Template:
  Labels:       app=nginx-balbertus
        pod-template-hash=678bff7b6c
  Containers:
   nginx:
    Image:      nginx:1.18.0
    Port:       <none>
    Host Port:  <none>
    Limits:
      cpu:      500m
      memory:   128Mi
    Requests:
      cpu:      300m
      memory:   64Mi
    Environment:        <none>
    Mounts:     <none>
  Volumes:      <none>
  Node-Selectors:       <none>
  Tolerations:  <none>

$ kubectl rollout history deployment nginx-balbertus --revision=5
deployment.apps/nginx-balbertus with revision #5
Pod Template:
  Labels:       app=nginx-balbertus
        pod-template-hash=76f7cf59cd
  Containers:
   nginx:
    Image:      nginx:1.17.0
    Port:       <none>
    Host Port:  <none>
    Limits:
      cpu:      500m
      memory:   128Mi
    Requests:
      cpu:      300m
      memory:   64Mi
    Environment:        <none>
    Mounts:     <none>
  Volumes:      <none>
  Node-Selectors:       <none>
  Tolerations:  <none>

user1@balbertus-virtual-machine:~/PICK/kubernets_uncomplicated/day-3$

>>>ALSO WE HAVE<<<

kubectl rollout pause TO STOP AND BLOCK ANY UPDATE AND kubectl rollout resume TO "PERMIT" AGAIN START UPDATE. FINALY WE CAN REMOVE THIS DEPLOYMENT WITH TWO WAYS

by Manifest and direct as BELLOW

$kubectl delete deployment nginx-balbertus

$kubectl delete -f deployment.yaml

$ kubectl get deployment
NAME              READY   UP-TO-DATE   AVAILABLE   AGE
nginx-balbertus   6/6     6            6           83m
$ kubectl delete deployment nginx-balbertus
deployment.apps "nginx-balbertus" deleted
$ kubectl get deployment
No resources found in default namespace.
 
$ kubectl apply -f deployment.yaml 
deployment.apps/nginx-balbertus created
$ kubectl get deployment
NAME              READY   UP-TO-DATE   AVAILABLE   AGE
nginx-balbertus   2/6     6            2           4s
$ kubectl delete -f deployment.yaml 
deployment.apps "nginx-balbertus" deleted
$ kubectl get deployment
No resources found in default namespace.
>>>ABOUT Namespace<<<

$ kubectl get namespaces
NAME                 STATUS   AGE
default              Active   3d1h
kube-node-lease      Active   3d1h
kube-public          Active   3d1h
kube-system          Active   3d1h
local-path-storage   Active   3d1h

$ kubectl create namespace balbertus
namespace/balbertus created

$ kubectl get namespaces
NAME                 STATUS   AGE
balbertus            Active   2s
default              Active   3d1h
kube-node-lease      Active   3d1h
kube-public          Active   3d1h
kube-system          Active   3d1h
local-path-storage   Active   3d1h

$ kubectl apply -f deployment.yaml -n balbertus
deployment.apps/nginx-balbertus created
$ kubectl get deployments
No resources found in default namespace.

$ kubectl get deployments -n balbertus
NAME              READY   UP-TO-DATE   AVAILABLE   AGE
nginx-balbertus   6/6     6            6           23s

#################################################################################
DAY - 4 - REPLICASET / DAEMONSET and PROBES

$ kubectl get deployment
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   1/1     1            1           10s
$ kubectl get replicaset
NAME                          DESIRED   CURRENT   READY   AGE
nginx-deployment-847d96855b   1         1         1       3m45s
$ cat deployment.yaml 
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx-deployment
  name: nginx-deployment
spec:
  replicas: 1 
  selector:
    matchLabels: 
      app: nginx-deployment
  strategy: {}
  template:
    metadata:
      labels: 
        app: nginx-deployment
    spec:
      containers:
      - image: nginx
        name: nginx
        resources:
          limits:
            cpu: "0.5"
            memory: "128Mi"
          requests:
            cpu: "0.3"
            memory: "64Mi"

#######################SCALE REPLICASET###############################################
$ kubectl scale deployment nginx-deployment --replicas=3
deployment.apps/nginx-deployment scaled
$ kubectl get replicaset
NAME                          DESIRED   CURRENT   READY   AGE
nginx-deployment-847d96855b   3         3         3       7m41s
$ kubectl get deployment
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   3/3     3            3           7m49s

#######################DESCRIBE####################################################
$ kubectl describe replicaset nginx-deployment-575cffbfc6
Name:           nginx-deployment-575cffbfc6
Namespace:      default
Selector:       app=nginx-deployment,pod-template-hash=575cffbfc6
Labels:         app=nginx-deployment
                pod-template-hash=575cffbfc6
Annotations:    deployment.kubernetes.io/desired-replicas: 3
                deployment.kubernetes.io/max-replicas: 4
                deployment.kubernetes.io/revision: 1
Controlled By:  Deployment/nginx-deployment
Replicas:       3 current / 3 desired
Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:  app=nginx-deployment
           pod-template-hash=575cffbfc6
  Containers:
   nginx:
    Image:      nginx:1.18.0
    Port:       <none>
    Host Port:  <none>
    Limits:
      cpu:     500m
      memory:  128Mi
    Requests:
      cpu:         300m
      memory:      64Mi
    Environment:   <none>
    Mounts:        <none>
  Volumes:         <none>
  Node-Selectors:  <none>
  Tolerations:     <none>
Events:
  Type    Reason            Age   From                   Message
  ----    ------            ----  ----                   -------
  Normal  SuccessfulCreate  63s   replicaset-controller  Created pod: nginx-deployment-575cffbfc6-42wwf
  Normal  SuccessfulCreate  63s   replicaset-controller  Created pod: nginx-deployment-575cffbfc6-2pts5
  Normal  SuccessfulCreate  63s   replicaset-controller  Created pod: nginx-deployment-575cffbfc6-n7d95

################################NEW APP VERSION##########################################################
$ kubectl get replicaset
NAME                          DESIRED   CURRENT   READY   AGE
nginx-deployment-575cffbfc6   3         3         3       2m32s
user1@balbertus-virtual-machine:~/PICK/kubernets_uncomplicated/day-4$ vim deployment.yaml 
user1@balbertus-virtual-machine:~/PICK/kubernets_uncomplicated/day-4$ kubectl apply -f deployment.yaml 
deployment.apps/nginx-deployment configured
user1@balbertus-virtual-machine:~/PICK/kubernets_uncomplicated/day-4$ kubectl get replicaset
NAME                          DESIRED   CURRENT   READY   AGE
nginx-deployment-575cffbfc6   3         3         3       2m55s
nginx-deployment-579d7cd555   1         1         0       3s

>>>SEE the TWO replicasets.... differ by nginx app version<<<
$ kubectl describe replicaset nginx-deployment 
Name:           nginx-deployment-575cffbfc6
Namespace:      default
Selector:       app=nginx-deployment,pod-template-hash=575cffbfc6
Labels:         app=nginx-deployment
                pod-template-hash=575cffbfc6
Annotations:    deployment.kubernetes.io/desired-replicas: 3
                deployment.kubernetes.io/max-replicas: 4
                deployment.kubernetes.io/revision: 1
Controlled By:  Deployment/nginx-deployment
Replicas:       0 current / 0 desired
Pods Status:    0 Running / 0 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:  app=nginx-deployment
           pod-template-hash=575cffbfc6
  Containers:
   nginx:
    Image:      nginx:1.18.0
    Port:       <none>
    Host Port:  <none>
    Limits:
      cpu:     500m
      memory:  128Mi
    Requests:
      cpu:         300m
      memory:      64Mi
    Environment:   <none>
    Mounts:        <none>
  Volumes:         <none>
  Node-Selectors:  <none>
  Tolerations:     <none>
Events:
  Type    Reason            Age    From                   Message
  ----    ------            ----   ----                   -------
  Normal  SuccessfulCreate  5m15s  replicaset-controller  Created pod: nginx-deployment-575cffbfc6-42wwf
  Normal  SuccessfulCreate  5m15s  replicaset-controller  Created pod: nginx-deployment-575cffbfc6-2pts5
  Normal  SuccessfulCreate  5m15s  replicaset-controller  Created pod: nginx-deployment-575cffbfc6-n7d95
  Normal  SuccessfulDelete  2m10s  replicaset-controller  Deleted pod: nginx-deployment-575cffbfc6-42wwf
  Normal  SuccessfulDelete  117s   replicaset-controller  Deleted pod: nginx-deployment-575cffbfc6-2pts5
  Normal  SuccessfulDelete  115s   replicaset-controller  Deleted pod: nginx-deployment-575cffbfc6-n7d95

Name:           nginx-deployment-579d7cd555
Namespace:      default
Selector:       app=nginx-deployment,pod-template-hash=579d7cd555
Labels:         app=nginx-deployment
                pod-template-hash=579d7cd555
Annotations:    deployment.kubernetes.io/desired-replicas: 3
                deployment.kubernetes.io/max-replicas: 4
                deployment.kubernetes.io/revision: 2
Controlled By:  Deployment/nginx-deployment
Replicas:       3 current / 3 desired
Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:  app=nginx-deployment
           pod-template-hash=579d7cd555
  Containers:
   nginx:
    Image:      nginx:1.19.0
    Port:       <none>
    Host Port:  <none>
    Limits:
      cpu:     500m
      memory:  128Mi
    Requests:
      cpu:         300m
      memory:      64Mi
    Environment:   <none>
    Mounts:        <none>
  Volumes:         <none>
  Node-Selectors:  <none>
  Tolerations:     <none>
Events:
  Type    Reason            Age    From                   Message
  ----    ------            ----   ----                   -------
  Normal  SuccessfulCreate  2m23s  replicaset-controller  Created pod: nginx-deployment-579d7cd555-dgtf7
  Normal  SuccessfulCreate  2m10s  replicaset-controller  Created pod: nginx-deployment-579d7cd555-tw8vz
  Normal  SuccessfulCreate  117s   replicaset-controller  Created pod: nginx-deployment-579d7cd555-f7xsk

$ kubectl get replicaset
NAME                          DESIRED   CURRENT   READY   AGE
nginx-deployment-575cffbfc6   0         0         0       8m39s
nginx-deployment-579d7cd555   3         3         3       5m47s

################################DOING A ROLLBACK###########################################################################

user1@balbertus-virtual-machine:~/PICK/kubernets_uncomplicated/day-4$ kubectl rollout undo deployment nginx-deployment
deployment.apps/nginx-deployment rolled back
user1@balbertus-virtual-machine:~/PICK/kubernets_uncomplicated/day-4$ kubectl get replicaset
NAME                          DESIRED   CURRENT   READY   AGE
nginx-deployment-575cffbfc6   2         2         1       9m21s
nginx-deployment-579d7cd555   2         2         2       6m29s
user1@balbertus-virtual-machine:~/PICK/kubernets_uncomplicated/day-4$ kubectl get replicaset
NAME                          DESIRED   CURRENT   READY   AGE
nginx-deployment-575cffbfc6   3         3         3       9m23s
nginx-deployment-579d7cd555   0         0         0       6m31s
user1@balbertus-virtual-machine:~/PICK/kubernets_uncomplicated/day-4$ 

>>>THIS IS ONE OF THE FUNCTIONS OF REPLICASET<<<

######################DEAMONSET####################################################################

$ kubectl apply -f node-exporter-daemonset.yaml
daemonset.apps/node-exporter created
$ kubectl get daemonset
NAME            DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
node-exporter   2         2         0       2            0           <none>          4s

>> Checking pods 

$ kubectl get pods -l app=node-exporter
NAME                  READY   STATUS    RESTARTS   AGE
node-exporter-rqnch   1/1     Running   0          3m55s
node-exporter-zpdd8   1/1     Running   0          3m55s

>>> Check in case its on all nodes of the cluster

$ kubectl get pods -o wide -l app=node-exporter
NAME                  READY   STATUS    RESTARTS   AGE     IP           NODE           NOMINATED NODE   READINESS GATES
node-exporter-rqnch   1/1     Running   0          9m29s   172.18.0.3   kind-worker    <none>           <none>
node-exporter-zpdd8   1/1     Running   0          9m29s   172.18.0.2   kind-worker2   <none>           <none>

>>>And describe to see all details<<<

$ kubectl describe daemonset node-exporter
Name:           node-exporter
Namespace:      default
Selector:       app=node-exporter
Node-Selector:  <none>
Labels:         <none>
Annotations:    deprecated.daemonset.template.generation: 1
Desired Number of Nodes Scheduled: 2
Current Number of Nodes Scheduled: 2
Number of Nodes Scheduled with Up-to-date Pods: 2
Number of Nodes Scheduled with Available Pods: 2
Number of Nodes Misscheduled: 0
Pods Status:  2 Running / 0 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:  app=node-exporter
  Containers:
   node-exporter:
    Image:        prom/node-exporter:latest
    Port:         9100/TCP
    Host Port:    9100/TCP
    Environment:  <none>
    Mounts:
      /host/proc from proc (ro)
      /host/sys from sys (ro)
  Volumes:
   proc:
    Type:          HostPath (bare host directory volume)
    Path:          /proc
    HostPathType:  
   sys:
    Type:          HostPath (bare host directory volume)
    Path:          /sys
    HostPathType:  
  Node-Selectors:  <none>
  Tolerations:     <none>
Events:
  Type    Reason            Age   From                  Message
  ----    ------            ----  ----                  -------
  Normal  SuccessfulCreate  11m   daemonset-controller  Created pod: node-exporter-rqnch

######################################################################################################
DAY 5 - Instalar um Cluster Kubernetes com 3 nodes.

We are going to start with kubeadm by instances created on the cloud as AWS/GCP/Azure or where you want.

Install a cluster with kubeadm!

1. Create 3 instances at AWS, GCP or Azure (it's about your cloud environment, I am not going to show here)
	a. At least VMs medium type.
	b. 2 vCPU and 4GB mem

2. Release Firewall rules as bellow
	Control Plane TCP 6443
	All Nodes     TCP 10250-10255
	WeaveNet      TCP 6783 and 6784
      		      UDP 6783

3. Intall all updates... (I'm working within Ubuntu)
#sudo apt-get update

and configure some parameters as:
# swapoff -a
- check in case /etc/fstab

- create some modules to K8s cluister config
# /etc/modules-load.d/k8s.conf
overlay
br_netfilter
- load (without reboot)
sudo modprobe overlay
sudo modprobe br_netfilter

- prepare some Kernel parameters
#/etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward = 1
#sudo sysctl --system

- Install transport https and curl
#apt-get install apt-transport-https

Now go to add Kubeadm repository.

https://v1-33.docs.kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/

After kubeadm installation as result bellow:
#kubelet set on hold
#kubeadm set on hold
#kubectl set on hold

<img width="1437" height="190" alt="image" src="https://github.com/user-attachments/assets/4c3e208d-0e1f-43ee-8462-50467e72315a" />

Check in case Container runtime is installed. (containerd)

if not follow instructions bellow

#sudo apt-get install gnupg lsb-release ca-certificates

Set up Docker's apt repository

#sudo apt-get install ca-certificates curl
#sudo install -m 0755 -d /etc/apt/keyrings
#sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
#sudo chmod a+r /etc/apt/keyrings/docker.asc

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

#sudo apt-get update

Finaly get installed Containerd

#sudo apt-get install containerd.io

Copy containerd config default to /etc/containerd/config.toml it is necessary to change system cgroup from FALSE to TRUE.

#sudo containerd config default |sudo tee /etc/containerd/config.toml

#sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/g' /etc/containerd/config.toml

This parameter bellow needs to be true.
systemd_cgroup = true
restart containerd

For now Do it only on Control Plane Machine!

Enabling  Kubeclt to Start our cluster

#sudo kubeadm init --pod-network-cidr=<YOUR IP> --apiserver-advertise-address=<IP to talk with nodes>

Follow instructions bellow:

Your Kubernetes control-plane has initilized successfully!

To start using your cluster, you need to run the following as a regular user:

<img width="1156" height="545" alt="image" src="https://github.com/user-attachments/assets/bae46dc6-56d2-4e15-b7e2-5fab9fe2209e" />

>>>> images screenshot >>>>

Add other nodes to the cluster...

#kubeadmin join <ip:port> --token <m5ltk...> --discovery-token-ca-cert-hash <sha...>

get command line started in kubeadm join <your ip>

Install CNI - network plugin

<img width="1295" height="427" alt="image" src="https://github.com/user-attachments/assets/39b7e2fb-1102-440b-bc32-0b92c7e53d69" />

>>>>>> screenshot <<<<<<<<

After that just play with one deployment

#kubeectl create deployment nginx --image nginx --replicas 3

<img width="797" height="165" alt="image" src="https://github.com/user-attachments/assets/9708d4f1-5407-4cfe-8175-0c6ff5707d76" />

>>>>> screen shot <<<<<<


########################################################################################################################################
Day-6 - Storage Class

Storage Class is a object that descruve and determine storages available for clusters. Persistent Volumes (PVs) and Persistent Volumes Claims (PVCs).

See all storages classes available in your cluster...

#kubectl get storageclass

$  kubectl get storageclass
NAME                 PROVISIONER                    RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
balbertus-local      kubernetes.io/no-provisioner   Retain          WaitForFirstConsumer   false                  6d6h
standard (default)   rancher.io/local-path          Delete          WaitForFirstConsumer   false                  6d7h

rancher.io/local-path, is a default provisioner provided by Kind.


You can create a new Storage Class to create a Persitent Volume on host directory - kind kubernetes.io/host-path.

Manifest:
$ cat pv.yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name:  balbertus-2
provisioner: kubernetes.io/no-provisioner
reclaimPolicy: Retain
volumeBindingMode: WaitForFirstConsumer


$ kubectl apply -f pv.yaml 
storageclass.storage.k8s.io/balbertus-2 created
balbertus@HP-ProBook-440-G1:~/PICK/kubernets_uncomplicated/day-6$ kubectl get storageclass
NAME                 PROVISIONER                    RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
balbertus-2          kubernetes.io/no-provisioner   Retain          WaitForFirstConsumer   false                  20s
standard (default)   rancher.io/local-path          Delete          WaitForFirstConsumer   false                  6d7h
balbertus@HP-ProBook-440-G1:~/PICK/kubernets_uncomplicated/day-6$

Use describe to see all details of this new Storage Class

$ kubectl describe storageclass balbertus-2
Name:            balbertus-2
IsDefaultClass:  No
Annotations:     kubectl.kubernetes.io/last-applied-configuration={"apiVersion":"storage.k8s.io/v1","kind":"StorageClass","metadata":{"annotations":{},"name":"balbertus-2"},"provisioner":"kubernetes.io/no-provisioner","reclaimPolicy":"Retain","volumeBindingMode":"WaitForFirstConsumer"}

Provisioner:           kubernetes.io/no-provisioner
Parameters:            <none>
AllowVolumeExpansion:  <unset>
MountOptions:          <none>
ReclaimPolicy:         Retain
VolumeBindingMode:     WaitForFirstConsumer
Events:                <none>

>>>>PV<<<<<

Persiste Volume (PV) is a resource to physical storage into a cluster. That would be local or remote.

Local:
HostPath

Remote: (Network)
NFS
iSCSI
Cloud Providers

See in case your PV

#kubectl get pv -A

$ kubectl get pv -A
No resources found

Go ahead to create your PV

Manifest:
pv.yaml
$ cat pv.yaml 
apiVersion: v1
kind: PersistentVolume
metadata:
  name:  balbertus-pv
  labels: 
    type: local
spec: 
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  hostPath:
    path: "/mnt/data"
  storageClassName: balbertus-2


#kubectl apply -f pv.yaml

$ kubectl get pv
NAME           CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   VOLUMEATTRIBUTESCLASS   REASON   AGE
balbertus-pv   1Gi        RWO            Retain           Available           balbertus-2    <unset>                          15s

>>>>> PVC <<<<<<
First its import to mention Storage Class NFS
>>>>> Manifest
$ cat storageclass-nfs.yaml 
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: nfs
provisioner: kubernetes.io/no-provisioner
reclaimPolicy: Retain
volumeBindingMode: WaitForFirstConsumer
parameters:
  archiveOnDelete: "false" 

Output
$ kubectl apply -f storageclass-nfs.yaml 
storageclass.storage.k8s.io/nfs created
$ kubectl get storageclass
NAME                 PROVISIONER                    RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
balbertus-2          kubernetes.io/no-provisioner   Retain          WaitForFirstConsumer   false                  22h
nfs                  kubernetes.io/no-provisioner   Retain          WaitForFirstConsumer   false                  11s
standard (default)   rancher.io/local-path          Delete          WaitForFirstConsumer   false                  7d6h

Right now We can create a PV over NFS...
>>>>>Manifest
$ cat pv-nfs.yaml 
apiVersion: v1
kind: PersistentVolume
metadata:
  name:  balbertus-pv-nfs
  labels: 
    type: nfs
spec: 
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  nfs:
    server: 192.168.0.18 #YOUR NFS SERVER
    path: "/mnt/data"
  storageClassName: nfs
>>>>>
>>>>>OUTPUT
 kubectl get pv
NAME               CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   VOLUMEATTRIBUTESCLASS   REASON   AGE
balbertus-pv       1Gi        RWO            Retain           Available           balbertus-2    <unset>                          21h
balbertus-pv-nfs   1Gi        RWO            Retain           Available           nfs            <unset>                          14s

>>>>>>>>>>PVC<<<<<<<<<<
Persistent Volume Claim: That is associate to Storage Class or Persistent Volume.
>>>>>>>>>
>>>>>>>>>Manifest
$ cat pvc.yaml 
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name:  balbertus-pvc
spec: 
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
  storageClassName: nfs
  selector:
    matchLabels:
      storage: nfs

>>>>>>Output
kubectl get pvc
NAME            STATUS    VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE
balbertus-pvc   Pending                                      nfs            <unset>                 14s

Describe to complete view
$ kubectl describe pvc
Name:          balbertus-pvc
Namespace:     default
StorageClass:  nfs
Status:        Pending
Volume:        
Labels:        <none>
Annotations:   <none>
Finalizers:    [kubernetes.io/pvc-protection]
Capacity:      
Access Modes:  
VolumeMode:    Filesystem
Used By:       <none>
Events:
  Type    Reason                Age               From                         Message
  ----    ------                ----              ----                         -------
  Normal  WaitForFirstConsumer  8s (x3 over 28s)  persistentvolume-controller  waiting for first consumer to be created before binding

Its PENDING because pvc is waiting for first pod creation.

POD manifest
$ cat pod.yaml 
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
  - name: nginx
    image: nginx:latest
    ports:
    - containerPort: 80
    volumeMounts:
    - name: balbertus-pvc
      mountPath: /usr/share/nginx/html
  volumes:
   - name: balbertus-pvc
     persistentVolumeClaim:
       claimName: balbertus-pvc

new output





