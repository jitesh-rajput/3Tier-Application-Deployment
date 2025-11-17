# three-tier-Application-Deployment with Docker & Kubernetes

# Flow 
<img width="1536" height="1024" alt="ChatGPT Image Nov 11, 2025, 01_25_59 AM" src="https://github.com/user-attachments/assets/1547618c-ca39-45a6-8429-03b948608f48" />

# Prerequisite 
**Install Kubectl**
https://kubernetes.io/docs/tasks/tools/


**Install Helm**
https://helm.sh/docs/intro/install/

```
helm repo update
```

**Install/update latest AWS CLI:** (make sure install v2 only)
https://aws.amazon.com/cli/

#update the Kubernetes context
aws eks update-kubeconfig --name my-eks-cluster --region us-west-2

# verify access:
```
kubectl auth can-i "*" "*"
kubectl get nodes
```

# Verify autoscaler running:
```
kubectl get pods -n kube-system
```

# Check Autoscaler logs
```
kubectl logs -f \
  -n kube-system \
  -l app=cluster-autoscaler
```

# Check load balancer logs
```
kubectl logs -f -n kube-system \
  -l app.kubernetes.io/name=aws-load-balancer-controller
```

<!-- aws eks update-kubeconfig \
  --name my-eks \
  --region us-west-2 \
  --profile eks-admin -->


# Buid Docker image :

Buid Front End :

```
docker build -t frontend:v1 . 
docker tag frontend:v1 public.ecr.aws/w8u5e4v2/frontend:v1
docker push public.ecr.aws/w8u5e4v2/frontend:v1
```


Buid Back End :

```
docker build -t backend:v1 . 
docker tag backend:v1 public.ecr.aws/w8u5e4v2/backend:v1
docker push public.ecr.aws/w8u5e4v2/backend:v1
```



**Create Namespace**
```
kubectl create ns workshop

kubectl config set-context --current --namespace workshop
```

# MongoDB Database Setup

**To create MongoDB Resources**
```
cd k8s_manifests/mongo_v1
kubectl apply -f secrets.yaml
kubectl apply -f deploy.yaml
kubectl apply -f service.yaml
```

# Backend API Setup

Create NodeJs API deployment by running the following command:
```
kubectl apply -f backend-deployment.yaml
kubectl apply -f backend-service.yaml
``


**Frontend setup**

Create the Frontend  resource. In the terminal run the following command:
```
kubectl apply -f frontend-deployment.yaml
kubectl apply -f frontend-service.yaml
```

Finally create the final load balancer to allow internet traffic:
```
kubectl apply -f full_stack_lb.yaml
```

