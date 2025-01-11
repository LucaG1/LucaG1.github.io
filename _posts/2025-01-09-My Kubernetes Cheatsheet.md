---
title: My Kubernetes Cheatsheet
date: 2025-01-09 23:00:00 +0100
categories: [cheatsheets]
tags: [cheatsheet, kubernetes]     # TAG names should always be lowercase
description: Commands I encountered when learning about kubernetes.
---

I will use this as a cheatsheet while learning about kubernetes. I started out with minikube to set up a local kubernetes cluster.

ephemeral - vergänglich

# Minikube

## Addons in minikube
```
minikube addons list
minikube addons enable dashboard
minikube addons enable metrics-server
```

# General

```
kubectl get namespaces
kubectl create namespace new-namespace-name 

kubectl config view
```
Compontent status and other
```
kubectl get cs

kubectl get po -A # get pods from all namespaces
```

```
minikube ssh
$ sudo grep admission /etc/kubernetes/manifests/kube-apiserver.yaml # make backup of this if changing
curl <service-ip> # for example
```

```
minikube service --all
```

## Other

```
kubectl -n kube-system describe pod kube-apiserver-minikube | grep -i admission
```

# Pods

## Creating and deleting
```
kubectl create -f def-pod.yaml
kubectl run nginx-pod --image=nginx:1.22.1 --port=80
kubectl run nginx-pod --image=nginx:1.22.1 --port=80 --dry-run=client -o yaml > nginx-pod.yaml
kubectl delete pod nginx-pod
```

Also possible to use `kubectl apply -f def-pod.yaml`. TODO: what is the difference?

## Getting Information

```
kubectl get pods
kubectl get pod nginx-pod -o yaml
kubectl describe pod nginx-pod
kubectl logs nginx-pod
```

# Replica Sets

Apparently ReplicationControllers < ReplicaSets < Deployments
```
kubectl get rs
kubectl scale rs frontend --replicas=4
```

# Deployments

```
kubectl create deployment nginx-deployment --image=nginx:1.20.2 --port=80 --replicas=3 --dry-run=client -o yaml > nginx-deploy.yaml
kubectl set image deploy nginx-deployment nginx=nginx:1.21.5 --record
kubectl rollout undo deploy nginx-deployment --to-revision=1
kubectl delete deploy nginx-deployment
```

```
kubectl apply -f nginx-deploy.yaml --record
kubectl scale deploy nginx-deployment --replicas=4
```

```
kubectl get deploy -o wide
kubectl get deploy nginx-deployment -o yaml
kubectl describe deploy nginx-deploymen
```

```
kubectl rollout status deploy nginx-deployment
kubectl rollout history deploy nginx-deployment
kubectl rollout history deploy nginx-deployment --revision=2
```

```
kubectl get all -l app=nginx-deployment -o wide
kubectl get deploy,rs,po -l app=nginx-deployment
```

# DaemonSets

- enforces single pod replica per node

```
kubectl get daemonsets
kubectl rollout status ds fluentd-agent
kubectl rollout history ds fluentd-agent
```

```
kubectl set image ds fluentd-agent fluentd=quay.io/fluentd_elasticsearch/fluentd:v4.6.2
```

```
kubectl get all -l k8s-app=fluentd-agent -o wide
```

# Authentication, Authorization, Access Control etc.

```
kubectl get csr
kubectl certificate approve bob-csr
kubectl config set-credentials bob --client-certificate=bob.crt --client-key=bob.key
kubectl config set-context bob-context --cluster=minikube --namespace=lfs158 --user=bob
(1)
kubectl --context=bob-context get pods # fails with permission denied
kubectl create -f role.yaml
kubectl create -f rolebinding.yaml
kubectl -n lfs158 get roles
kubectl --context=bob-context get pods # works now

```

From tutorial

```
(1) kubectl -n lfs158 create deployment nginx --image=nginx:alpine
```

# Services

```
kubectl expose deploy frontend --name=frontend-svc --port=80 --target-port=5000

kubectl get svc,ep frontend-svc

kubectl expose deploy frontend --name=frontend-svc --port=80 --target-port=5000 --type=NodePort 
kubectl create service nodeport frontend-svc --tcp=80:5000 --node-port=32233
```

(Give service to a client application)
```
kubectl exec client-app-pod-name -c client-container-name -- /bin/sh -c curl -s frontend-svc:80
```

From video tutorial
```
kubectl run po-hello --image=pbitty/hello-from:latest --port=80 --expose=true
kubectl get po,svc,ep --show-labels

kubectl edit svc pod-hello

kubectl create deployment deploy-hello --image=pbitty/hello-from:latest --port=80 --replicas=3
kubectl expose deployment deploy-hello --type=NodePort # can't be exposed with the create command directly
kubectl get deploy,po,svc,ep -l app=deploy-hello --show-labels 
```


```
kubectl port-forward deploy/frontend 8080:5000
kubectl port-forward frontend-77cbdf6f79-qsdts 8080:5000
kubectl port-forward svc/frontend-svc 8080:80
```
