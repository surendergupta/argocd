﻿# Argocd Installation and Setup

- Create deploy.yml file

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello
spec:
  replicas: 3
  selector:
    matchLabels:
      app: hello
  template:
    metadata:
      labels:
        app: hello
    spec:
      containers:
        - name: hello-microservice
          image: "public.ecr.aws/t5n9y4h0/suri-simple-mern-be-micro-hello-service:latest"
          imagePullPolicy: Always
          resources:
            limits:
              memory: "128Mi"
              cpu: "500m"
          ports:
            - containerPort: 3001
          env:
            - name: PORT
              value: "3001"

```

- Deploy you file in cluster minikube or EKS or kubeadm

```
kubectl apply -f deploy.yml
kubectl get deployment
kubectl get pod 
```

- Push your deploy.yml file into gitgub repositry

```
git add .\deploy.yml
git commit -m "WIP: adding the deployement hello k8s"
git push 
```

- Install ArgoCd

```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

- See what are argocd created inside my cluster

```
kubectl get pod -n argocd
kubectl get svc -n argocd

```

- To see argocd dashboard

```
kubectl port-forward -n argocd  svc/argocd-server 5000:443

```

![alt text](./screenshots/image.png)

![alt text](./screenshots/image-1.png)

- If argocd is run as a Service then you can find your password below command:

```
argocd admin initial-password -n argocd

```

![alt text](./screenshots/image-3.png)

- Otherwise you can use below command:

```
kubectl get secret argocd-initial-admin-secret -n argocd -o yaml

```

![alt text](./screenshots/image-2.png)

![alt text](./screenshots/image-4.png)

![alt text](./screenshots/image-5.png)

![alt text](./screenshots/image-6.png)

- Create Application.yml file you can also use to create git connect from argocd Dashboard (+ NEW APP) Button

```
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: mycool-argocd-app
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/surendergupta/argocd.git
    targetRevision: HEAD
    path: guestbook
  destination:
    server: https://kubernetes.default.svc
    namespace: myapp
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

- Deploy your application.yml file in cluster minikube or EKS or kubeadm

```
kubectl apply -f .\application.yml
```

- See your argocd Dashboard

![alt text](./screenshots/image-7.png)

- Till now it not fullyconfigured it will take time

![alt text](./screenshots/image-8.png)
