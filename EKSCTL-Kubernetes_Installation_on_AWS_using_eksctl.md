# Setup Kubernetes Cluster on AWS using eksctl

You can follow same procedure in the official  AWS document [Getting started with Amazon EKS – eksctl](https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html)   

#### Pre-requisites: 
  - an EC2 Instance 

#### AWS EKS Setup 
1. Setup kubectl   
   a. Download kubectl version 1.20  
   b. Grant execution permissions to kubectl executable   
   c. Move kubectl onto /usr/local/bin   
   d. Test that your kubectl installation was successful    
   ```sh 
   curl -o kubectl https://s3.us-west-2.amazonaws.com/amazon-eks/1.20.4/2021-04-12/bin/linux/amd64/kubectl
   chmod +x ./kubectl
   mkdir -p $HOME/bin && cp ./kubectl $HOME/bin/kubectl && export PATH=$PATH:$HOME/bin
   echo 'export PATH=$PATH:$HOME/bin' >> ~/.bashrc
   kubectl version --short --client
   ```
2. Setup eksctl   
   a. Download and extract the latest release   
   b. Move the extracted binary to /usr/local/bin   
   c. Test that your eksclt installation was successful   
   ```sh
   curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
   sudo mv /tmp/eksctl /usr/local/bin
   eksctl version
   export PATH=$PATH:/usr/local/bin/
   ```
  
3. Create an IAM Role and attache it to EC2 instance    
   `Note: create IAM user with programmatic access if your bootstrap system is outside of AWS`   
   IAM user should have access to   
   AmazonEC2FullAccess  
   IAMFullAccess 
   AdministratorAccess
   AmazonVPCFullAccess
   AWSCloudFormationFullAccess

4. Create your cluster and nodes 
   ```sh
   eksctl create cluster --name cluster-name  \
   --region region-name \
   --node-type instance-type \
   --nodes-min 2 \
   --nodes-max 2 \ 
   --zones <AZ-1>,<AZ-2>
   
   example:
   eksctl create cluster --name ramesh-cluster \
   --region us-east-2 \
   --node-type t2.small
    ```

5. To delete the EKS clsuter 
   ```sh 
   eksctl delete cluster ramesh-cluster --region us-east-2
   ```
   
6. Validate your cluster using by creating by checking nodes and by creating a pod 
   ```sh 
   kubectl get nodes
   kubectl run pod tomcat --image=tomcat 
   ```
