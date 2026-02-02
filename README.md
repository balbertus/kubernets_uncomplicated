# Subject
## This is based on the LinuxTips course called - Descomplicando o Kubernetes by Jefferson

Well I'm learning about Docker and Kubernetes this is the second big module of PICK - 'Programa Intensivo de Containers e Kubernetes' that I made in portuguese. This is a trascription to English with my point of view and learning journey. You also can take a look at my another repository dedicated to Containers (Docker) - 
docker_uncomplicated also based on LinuxTips PICK.
I tried to follow all contents and follow by days and create my personal notes to study and remmember all topics with command line outputs and example. 
The objectve here is learning with pratical labs and demonstrations to prove knowledge and adquire skills.

######################################################################################################
>  Install a K8s Cluster with 3 nodes.
I've been working with KIND that is a good one to start a simulated and local environment with few resources and in your local machine.
This is the path and documentation:

	- https://kind.sigs.k8s.io/

Also you can use a Cloud environment with kubeadm by instances created on the cloud as AWS/GCP/Azure or where you want.

>  Install a cluster with kubeadm!
	1. Create 3 instances at AWS, GCP or Azure (it's about your cloud environment, I am not going to show here)
		a. At least VMs medium type.
		b. 2 vCPU and 4GB mem
	2. Release Firewall rules as bellow
	Control Plane TCP 6443
	All Nodes     TCP 10250-10255
	WeaveNet      TCP 6783 and 6784
      		      UDP 6783
	3. Intall all updates... (I'm working within Ubuntu)
		$ sudo apt-get update
		and configure some parameters as:
		$ swapoff -a
		- check in case /etc/fstab
		- create some modules to K8s cluister config
		$ /etc/modules-load.d/k8s.conf
		overlay
		br_netfilter
		- load (without reboot)
		$ sudo modprobe overlay
		$ sudo modprobe br_netfilter
		- prepare some Kernel parameters
		$ /etc/sysctl.d/k8s.conf
		net.bridge.bridge-nf-call-iptables = 1
		net.bridge.bridge-nf-call-ip6tables = 1
		net.ipv4.ip_forward = 1
		$ sudo sysctl --system
		- Install transport https and curl
		$ apt-get install apt-transport-https

> Now go to add Kubeadm repository.
	- https://v1-33.docs.kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/

After kubeadm installation as result bellow:
$ kubelet set on hold
$ kubeadm set on hold
$ kubectl set on hold

<img width="1437" height="190" alt="image" src="https://github.com/user-attachments/assets/4c3e208d-0e1f-43ee-8462-50467e72315a" />

Check in case Container runtime is installed. (containerd). If not follow instructions bellow

$ sudo apt-get install gnupg lsb-release ca-certificates

Set up Docker's apt repository

$ sudo apt-get install ca-certificates curl

$ sudo install -m 0755 -d /etc/apt/keyrings

$ sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc

$ sudo chmod a+r /etc/apt/keyrings/docker.asc

	- echo \"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo /n  "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

$ sudo apt-get update

> Finaly get installed Containerd

$ sudo apt-get install containerd.io

Copy containerd config default to /etc/containerd/config.toml it is necessary to change system cgroup from FALSE to TRUE.

$ sudo containerd config default |sudo tee /etc/containerd/config.toml

$ sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/g' /etc/containerd/config.toml

This parameter bellow needs to be true.
systemd_cgroup = true
restart containerd

For now Do it only on Control Plane Machine!

> Enabling  Kubeclt to Start our cluster

$ sudo kubeadm init --pod-network-cidr=<YOUR IP> --apiserver-advertise-address=<IP to talk with nodes>

Follow instructions bellow:

Your Kubernetes control-plane has initilized successfully!

To start using your cluster, you need to run the following as a regular user:

<img width="1156" height="545" alt="image" src="https://github.com/user-attachments/assets/bae46dc6-56d2-4e15-b7e2-5fab9fe2209e" />

>>>> images screenshot >>>>

Add other nodes to the cluster...

$ kubeadmin join <ip:port> --token <m5ltk...> --discovery-token-ca-cert-hash <sha...>

get command line started in kubeadm join <your ip>

Install CNI - network plugin

<img width="1295" height="427" alt="image" src="https://github.com/user-attachments/assets/39b7e2fb-1102-440b-bc32-0b92c7e53d69" />

>>>>>> screenshot <<<<<<<<

After that just play with one deployment

$ kubeectl create deployment nginx --image nginx --replicas 3

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





