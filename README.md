

# 🚀 AWS EKS: HPA Setup with Load Testing

The **goal of this project** is to:

---

## 🎯 **Demonstrate Auto-Scaling in Kubernetes on AWS EKS**

This project shows how to:

### ✅ 1. **Deploy an application** on an AWS-managed Kubernetes cluster (EKS).

### ✅ 2. **Expose the app** to the internet using a **LoadBalancer service**, so users can access it externally.

### ✅ 3. **Install and use Metrics Server** to collect CPU usage data from pods.

### ✅ 4. **Configure Horizontal Pod Autoscaler (HPA)** to automatically:

* Monitor the app’s CPU usage
* Increase or decrease the number of pods
* Based on real-time CPU demand

### ✅ 5. **Generate traffic** to simulate high CPU load and **observe the autoscaling in action**.

---



## 🔹 Step 1: Create EKS Cluster

```bash
eksctl create cluster --name my-cluster --region us-west-2
```
<img width="1345" height="596" alt="image" src="https://github.com/user-attachments/assets/0223b038-9293-42c7-8f5b-d69c448e1b9b" />

---

## 🔹 Step 2: Deploy the Application

Apply your deployment:

```bash
kubectl apply -f deployment.yaml
```
<img width="642" height="140" alt="image" src="https://github.com/user-attachments/assets/bcfe824f-c867-48f7-9231-f18c5754e1ee" />

---

## 🔹 Step 3: Expose Deployment as LoadBalancer

```bash
kubectl expose deployment mywebd --type=LoadBalancer --name=mywebd-svc --port=80
```

Get the service and copy the `EXTERNAL-IP`:

```bash
kubectl get svc mywebd-svc
```
<img width="1267" height="95" alt="image" src="https://github.com/user-attachments/assets/2783ba34-9c31-420f-bcbf-483564252d11" />


Open in browser or use curl:

```
http://<EXTERNAL-IP>
```
<img width="936" height="390" alt="image" src="https://github.com/user-attachments/assets/819e68d8-81ce-4f45-bd61-dc1cc6b896eb" />

---

## 🔹 Step 4: Install Metrics Server (for HPA)

```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```
<img width="1273" height="154" alt="image" src="https://github.com/user-attachments/assets/a6ba96a0-b9e9-4393-90b3-8a3c087d2938" />


Check if metrics-server pod is running:

```bash
kubectl get pods -n kube-system
```
<img width="736" height="141" alt="image" src="https://github.com/user-attachments/assets/ad8a20da-097b-4ac1-83ff-377d35dcd708" />


Test metrics availability:

```bash
kubectl top pods
```
<img width="652" height="48" alt="image" src="https://github.com/user-attachments/assets/08c5f098-d5cd-497b-b19a-8dfc7ce1b134" />


---

## 🔹 Step 5: Create Horizontal Pod Autoscaler

```bash
kubectl autoscale deployment mywebd --cpu-percent=50 --min=1 --max=10
```

Check HPA:

```bash
kubectl get hpa
```
<img width="1246" height="106" alt="image" src="https://github.com/user-attachments/assets/46abcc59-290c-4036-b7f0-2c71fb4b8d83" />


Watch live scaling:

```bash
kubectl get hpa -w
```
<img width="757" height="274" alt="image" src="https://github.com/user-attachments/assets/8a06e370-33d6-4208-8572-2df4bc5f2951" />

---

## 🔹 Step 6: Generate Load (in Separate Terminal 1)

```bash
kubectl run -i --tty load-generator --rm --image=busybox /bin/sh
```

Then inside the pod:

```sh
while true; do wget -q -O- http://<EXTERNAL-IP>; done
```
<img width="639" height="546" alt="image" src="https://github.com/user-attachments/assets/1afefc3d-515e-4826-92cf-531f93fb64dd" />

> Replace `<EXTERNAL-IP>` with your actual LoadBalancer IP.


## Watch live scaling:

```bash
kubectl get hpa -w
```
<img width="877" height="247" alt="image" src="https://github.com/user-attachments/assets/ec894e32-0dd0-4e38-b52f-f4efb1039a7c" />


<img width="700" height="287" alt="image" src="https://github.com/user-attachments/assets/a6fce0d2-6a02-458c-bf2a-859e4cb540e2" />
---

## ✅ Expected Behavior

* `kubectl get hpa -w` shows increasing `TARGETS` CPU usage.
* `REPLICAS` increase as load increases.
* HPA scales from 1 to more pods automatically.

---
