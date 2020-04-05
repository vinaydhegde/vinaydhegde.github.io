---
layout: post
title: "Deploy and Run an Application in Kubernetes"
date: 2020-04-05
---

In this post, we'll first take a look at Kubernetes and container orchestration in general and then we'll walk through a step-by-step tutorial that details how to deploy a Flask-based microservice (along with Postgres and React.js) to a Kubernetes cluster.

Dependencies:
* Kubernetes v1.15.0
* Minikube v1.2.0
* Docker v19.03.8
* Docker-Compose v1.17.1

## Objectives

By the end of this tutorial, you will be able to:

1. Explain what container orchestration is and why you may need to use an orchestration tool
1. Discuss the pros and cons of using Kubernetes over other orchestration tools like Docker Swarm and Elastic Container Service (ECS)
1. Explain the following Kubernetes primitives: Node, Pod, Service, Label, Deployment, Ingress, and Volume
1. Spin up a Python-based microservice locally with Docker Compose
1. Configure a Kubernetes cluster to run locally with Minikube
1. Set up a volume to hold Postgres data within a Kubernetes cluster
1. Use Kubernetes Secrets to manage sensitive information
1. Run Flask, Gunicorn, Postgres, and React on Kubernetes
1. Expose Flask and React to external users via an Ingress

## What is Container Orchestration?
As you move from deploying containers on a single machine to deploying them across a number of machines, you'll need an orchestration tool to manage (and automate) the arrangement, coordination, and availability of the containers across the entire system.

Orchestration tools help with:

1. Cross-server container communication
1. Horizontal scaling
1. Service discovery
1. Load balancing
1. Security/TLS
1. Zero-downtime deploys
1. Rollbacks
1. Logging
1. Monitoring

This is where Kubernetes fits in along with a number of other orchestration tools -- like Docker Swarm, ECS and Mesos.

Which one should you use?

1. use Kubernetes if you need to manage large, complex clusters
1. use Docker Swarm if you are just getting started and/or need to manage small to medium-sized clusters
1. use ECS if you're already using a number of AWS services

Tool| Pros| Cons
Kubernetes| large community, flexible, most features, hip| complex setup, high learning curve, hip
Docker Swarm| easy to set up, perfect for smaller clusters| limited by the Docker API
ECS| fully-managed service, integrated with AWS| vendor lock-in

There's also a number of managed Kubernetes services on the market:

1. Google Kubernetes Engine (GKE)
1. Elastic Kubernetes Service (EKS)
1. Azure Kubernetes Service (AKS)
1. DigitalOcean Kubernetes

## Kubernetes Concepts
Before diving in, let's look at some of the basic building blocks that you have to work with from the [Kubernetes API]:(https://kubernetes.io/docs/concepts/overview/kubernetes-api/)


1. A [Node](https://kubernetes.io/docs/concepts/architecture/nodes/) is a worker machine provisioned to run Kubernetes. Each Node is managed by the Kubernetes master.
1. A [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/) is a logical, tightly-coupled group of application containers that run on a Node. Containers in a Pod are deployed together and share resources (like data volumes and network addresses). Multiple Pods can run on a single Node.
1. A [Service](https://kubernetes.io/docs/concepts/services-networking/service/) is a logical set of Pods that perform a similar function. It enables load balancing and service discovery. It's an abstraction layer over the Pods; Pods are meant to be ephemeral while services are much more persistent.
1. [Deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) are used to describe the desired state of Kubernetes. They dictate how Pods are created, deployed, and replicated.
1. [Labels](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) are key/value pairs that are attached to resources (like Pods) which are used to organize related resources. You can think of them like CSS selectors. For example:
* Environment - dev, test, prod
* App version - beta, 1.2.1
* Type - client, server, db
1. [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) is a set of routing rules used to control the external access to Services based on the request host or path.
1. [Volumes](https://kubernetes.io/docs/concepts/storage/volumes/) are used to persist data beyond the life of a container. They are especially important for stateful applications like Redis and Postgres.
* A [PersistentVolume](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) defines a storage volume independent of the normal Pod-lifecycle. It's managed outside of the particular Pod that it resides in.
* A [PersistentVolumeClaim](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims) is a request to use the PersistentVolume by a user.

