
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
```
eksctl create cluster --name my-cluster --region us-west-2
```
2. Deploy the Application
```
kubectl create deployment mywebd --image=vimal13/apache-webserver-php --dry-run=client -o yaml > deployment.yaml
```
```
kubectl apply -f deployment.yaml
```
3. Expose Deployment with LoadBalancer
```
kubectl expose deployment mywebd --type=LoadBalancer --name=mywebd-svc --port=80
```
```
kubectl get svc mywebd-svc
```

Test external access (replace <EXTERNAL-IP>):


http://<EXTERNAL-IP>

![Image Alt](https://github.com/abhi-gadge1773/EKS-Cluster-with-HPA/blob/main/Screenshot%202025-07-30%20150631.png?raw=true)



4. Install Metrics Server
```
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```
```
kubectl get pods -n kube-system
```
```
kubectl top pods
```

5. Configure Horizontal Pod Autoscaler
```
kubectl autoscale deployment mywebd --cpu-percent=50 --min=1 --max=10
```
```
kubectl get hpa
```
```
kubectl get hpa -w
```

![Image Alt](https://github.com/abhi-gadge1773/EKS-Cluster-with-HPA/blob/main/Screenshot%202025-07-30%20142542.png?raw=true)


6. Generate Load
```
kubectl run -i --tty load-generator --rm --image=busybox -- /bin/sh
```

# Inside the pod:
```
while true; do wget -q -O- http://<EXTERNAL-IP>; done
```

![Image Alt](https://github.com/abhi-gadge1773/EKS-Cluster-with-HPA/blob/main/Screenshot%202025-07-30%20142552.png?raw=true)


7. Observe Autoscaling
Keep an eye on HPA and pod behavior:

```
kubectl get hpa -w
```
```
kubectl get pods
```
kubectl top pods
```

📷 Screenshots: Live Example

![Image Alt](https://github.com/abhi-gadge1773/EKS-Cluster-with-HPA/blob/main/Screenshot%202025-07-30%20142548.png?raw=true)

