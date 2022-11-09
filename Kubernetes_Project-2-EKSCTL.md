# Setup Kubernetes Cluster on AWS using eksctl

![image](https://user-images.githubusercontent.com/111989928/200792281-b1bfa380-a258-4258-8d3c-494f74b9b876.png)

 

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
   ![image](https://user-images.githubusercontent.com/111989928/200128560-6bd07abd-151a-4e71-8d28-5dd32f29686f.png)

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
   ![image](https://user-images.githubusercontent.com/111989928/200128535-2c7b187c-c00d-4e1a-a77f-2d94cda8f858.png)

  
3. Create an IAM Role and attache it to EC2 instance    
   `Note: create IAM user with programmatic access if your bootstrap system is outside of AWS`   
   IAM user should have access to   
   AmazonEC2FullAccess  
   IAMFullAccess 
   AdministratorAccess
   AmazonVPCFullAccess
   AWSCloudFormationFullAccess
   ![image](https://user-images.githubusercontent.com/111989928/200128479-77e1c6d3-5f8b-4807-a5c4-363b3cb5e16c.png)


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
   ![image](https://user-images.githubusercontent.com/111989928/200128583-9f522c17-ace6-43b0-8a27-ce2b2631a025.png)
   
   It will create Cloudformation stack
   ![image](https://user-images.githubusercontent.com/111989928/200128625-747f0080-0634-4f3d-87be-4d1051a9afe5.png)
   
   EC2 instances for nodes.
   ![image](https://user-images.githubusercontent.com/111989928/200128648-26bda5e6-64be-41e3-92af-84ae058e8738.png)
   
   Validate the Cluster and nodes status
   ```sh 
   eksctl get cluster --region us-east-2
   kubectl get nodes
   kubectl get all
   ```
   ![image](https://user-images.githubusercontent.com/111989928/200128893-263b18f4-4601-4a5a-9c11-754a72af0260.png)


5. Validate your cluster using by creating a pod 
   ```sh 
   kubectl create deployment myapp --image=raam043/nginx --replicas=3 --port=80
   kubectl expose deployment myapp --port=80 --type=LoadBalancer
   ```
   ![image](https://user-images.githubusercontent.com/111989928/200129107-e811fba1-afbf-4db1-921a-4263d1bda9c7.png)
   
   ![image](https://user-images.githubusercontent.com/111989928/200129469-3decf656-8716-46ac-b3e5-068600bb98c1.png)

   
   It will auto generate the Load balancer dns to listen
   
   ![image](https://user-images.githubusercontent.com/111989928/200129234-133d3d2d-a8d5-4b03-bd5b-2ae930e44381.png)

   
6. To delete the EKS clsuter 
   ```sh 
   eksctl delete cluster ramesh-cluster --region us-east-2
   ```

