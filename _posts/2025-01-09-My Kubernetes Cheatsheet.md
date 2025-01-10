---
title: My Kubernetes Cheatsheet
date: 2025-01-09 23:00:00 +0100
categories: [cheatsheets]
tags: [cheatsheet, kubernetes]     # TAG names should always be lowercase
description: Commands I encountered when learning about kubernetes.
---

I will use this as a cheatsheet while learning about kubernetes. I started out with minikube to set up a local kubernetes cluster.

# Minikube

## Addons in minikube
```
minikube addons list
minikube addons enable dashboard
minikube addons enable metrics-server
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
