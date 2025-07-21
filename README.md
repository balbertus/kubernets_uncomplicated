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

#############################################################################################################################

DAY - 3
