# Signup-Login Application CICD (HTML+CSS Project)

### Requirements to complete CICD:- 

AWS_3_Serrvers : 1 for Jenkins + Docker | 1 for Kubernetes Master | 1 for Kubernetes worker/node

Git_Repo : Application files + Dockerfile

Jenkins : To create Pipeline

Docker : To build image and push image to DockerHub

Kubernetes : Running Docker containers

1. Release one server to install Jenkins + Docker

```sh
yum update -y
yum install docker -y
systemctl enable docker
systemctl start docker
yum install pip -y
pip install docker-py
yum install git -y
wget -O /etc/yum.repos.d/jenkins.repo \
    https://pkg.jenkins.io/redhat-stable/jenkins.repo
rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
yum upgrade
amazon-linux-extras install java-openjdk11 -y
yum install jenkins -y
systemctl enable jenkins
systemctl start jenkins
```
2. Release 2 server to make Kubernetes cluster set up

1. System Requirements 
    >Master & Nodes: t2.medium (2 CPUs and 2GB Memory)   
     

1. Open Below ports in the Security Group. 
   #### Master node: 
    `6443  
    2379-2380  
    10250
    10251
    10252
    4443 
    443
    80
    22
    8080 `

   #### Worker node:
    `179
    30000-32767
    80
    22
    10250`  

   ### `On Master and Worker:`
   Perform all the commands as root user unless otherwise specified
 
## Docker setup and Configuration setting for all nodes
   Connect to the nodes with ssh 22 (Using MobaXterm for multi executive) 
   
1. Install Docker and Start services
   ```sh
   yum install -y docker
   systemctl enable docker
   systemctl start docker
   ```
   
2. Disable swap & Disable SELinux
   ```sh
   swapoff -a
   setenforce 0
   sed -i 's/enforcing/disabled/g' /etc/selinux/config
   grep disabled /etc/selinux/config | grep -v '#'
   sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config
   ```
   
## Kubernetes Setup
1. Add yum repository for kubernetes packages 
    ```sh
    cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
    [kubernetes]
    name=Kubernetes
    baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-\$basearch
    enabled=1
    gpgcheck=1
    gpgkey=https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
    exclude=kubelet kubeadm kubectl
    EOF
    ```
2. Install Kubernetes & Enable and Start kubelet service
    ```sh
    yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes
    sudo systemctl enable --now kubelet
    ```
3. Check the versions
   ```sh
   kubectl version
   kubelet --version
   kubeadm version
   ```
 
## `On Master Node:`
1. Initialize Kubernetes Cluster
    ```sh
    kubeadm init
    ```

2. kube configure setting with default commands
   ```sh
   mkdir -p $HOME/.kube
   sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
   sudo chown $(id -u):$(id -g) $HOME/.kube/config
   ```
   
   if already Initialized use Cluster join command to get token details to join workers
   ```sh
   kubeadm token create --print-join-command
   
   #Output
    kubeadm join 172.31.25.79:6443 --token 8haora.ebxx9dbbu5eyiqv2 --discovery-token-ca-cert-hash
    sha256:51033461996687f19049f9d3dd89e5e9b3e59acd53af17c1ab67e724a1f59bb7   
   ```

## `On Worker Nodes:`
1.  Run the kubeadm join command on worker nodes to join cluster
    ```sh
    kubeadm join 172.31.25.79:6443 --token 8haora.ebxx9dbbu5eyiqv2 --discovery-token-ca-cert-hash
    sha256:51033461996687f19049f9d3dd89e5e9b3e59acd53af17c1ab67e724a1f59bb7
    ```
  
## `On Master Node:`  
1.  Verifying the cluster To Get Nodes status / Calico communication installer
    ```sh
    kubectl get nodes
    kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.24.4/manifests/tigera-operator.yaml
    
    kubectl get pods -n kube-system -o wide
    kubectl get pods
    ```
2.  If Master + Nodes not ready please check with Calico installer OR CNI
  
    ![image](https://user-images.githubusercontent.com/111989928/200110244-410e46bb-f6cb-4f71-957d-dfa218bcbbf0.png)
