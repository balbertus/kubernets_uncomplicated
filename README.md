This is based on LinuxTips - Descomplicando o Kubernetes

Day -1 ! Install Kubectl and Kind to Starting play with Clusters

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

###########################get ignoring namespace - get by resources####################################################################################
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

##################################CREATE A YAML FILE################################################################################################
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
