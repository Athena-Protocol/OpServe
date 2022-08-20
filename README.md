# OpServe
Ocean Protocol Middelware configured to run on with Athena Protocol integrations. 


**Create EKS Cluster**
===========================================

1. eksctl create cluster --name athena-compute-cluster --region us-east-1

	To install eksctl - https://docs.aws.amazon.com/eks/latest/userguide/eksctl.html

	https://docs.aws.amazon.com/eks/latest/userguide/eksctl.html	

2. Install kubernetes dashboard
	https://docs.aws.amazon.com/eks/latest/userguide/dashboard-tutorial.html

**Run Kubernetes Dashboard**
===========================================
1. aws eks get-token --cluster-name athena-compute-cluster 
2. kubectl proxy
3. http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/#/login

Enter token from step 1 to login. If the token expires, regenerate using step 1.

NOTE: If you are running the already setup kubernetes cluster, you only need to perform the above steps to see the kubernetes dashboard. Make sure you have setup aws and kubernetes command line. For that perform the following steps - 

1. In a command prompt run - 
	aws configure
   Provide your keys and region in which k8 is setup ex. us-east-1. You can get the keys from IAM service.
   
2. Install kubectl (1.24)
3. Run command - 
   aws eks update-kubeconfig --name athena-compute-cluster --region us-east-1
   
 This is a one time setup.  

**Create namespaces**
===========================================
1. kubectl create namespace ocean-compute
2. kubectl create namespace ocean-operator
3. kubectl create market
4. kubectl create provider



**Yaml files of each service**
===========================================
Each repo has deployment.yaml file (created newly for market, provider) that will be used for deploying pods for each service

Run Yaml file for each service. Example - 
1. kubectl config set-context --current --namespace market
2. kubectl apply -f d:\Projects\Ocean\market-deploy.yml

**Setup load balancer to expose market**
===========================================
Expose market through load balancer

kubectl expose deployment market --type=LoadBalancer --name=market

Copy the endpoint from kubernetes dashboard - which is the URL of market service



**Installing Prometheus on AWS EKS using helm**
===========================================

1. kubectl create namespace prometheus



2. Install helm - https://helm.sh/docs/intro/install/

3. helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

4. helm upgrade -i prometheus prometheus-community/prometheus \
    --namespace prometheus \
    --set alertmanager.persistentVolume.storageClass="gp2",server.persistentVolume.storageClass="gp2"

5. kubectl -n prometheus patch svc prometheus-server -p {"spec": {"type": "LoadBalancer"}}

6. kubectl apply -f grafana.yml


Installing filebeat on AWS EKS using ECK:
=========================================

--      Installing ECK on EKS     --

1. kubectl create -f https://download.elastic.co/downloads/eck/2.3.0/crds.yaml

2. kubectl apply -f https://download.elastic.co/downloads/eck/2.3.0/operator.yaml

----    Change the Elasticsearch/Opensearch url and credentials in the filebeat.yml file.  ---

3. kubectl apply -f filebeat.yml


long tpken - 

kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep eks-admin | awk '{print $1}')
