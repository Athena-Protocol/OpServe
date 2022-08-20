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

Use token from step 1.


Installing Prometheus on AWS EKS using helm:
===========================================

1. kubectl create namespace monitoring

2. helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

3. helm upgrade -i prometheus prometheus-community/prometheus \
    --namespace monitoring \
    --set alertmanager.persistentVolume.storageClass="gp2",server.persistentVolume.storageClass="gp2"

4. kubectl -n monitoring patch svc prometheus-service -p '{"spec": {"type": "LoadBalancer"}}'

5. kubectl apply -f grafana.yml


Installing filebeat on AWS EKS using ECK:
=========================================

--      Installing ECK on EKS     --

1. kubectl create -f https://download.elastic.co/downloads/eck/2.3.0/crds.yaml

2. kubectl apply -f https://download.elastic.co/downloads/eck/2.3.0/operator.yaml

----    Change the Elasticsearch/Opensearch url and credentials in the filebeat.yml file.  ---

3. kubectl apply -f filebeat.yml
