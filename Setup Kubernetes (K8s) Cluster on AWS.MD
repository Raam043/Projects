

## Releasse k8s ubuntu serrver then connecct to MobXterm and follow below commands to install k8s

## Install AWS CLI on ubuntu server
   '''sh
sudo apt update -y

sudo apt install unzip python3

curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"

unzip awscliv2.zip

sudo ./aws/install

aws --version
   '''

## Install kubernetes "kubctl" on ubuntu server and move it to local bin (/usr/local/bin/kubectl)

curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl

chmod +x ./kubectl

sudo mv ./kubectl /usr/local/bin/kubectl

kubectl version


## Install kops on ubuntu server

curl -LO https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64

chmod +x kops-linux-amd64

sudo mv kops-linux-amd64 /usr/local/bin/kops



## Create an IAM user/role with Route53, EC2, IAM and S3 full access (k8s-role)
## Attach IAM role to ubuntu server (EC2 screen) Open ubuntu server secutiry setting then clicl on Modify IAM role atatche "k8s-role"

aws configure

Output key in:
* AWS Access Key ID [None]:
* AWS Secret Access Key [None]:
* Default region name [None]: us-east-2
* Default output format [None]:

aws s3 ls


## Create a Route53 private hosted zone (you can create Public hosted zone if you have a domain)
## in my case i have created saikrishnar.net (domain) in private

## create an S3 bucket

aws s3 mb s3://saikrishnar.net


## Expose environment variable:


export KOPS_STATE_STORE=s3://saikrishnar.net

## Create sshkeys before creating cluster


ssh-keygen

## Create kubernetes cluster definitions on S3 bucket

kops create cluster --cloud=aws --zones=us-east-2b --name=saikrishnar.net --dns-zone=saikrishnar.net --dns private

Suggestions:
 * list clusters with: kops get cluster
 * edit this cluster with: kops edit cluster saikrishnar.net
 * edit your node instance group: kops edit ig --name=saikrishnar.net nodes-us-east-2b
 * edit your master instance group: kops edit ig --name=saikrishnar.net master-us-east-2b


## Edit the cluster ( as master)

kops edit ig --name=saikrishnar.net master-us-east-2b

> edit machine type to "t2.micro" & save 

> run below command 

kops update cluster --name saikrishnar.net --yes --admin

kops validate cluster

> for validate the master
 
kops update cluster

> for update the master










