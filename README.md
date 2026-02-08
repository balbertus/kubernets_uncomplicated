# Subject
## This is based on the LinuxTips course called - Descomplicando o Kubernetes by Jefferson

Well I'm learning about Docker and Kubernetes this is the second big module of PICK - 'Programa Intensivo de Containers e Kubernetes' that I made in portuguese. This is a trascription to English with my point of view and learning journey. You also can take a look at my another repository dedicated to Containers (Docker) - 
docker_uncomplicated also based on LinuxTips PICK.
I tried to follow all contents and follow by days and create my personal notes to study and remmember all topics with command line outputs and example. 
The objectve here is learning with pratical labs and demonstrations to prove knowledge and adquire skills.
##
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

## Summary

- Day 1: Recap about Container, Container Engine, Container RunTime, OCI and What is Kuberbernetes
  		 Workers and Control Plane, Pods, ReplicaSet, Deployments and Services.
         How to install Kubectl. Using Kind to simulate and run your environment.
- Day 2: What is a Pod, kubectl attach adn kubectl exec, Pod with more than one container, limit your resourceas CPU and Memory,
  		 Configure a volume if EmptyDir.
- Day 3: What is a Deployment, Rollback and Rollout.
- Day 4: What is ReplicaSet, DeamonSet, Probes (Liveness, Readness and Startup).
- Day 5: What is Cluster Kubernetes, node configuration, containerID, ports. How to add others nodes and CNI.
- Day 6: Volumes, StorageClass Persistent Volume (PV), Persistent Volume Claim (PVC).
- Day 7: StatefulSet, Services (ClusterIP and NodePort), (LoadBalancer and ExternalName).
- Day 8: Secrets and ConfigMaps.
- Day 9: Kube-Promethersand Promethes Operator, EKSCTL and AWS CLI, Grafana, AlertManager and ServiceMonitor.
- Day 10: ServiceMonitor - Observability.
- Day 11: What is Ingress in Kubernetes.
- Day 12: What is Cert-Manager, Labels, Annotations and more.
- Day 13: Horizontal Pod Autoscaler (HPA).
- Day 14: Kyverno
- Day 15: Taints, Tolerations, Labels, Affinity and Anti Affinity.
- Day 16: EKS and EKSCTL, Nginx Ingress, NetPol, Network Ploicy and more.
- Day 17: RBAC, Role, RoleBinding, ClusterRole.
- Day 18: What is HELM.

# Introduction to GitOps and ArgoCD







