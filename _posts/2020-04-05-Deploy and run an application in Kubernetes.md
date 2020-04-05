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

    Cross-server container communication
    Horizontal scaling
    Service discovery
    Load balancing
    Security/TLS
    Zero-downtime deploys
    Rollbacks
    Logging
    Monitoring

This is where Kubernetes fits in along with a number of other orchestration tools -- like Docker Swarm, ECS, Mesos, and Nomad.

