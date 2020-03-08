---
title: k8s minikube rpc error
date: "2020-03-08T22:12:03.284Z"
description: "Solving error response from daemon in minikube"
---

I started this udemy course on [Kubernetes/Microservices](https://www.udemy.com/course/kubernetes-microservices/) to get some hands on experience on k8s and devops in general. My company uses k8s to orchestrate our miroservice containers in AWS - similar to what is being offered in this course. 
I installed minikube to begin local kubernetes deployment. I also have docker on my macOS so I thought I could get away without installing a specific hypervisor but needed to install [Virtualbox](https://www.virtualbox.org/wiki/Downloads) anyway. 

workloads.yaml file
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: position-simulator
spec:
  selector:
    matchLabels:
      app: position-simulator
  replicas: 1
  template:
    metadata:
      labels:
        app: position-simulator
    spec:
      containers:
      - name: queue
        image: richardchesterwood/k8s-fleetman-position-simulator:release1
        env:
        - name: SPRING_PROFILES_ACTIVE
          value: production-microservice
```

I executed `kubectl apply -f workloads.yaml` but I got an error 
```
Failed to pull image "richardchesterwood/k8s-fleetman-position-simulator:release1": 
rpc error: code = Unknown desc = Error response from daemon: 
Get https://registry-1.docker.io/v2/: 
dial tcp: lookup registry-1.docker.io on 192.168.64.1:53: 
read udp 192.168.64.3:60508->192.168.64.1:53: read: connection refused
```

Turns out my minikube's DNS resolver was not able to lookup docker's registry. The solution is pretty straightforward.
Add a nameserver for resolution

Step 1.
`minikube ssh`

Step 2. 
`sudo vi /etc/resolv.conf`

Step 3.
Add `nameserver 8.8.8.8`
Save & Exit.

Step 4.
Test `docker pull alpine`. The image should get downloaded.

Step 5.
`kubectl delete -f workloads.yaml`

Step 6.
`kubectl apply -f workloads.yaml`

I had to repeat this everytime the existing local kubernetes cluster is deleted like I did recently to increase the 
allocate more RAM to minikube to run a heavier workload. 

