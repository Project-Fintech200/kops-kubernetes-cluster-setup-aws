# kops-kubernetes-cluster-configuration
# Landmark Technologies,  -    Landmark Technologies 
# Tel: +1 437 215 2483,   -     +1 437 215 2483 
# mylandmarktech@gmail.com,  -    www.mylandmarktech.com 

# Setting up Kubernetes (K8s) Cluster on AWS Using KOPS

1. KOPS is a software use to create production ready k8s cluster in a cloud provider like AWS.

2. KOPS SUPPORTS MULTIPLE CLOUD PROVIDERS

3. KOPS compete with managed kubernestes services like EKS, AKS and GKE

4. KOPS is cheaper than the others.

5. KOPS create production ready K8S.

6. KOPS create resources like: LoadBalancers, ASG, Launch Configuration, woker node Master node (CONTROL PLANE.

7. KOPS is IaaC - Infrastructure as a Code

#!/bin/bash
# 1) Create Ubuntu EC2 instance in AWS and create kops user
 sudo adduser kops
 sudo echo "kops  ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/kops
 sudo su - kops

# 2) install AWSCLI
# use official documentation for update on how to install AWSCLI
# https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html
 sudo apt update -y
 sudo apt install unzip wget -y
 sudo curl https://s3.amazonaws.com/aws-cli/awscli-bundle.zip -o awscli-bundle.zip
 sudo apt install unzip python -y
 sudo unzip awscli-bundle.zip
 sudo ./awscli-bundle/install -i /usr/local/aws -b /usr/local/bin/aws
 
 OR
 
 sudo apt update -y
 sudo apt install unzip wget -y
 sudo curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
 sudo apt install unzip python -y
 sudo unzip awscliv2.zip
 sudo ./aws/install
 sudo ./aws/install -i /usr/local/aws-cli -b /usr/local/bin
 sudo ./aws/install --bin-dir /usr/local/bin --install-dir /usr/local/aws-cli --update
 # to find your symlink.
 which aws
 # to find the directory that your symlink points to 
 ls -l /usr/local/bin/aws
 # Confirm the version installation 
 aws --version
 
# 3) Install kops software on ubuntu instance:
 sudo apt install wget -y
 sudo wget https://github.com/kubernetes/kops/releases/download/v1.22.0/kops-linux-amd64
 sudo chmod +x kops-linux-amd64
 sudo mv kops-linux-amd64 /usr/local/bin/kops
 
# 4) Install kubectl

 sudo curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
 sudo chmod +x ./kubectl
 sudo mv ./kubectl /usr/local/bin/kubectl
 aws s3 mb s3://class27.k8s.local 
 aws s3 ls
 
  # Programatic access using
          # Access Key ID: AKIAWPH5362NUEGVAG6E
          # Secret Access Key: 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

# 5) Create an IAM role from AWS Console or CLI with below Policies.

	AmazonEC2FullAccess 
	AmazonS3FullAccess
	IAMFullAccess 
	AmazonVPCFullAccess

Then Attach IAM role to ubuntu server from Console Select KOPS Server --> Actions --> Security --> Attach/Replace IAM Role --> Select the role which You Created. --> Save.

# 6) create an S3 bucket Execute below command in KOPS Server use unique bucket name if you get bucket name exists error.
	aws s3 mb s3://class27.k8s.local
	aws s3 ls
	
    ex: s3://nubong.k8s.local
     
	Expose environment variable:

 # Add env variables in bashrc
    vi .bashrc
	
# Give Unique Name And S3 Bucket which you created.
# paste enviromental variables after "for example" without a comment from line 4
	export NAME=class27.k8s.local
	export KOPS_STATE_STORE=s3://class27.k8s.local

# refresh the installation 
    source .bashrc
	
# 7) Create sshkeys before creating cluster

    ssh-keygen
 
# 8) Create kubernetes cluster definitions on S3 bucket
# Details of installed EC2 instance displays availablity zone "us-east-1d"
# Number of nodes and server size required can be altered as necessary

	kops create cluster --zones us-east-1d --networking weave --master-size t2.medium --master-count 1 --node-size t2.medium --node-count=2 ${NAME}

<<mm 	
# Cluster configuration has been created.
Suggestions:
 * list clusters with: kops get cluster
 * edit this cluster with: kops edit cluster class27.k8s.local
 * edit your node instance group: kops edit ig --name=class27.k8s.local nodes-us-east-1d
 * edit your master instance group: kops edit ig --name=class27.k8s.local master-us-east-1d
    kops edit ig --name=class27.k8s.local us-east-1d

# Finally configure your cluster with:   
kops update cluster --name class27.k8s.local --yes --admin
mm 

# copy sshkeys into the cluster with admin access	
	kops create secret --name ${NAME} sshpublickey admin -i ~/.ssh/id_rsa.pub

# 9) Create kubernetes cluser
	 kops update cluster ${NAME} --yes
	 
<<mm
Suggestions:
 * validate cluster: kops validate cluster --wait 10m
 * list nodes: kubectl get nodes --show-labels
 * ssh to the master: ssh -i ~/.ssh/id_rsa ubuntu@api.class27.k8s.local
 * the ubuntu user is specific to Ubuntu. If not using Ubuntu please use the appropriate user based on your OS.
 * read about installing addons at: https://kops.sigs.k8s.io/operations/addons.
mm
     
# 10) Validate your cluster(KOPS will take some time to create cluster ,Execute below commond after 3 or 4 mins)

	   kops validate cluster

# 11) connect to the master node
    sh -i ~/.ssh/id_rsa ubuntu@PublicIPAddress
    ssh -i ~/.ssh/id_rsa ubuntu@3.90.203.23
# 11) To list nodes

	  kubectl get nodes 
 
# 12) To Delete Cluster

   kops delete cluster --name=${NAME} --state=${KOPS_STATE_STORE} --yes  
   
====================================================================================================


13 # IF you want to SSH to Kubernetes Master or Nodes Created by KOPS. You can SSH From KOPS_Server

sh -i ~/.ssh/id_rsa ubuntu@ipAddress
ssh -i ~/.ssh/id_rsa ubuntu@3.90.203.23
  
