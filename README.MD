🚀 AWS EKS: HPA Setup with Load Testing
🎯 Project Goal
Show how Kubernetes’ Horizontal Pod Autoscaler (HPA) automatically scales pods in AWS EKS based on real CPU load.

Steps Overview
Create EKS Cluster

Deploy the Application

Expose via LoadBalancer

Install Metrics Server

Configure HPA

Generate Load

Observe Autoscaling

1. Create the EKS Cluster
bash
eksctl create cluster --name my-cluster --region us-west-2
2. Deploy the Application
bash
kubectl apply -f deployment.yaml
3. Expose Deployment with LoadBalancer
bash
kubectl expose deployment mywebd --type=LoadBalancer --name=mywebd-svc --port=80
kubectl get svc mywebd-svc
Test external access (replace <EXTERNAL-IP>):

text
http://<EXTERNAL-IP>
4. Install Metrics Server
bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
kubectl get pods -n kube-system
kubectl top pods
5. Configure Horizontal Pod Autoscaler
bash
kubectl autoscale deployment mywebd --cpu-percent=50 --min=1 --max=10
kubectl get hpa
kubectl get hpa -w

6. Generate Load
bash
kubectl run -i --tty load-generator --rm --image=busybox -- /bin/sh
# Inside the pod:
while true; do wget -q -O- http://<EXTERNAL-IP>; done
7. Observe Autoscaling
Keep an eye on HPA and pod behavior:

bash
kubectl get hpa -w

kubectl get pods
kubectl top pods
📷 Screenshots: Live Example
