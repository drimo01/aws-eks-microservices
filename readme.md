# Project 2: Deploying Microservices on Amazon EKS

## Overview
This project focuses on deploying a containerized application using **Amazon EKS (Elastic Kubernetes Service)**. The goal was to understand how modern applications are deployed and managed using **Kubernetes**, rather than relying on traditional virtual machines.

WordPress was used as a sample application to simulate a real-world microservice that requires persistent storage, scalability, and high availability. The emphasis of this project is on **architecture, deployment workflow, and troubleshooting**, not on the website’s design.

---

## Why This Project
Many legacy systems are deployed on single servers or virtual machines, which makes them hard to scale, recover, and manage. This project demonstrates how Kubernetes and managed cloud services can solve those problems by:

- Automatically restarting failed workloads
- Scaling applications easily
- Managing infrastructure declaratively
- Separating application logic from infrastructure

---

## Architecture Overview
The solution uses the following components:

- **Amazon EKS** to manage the Kubernetes control plane
- **EC2 worker nodes** to run containerized workloads
- **Helm** to simplify application deployment
- **WordPress pods** running inside Kubernetes
- **Amazon EBS (gp3)** for persistent storage
- **Kubernetes LoadBalancer Service** to expose the application

Traffic enters through an AWS-managed Load Balancer, is routed to WordPress pods running in the EKS cluster, and persistent data is stored on dynamically provisioned EBS volumes.

An architecture diagram is included in this repository for reference.

---

## Tools & Technologies
- Amazon EKS
- Kubernetes
- Helm
- kubectl
- Amazon EC2
- Amazon EBS
- Docker (via Helm images)

---

## Deployment Summary
1. Created an EKS cluster using `eksctl`
2. Configured local access using `kubectl`
3. Deployed WordPress using Helm
4. Configured persistent storage using Amazon EBS
5. Exposed the application using a Kubernetes LoadBalancer service

---

## Challenges Encountered
During deployment, the WordPress pods remained in a **Pending** state.

### Root Cause
The issue was caused by unbound PersistentVolumeClaims. The default `gp2` StorageClass uses **Immediate volume binding**, which caused Kubernetes to attempt volume provisioning before pod scheduling was complete.

### Resolution
A new `gp3` StorageClass was created using the `WaitForFirstConsumer` binding mode. This allowed Kubernetes to provision storage only after a pod was scheduled to a node.

Once the Helm release was updated to use this StorageClass, the pods successfully transitioned to the **Running** state.

---

## Key Learnings
- How Kubernetes schedules pods and manages persistent storage
- How Helm simplifies complex Kubernetes deployments
- The importance of StorageClass configuration in EKS
- How managed services reduce operational overhead

---

## Cleanup
After completing the project, all AWS resources were deleted, including:
- EKS clusters
- Node groups
- Load balancers
- EBS volumes
- CloudFormation stacks

This ensured no unnecessary resources were left running.

---

## Conclusion
This project provided hands-on experience with Kubernetes-based application deployment on AWS. It reinforced core cloud-native concepts such as container orchestration, dynamic storage provisioning, and infrastructure cleanup best practices.

