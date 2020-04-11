---
layout: post
title: "Deploy and Run a microservice in Kubernetes"
date: 2020-04-05
---

In this blog, I have tried to explain the basics of Kubernetes & Container Orchestration in gneral and then explained how to deploy a Flask-based microservice (along with Postgres and React.js) to a Kubernetes cluster.

Dependencies:

- Docker Engine - CE: Client: v19.03.8, Server: v19.03.8
- Kubectl: v1.18.0
- Minikube: v1.9.2
- Oracle VirtualBox: v6.1


{% if page.toc %}
  {% include toc.html %}
{% endif %}

## Objectives

1. Understand what is container orchestration
1. Pros and cons of using Kubernetes over other orchestration tools like Docker Swarm and Elastic Container Service (ECS)
1. Kubernetes primitives - Node, Pod, Service, Label, Deployment, Ingress, and Volume
1. Spin up a Flask-based microservice locally with Docker Compose
1. Configure a Kubernetes cluster to run locally with Minikube
1. Use Kubernetes Secrets to manage sensitive information
1. Set up a volume to hold Postgres data within a Kubernetes cluster
1. Run Flask, Gunicorn, Postgres, and React on Kubernetes
1. Expose Flask and React to external users via an Ingress

## What is Container Orchestration?
Container orchestration is automated configuration, coordination, and management of Containers.
As you move from deploying containers on a single machine to deploying them across a number of machines, you'll need an orchestration tool to manage (and automate) the arrangement, coordination, and availability of the containers across the entire system.

Orchestration tools helps in:

1. Cross-server container communication
1. Horizontal scaling
1. Service discovery
1. Load balancing
1. Security/TLS
1. Zero-downtime deploys
1. Rollbacks
1. Logging
1. Monitoring

This is where [Kubernetes](https://kubernetes.io/) fits in along with a number of other orchestration tools  - like [Docker Swarm](https://docs.docker.com/engine/swarm/), [ECS](https://aws.amazon.com/ecs/) and [Mesos](http://mesos.apache.org/).

Which one should you use?

- use *Kubernetes* if you need to manage large, complex clusters
- use *Docker Swarm* if you are just getting started and/or need to manage small to medium-sized clusters
- use *ECS* if you're already using a number of AWS services

| Tool         | Pros                                          | Cons                                    |
|--------------|-----------------------------------------------|-----------------------------------------|
| Kubernetes   | large community, flexible, most features, hip | complex setup, high learning curve, hip |
| Docker Swarm | easy to set up, perfect for smaller clusters  | limited by the Docker API               |
| ECS          | fully-managed service, integrated with AWS    | vendor lock-in                          |

There's also a number of Kubernetes services offered by deifferent vendors

1. [Elastic Container Service](https://aws.amazon.com/eks/) (EKS)
1. [Google Kubernetes Engine](https://cloud.google.com/kubernetes-engine/) (GKE)
1. [Azure Kubernetes Service](https://azure.microsoft.com/en-us/services/kubernetes-service/) (AKS)

## Kubernetes
Kubernetes is a portable, extensible, open-source platform for managing containerized workloads and services, that facilitates both declarative configuration and automation.
The name Kubernetes originates from Greek, meaning helmsman or pilot. Google open-sourced the Kubernetes project in 2014

## Kubernetes Architecture (Components of Kubernetes)
- The *master* node, which hosts the *Kubernetes Control Plane* that controls and managaes the whole Kubernetes system.
 It runs 3 processes: kube-apiserver, kube-controller-manager and kube-scheduler.
- Worker *nodes* that run the actual applications you deploy. It runs 2 processes: kubelet (which communicates with  the Kuberenetes master) and kube-proxy.


<img src="/images/k8s/kubernetes-components.png" style="max-width:80%;padding-bottom:20px;" alt="Kubernetes Components">
To knowmore about each component, refer: https://kubernetes.io/docs/concepts/overview/components/

## Kubernetes Concepts
To work with Kubernetes, you use *Kubernetes API objects* to describe your cluster’s desired state: what applications or other workloads you want to run, what container images they use, the number of replicas, what network and disk resources you want to make available, and more. You set your desired state by creating objects using the Kubernetes API, typically via the command-line interface, *kubectl*

The basic Kubernetes objects include:
1. **[Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/)** is a logical, tightly-coupled group of application containers that run on a Node. Containers in a Pod are deployed together and share resources (like data volumes and network addresses). Multiple Pods can run on a single Node.
1. **[Service](https://kubernetes.io/docs/concepts/services-networking/service/)** is a logical set of Pods that perform a similar function. It enables load balancing and service discovery. It's an abstraction layer over the Pods; Pods are meant to be ephemeral while services are much more persistent.
1. **[Volumes](https://kubernetes.io/docs/concepts/storage/volumes/)** are used to persist data beyond the life of a container. They are especially important for stateful applications like Redis and Postgres.
    - A *[PersistentVolume](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)* defines a storage volume independent of the normal Pod-lifecycle. It's managed outside of the particular Pod that it resides in.
    - A *[PersistentVolumeClaim](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims)* is a request to use the PersistentVolume by a user.
1. **[Namespace](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)**: Kubernetes supports multiple virtual clusters backed by the same physical cluster. These virtual clusters are called namespaces    
    
Kubernetes also contains higher-level abstractions that rely on controllers to build upon the basic objects, and provide additional functionality and convenience features. These include:

1. **[Deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)** are used to describe the desired state of Kubernetes. They dictate how Pods are created, deployed, and replicated.
1. **[DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)** A DaemonSet ensures that all (or some) Nodes run a copy of a Pod.
1. **[StatefulSets](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/)**: Manages the deployment and scaling of a set of Pods, and provides guarantees about the ordering and uniqueness of these Pods.
1. **[ReplicaSet](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/)**: 
Before diving in, let's look at some of the basic building blocks that you have to work with from the [Kubernetes API](https://kubernetes.io/docs/concepts/overview/kubernetes-api/): A ReplicaSet’s purpose is to maintain a stable set of replica Pods running at any given time
1. **[Jobs](https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/)**: A Job creates one or more Pods and ensures that a specified number of them successfully terminate

Other objects/metadata for the objects include:
1. **[Labels](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/)** Labels are key/value pairs that are attached to objects, such as pods.
1. **[Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/)** An API object that manages external access to the services in a cluster, typically HTTP. Ingress may provide load balancing, SSL termination and name-based virtual hosting

Now, It's time to look at the App deployment part, which is the main purpose of this blog.

## App Overview
**DevopsTree**: Flask + React + Postgres app (currently, it only displays the information. You cannot edit/update).
It displays major tools & technolgies used in Devops & it's dependencies in a Tree pattern.


<img src="/images/k8s/devopstree.gif" style="max-width:80%;padding-bottom:20px;" alt="DevopsTree App">


## Project Setup

Clone the Github repo: [DevopsTree-Kubernetes](https://github.com/vinaydhegde/DevopsTree-Kubernetes)

```sh
$ git clone https://github.com/vinaydhegde/DevopsTree-Kubernetes ~/DevopsTree-Kubernetes
```

Below is the code structure you see when you clone the repo :

```sh
├── README.md
├── k8s
│   ├── devopstree-secret.yml
│   ├── devopstree-deployment-postgres.yml
│   ├── devopstree-service-postgres.yml
│   ├── devopstree-minikube-ingress.yml
│   ├── devopstree-pv.yml
│   ├── devopstree-pvc.yml
│   ├── devopstree-deployment-flask.yml
│   ├── devopstree-service-flask.yml
│   ├── devopstree-deployment-react.yml
│   └── devopstree-service-react.yml
└── services
    ├── client
    │   ├── Dockerfile
    │   ├── Dockerfile-k8s
    │   ├── README.md
    │   ├── node_modules
    |   ├── public
    │   ├── package.json
    │   ├── src
    │   │   ├── App.js
    │   │   ├── index.js
    │   │   ├── utils.js
    │   │   ├── Tree
    │   │       ├── index.js
    ├── db
    │   ├── create_database.sql
    │   └── dockerfile
    └── server
        ├── dockerfile
        ├── entrypoint.sh
        ├── manage.py
        ├── project
        │   ├── __init__.py
        │   ├── api
        │   │   ├── __init__.py
        │   │   ├── devopstree.py
        │   │   └── models.py
        │   └── config.py
        └── requirements.txt
```

## Minikube

[Minikube](https://kubernetes.io/docs/setup/minikube/) is a tool which allows developers to use and run a Kubernetes cluster locally. It's a great way to quickly get a cluster up and running so you can start interacting with the Kubernetes API.
```sh
# For Ubuntu 
$ curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 && sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

The command above is to install Minikube on Ubuntu. You can follow the official [quickstart](https://github.com/kubernetes/minikube#quickstart) guide to get Minikube installed for other OS platforms.

Along with this, you also need to install,

1. A [Hypervisor](https://kubernetes.io/docs/tasks/tools/install-minikube/#install-a-hypervisor) (like [VirtualBox](https://www.virtualbox.org/wiki/Downloads) or [HyperKit](https://github.com/moby/hyperkit)) to manage virtual machines

1. [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) to deploy and manage apps on Kubernetes
```sh
$ curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
$ chmod +x ./kubectl
$ sudo mv ./kubectl /usr/local/bin/kubectl
$ kubectl version --client
```

Start Minikube & create a cluster
```sh
$ minikube start
```

Access Minikube [dashboard](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/):
```sh
$ minikube dashboard
```

<img src="/images/k8s/minikube-dashboard.png" style="max-width:80%;padding-bottom:20px;" alt="Minikube Dashboard">

> Note: Config files will be located in the *~/.kube* directory and all the virtual machine bits will be in the *~/.minikube* directory.

Now let's start creating objects via the Kubernetes API.

## Creating Objects

To create a new [object](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/) in Kubernetes, you must provide a "spec" that describes its desired state.


Example:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: devopstree-react
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: devopstree-react
    spec:
      containers:
      - name: devopstree-react
      - image: vinaydhegde/devopstree-react:latest
```

Required Fields:

1. `apiVersion` - [Kubernetes API](https://kubernetes.io/docs/reference/#api-reference) version
1. `kind` - the type of object you want to create
1. `metadata` - info about the object so that it can be uniquely identified
1. `spec` - desired state of the object

In the above example, this spec will create a new Deployment called *devopstree-react* with a single replica (Pod). Take note of the `containers` section. Here, we specified the Docker image to pull from the docker registry.

We are going to create the following objects in Kubernetes:

<img src="/images/k8s/objects.png" style="max-width:80%;padding-bottom:20px;" alt="objects">

## Volume: db-pv

As, containers are ephemeral, we need to configure a volume, via a [PersistentVolume](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistent-volumes) and a [PersistentVolumeClaim](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims), to store the Postgres data outside of the Pod.

Take note of the YAML file in *k8s/devopstree-pv.yml*:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: db-pv
  labels:
    type: local
spec:
  capacity:
    storage: 2Gi
  storageClassName: standard
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/data/db-pv"
```

This configuration will create a [hostPath](https://kubernetes.io/docs/concepts/storage/volumes/#hostpath) PersistentVolume at "/data/db-pv" within the Node. The size of the volume is 2 gibibytes with an access mode of [ReadWriteOnce](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes), which means that the volume can be mounted as read-write by a single node.

> Note: Kubernetes only supports using a hostPath on a single-node cluster.

Create the volume:

```sh
$ kubectl apply -f ./k8s/devopstree-pv.yml
```

View details:

```sh
$ kubectl get pv

NAME    CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
db-pv   2Gi        RWO            Retain           Available           standard                5s
```

You can also see this object in the dashboard:

<img src="/images/k8s/pv.png" style="max-width:80%;padding-bottom:20px;" alt="peristent-volume">

*k8s/devopstree-pvc.yml*:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: db-pvc
  labels:
    type: local
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 2Gi
  volumeName: db-pv
  storageClassName: standard
```

## Create the volume claim: dp-pvc

```sh
$ kubectl apply -f ./k8s/devopstree-pvc.yml
```

View details:

```sh
$ kubectl get pvc

NAME     STATUS    VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   AGE
db-pvc   Pending   db-pv    0                         standard       9s
```

<img src="/images/k8s/pvc.png" style="max-width:80%;padding-bottom:20px;" alt="peristent-volume-claim">

## Secrets: postgres-credentials

[Secrets](https://kubernetes.io/docs/concepts/configuration/secret/) are used to handle sensitive info such as passwords, API tokens, and SSH keys. We'll set up a Secret to store our Postgres database credentials.

*k8s/devopstree-secret.yml*:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: postgres-credentials
type: Opaque
data:
  user: ZGV2b3BzdHJlZS1kZW1v
  password: ZGVtb0AxMjM=
```

The `user` and `password` fields are base64 encoded strings ([security via obscurity](https://en.wikipedia.org/wiki/Security_through_obscurity)):

For example:
```sh
$ echo -n "<your_password_to_encode>" | base64
eW91cl9wYXNzd29yZF90b19lbmNvZGU=
```

> Note: Any user with access to the cluster will be able to read the values in plaintext. Use `vault` if you want to encrypt secrets in transit and at rest.

Add the Secrets object:

```sh
$ kubectl apply -f ./k8s/devopstree-secret.yml
```

<img src="/images/k8s/secret.png" style="max-width:80%;padding-bottom:20px;" alt="Secret object">

## POSTGRES

## Deployment: devopstree-deployment-postgres

With the volume and database credentials set up in the cluster, we can now configure the Postgres database itself.

*k8s/devopstree-deployment-postgres.yml*:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    name: database
  name: devopstree-postgres
spec:
  progressDeadlineSeconds: 2147483647
  replicas: 1
  selector:
    matchLabels:
      service: devopstree-postgres
  template:
    metadata:
      creationTimestamp: null
      labels:
        service: devopstree-postgres
    spec:
      containers:
      - name: devopstree-postgres
        image: postgres:12.2-alpine
        env:
          - name: POSTGRES_USER
            valueFrom:
              secretKeyRef:
                name: postgres-credentials
                key: user
          - name: POSTGRES_PASSWORD
            valueFrom:
              secretKeyRef:
                name: postgres-credentials
                key: password
        volumeMounts:
        - mountPath: /var/lib/postgresql/data
          name: postgres-volume-mount
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
      volumes:
      - name: postgres-volume-mount
        persistentVolumeClaim:
          claimName: db-pvcc
```

Let's understand what the above configuration does:

1. `metadata`
    - The `name` field defines the Deployment name - `devopstree-postgres`
    - `labels` define the labels for the Deployment - `name: database`
1. `spec`
    - `replicas` define the number of Pods to run - `1`
    - `template`
        - `metadata`
            - `labels` indicate which labels should be assigned to the Pod - `service: devopstree-postgres`
        - `spec`
            - `containers` define the containers associated with each Pod
            - `volumes` define the volume claim - `postgres-volume-mount`
            - `restartPolicy` defines the [restart policy](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/) - `Always`

Here, the Pod name is also `devopstree-postgres` but it can be different than the service name (You will see name prefix *devopstree* in many places in almost all configuration files. So don't get confused). 
The docker image name is `postgres:12.2-alpine`, which will be pulled from Docker Hub. The database credentials, from the Secret object, are passed in as well.

Finally, when applied, the volume claim will be mounted into the Pod. The claim is mounted to "/var/lib/postgresql/data" - the default location - while the data will be stored in the PersistentVolume, "/data/db-pv".

Create the Deployment:

```sh
$ kubectl create -f ./k8s/devopstree-deployment-postgres.yml
```

Status:

```sh
$ kubectl get deployments

NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
devopstree-postgres   0/1     1            0           17s
```

*k8s/devopstree-service-postgres.yml*:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: devopstree-postgres
  labels:
    service: devopstree-postgres
spec:
  selector:
    service: devopstree-postgres
  type: ClusterIP
  ports:
  - port: 5432
```

Let's understand what the above configuration does:

1. `metadata`
    - The `name` field defines the Service name - `devopstree-postgres`
    - `labels` define the labels for the Service - `name: database`
1. `spec`
    - `selector` defines the Pod label and value that the Service applies to - `service: devopstree-postgres`
    - `type` defines the [type](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types) of Service - `ClusterIP`
    - `ports`
        - `port` defines the port exposed to the cluster


> Take a moment to go back to the Deployment spec. How does the `selector` in the Service relate back to the Deployment?

Since the [Service type](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types) is `ClusterIP`, it's not exposed externally, so it's *only* accessible from within the cluster by other objects.

Create the service:

```sh
$ kubectl create -f ./k8s/postgres-service.yml
```

Create the `devops` database, using the Pod name:

```sh
$ kubectl get pods

NAME                        READY     STATUS    RESTARTS   AGE
postgres-6fb596d5b-mqmzz    1/1       Running   0          6m

$ kubectl exec postgres-6fb596d5b-mqmzz --stdin --tty -- createdb -U postgres devops
```

Verify the creation:

```sh
kubectl exec postgres-6fb596d5b-mqmzz --stdin --tty -- psql -U sample devops

psql (10.4)
Type "help" for help.

postgress=# \l
                                 List of databases
   Name    |  Owner   | Encoding |  Collate   |   Ctype    |   Access privileges
-----------+----------+----------+------------+------------+-----------------------
 devops    | postgres | UTF8     | en_US.utf8 | en_US.utf8 |

postgres=#
```

> You can also get the Pod name via:
>
> ```sh
> $ kubectl get pod -l service=postgres -o jsonpath="{.items[0].metadata.name}"
> ```
>
> Assign the value to a variable and then create the database:
> ```sh
> $ POD_NAME=$(kubectl get pod -l service=postgres -o jsonpath="{.items[0].metadata.name}")
> $ kubectl exec $POD_NAME --stdin --tty -- createdb -U postgres devops
> ```

## Flask

Take a moment to review the Flask project structure along with the *dockerfile* and the *entrypoint.sh* files:

1. "services/server"
1. *services/server/dockerfile*
1. *services/server/entrypoint.sh*

*k8s/flask-deployment.yml*:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    name: flask
  name: flask
spec:
  progressDeadlineSeconds: 2147483647
  replicas: 1
  selector:
    matchLabels:
      app: flask
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: flask
    spec:
      containers:
      - env:
        - name: FLASK_ENV
          value: development
        - name: APP_SETTINGS
          value: project.config.DevelopmentConfig
        - name: POSTGRES_USER
          valueFrom:
            secretKeyRef:
              key: user
              name: postgres-credentials
        - name: POSTGRES_PASSWORD
          valueFrom:
            secretKeyRef:
              key: password
              name: postgres-credentials
        image: vinaydhegde/flask-kubernetes:latest
        imagePullPolicy: Always
        name: flask
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
```

This should look similar to the Postgres Deployment spec. The big difference is that you can either use my pre-built and pre-pushed image on [Docker Hub](https://hub.docker.com/), `vinaydhegde/flask-kubernetes`, or build and push your own.

For example:

```sh
$ docker build -t <YOUR_DOCKER_HUB_NAME>/flask-kubernetes ./services/server
$ docker push <YOUR_DOCKER_HUB_NAME>/flask-kubernetes
```

If you use your own, make sure you replace `vinaydhegde` with your Docker Hub name in *k8s/flask-deployment.yml* as well.

> Alternatively, if don't want to push the image to a Docker registry, after you build the image locally, you can set the `image-pull-policy` flag to `Never` to always use the local image.

Create the Deployment:

```sh
$ kubectl create -f ./k8s/flask-deployment.yml
```

<img src="/assets/img/blog/flask-kubernetes/minikube-dashboard-pg-deployments.png" style="max-width:80%;padding-top:20px;padding-bottom:20px;" alt="minikube dashboard">

This will immediately spin up a new Pod:

<img src="/assets/img/blog/flask-kubernetes/minikube-dashboard-pg-pod.png" style="max-width:80%;padding-top:20px;padding-bottom:20px;" alt="minikube dashboard">

*kubernetes/flask-service.yml*:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: flask
  labels:
    service: flask
spec:
  selector:
    app: flask
  ports:
  - port: 5000
    targetPort: 5000
```

> Curious about the `targetPort` and how it relates to the `port`? Review the offical [Services](https://kubernetes.io/docs/concepts/services-networking/service/#defining-a-service) guide.

Create the service:

```sh
$ kubectl create -f ./k8s/flask-service.yml
```

Make sure the Pod is associated with the Service:

<img src="/assets/img/blog/flask-kubernetes/minikube-dashboard-pg-service.png" style="max-width:80%;padding-top:20px;padding-bottom:20px;" alt="minikube dashboard">

Apply the migrations and seed the database:

```sh
$ kubectl get pods

NAME                        READY     STATUS    RESTARTS   AGE
flask-6844c9569-5znvr       1/1       Running   0          28m
postgres-6fb596d5b-mqmzz    1/1       Running   0          40m
```

```h
$ kubectl exec flask-6844c9569-5znvr --stdin --tty -- python manage.py recreate_db
$ kubectl exec flask-6844c9569-5znvr --stdin --tty -- python manage.py seed_db
```

Verify:

```sh
$ kubectl exec postgres-6fb596d5b-mqmzz --stdin --tty -- psql -U postgres

postgres=# \c devops
You are now connected to database "devops" as user "postgres".
devops=# select * from devops;
```

## Ingress

To enable traffic to access the Flask API inside the cluster, you can use either a NodePort, LoadBalancer, or Ingress:

1. A [NodePort](https://kubernetes.io/docs/concepts/services-networking/service/#nodeport) exposes a Service on an open port on the Node.
1. As the name implies, a [LoadBalancer](https://kubernetes.io/docs/concepts/services-networking/service/#loadbalancer) creates an external load balancer that points to a Service in the cluster.
1. Unlike the previous two methods, an [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) is not a type of Service; instead, it sits on top of the Services as an entry point into the cluster.

> For more, review the official [Publishing Services](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types) guide.

*k8s/minikube-ingress.yml*:

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: minikube-ingress
  annotations:
spec:
  rules:
  - host: devops-tree
    http:
      paths:
      - path: /
        backend:
          serviceName: react
          servicePort: 8080
      - path: /devops
        backend:
          serviceName: flask
          servicePort: 5000
```

Here, we defined the following HTTP rules:

1. `/` - routes requests to the React Service (which we still need to set up)
1. `/devops` - routes requests to the Flask Service

Enable the Ingress [addon](https://github.com/kubernetes/minikube/tree/master/deploy/addons/ingress):

```sh
$ minikube addons enable ingress
```

Create the Ingress object:

```sh
$ kubectl apply -f ./k8s/minikube-ingress.yml
```

Next, you need to update your */etc/hosts* file to route requests from the host we defined, `devops-tree`, to the Minikube instance.

Add an entry to */etc/hosts*:

```sh
$ echo "$(minikube ip) devops-tree" | sudo tee -a /etc/hosts
```

Try it out:

1. http://devops-tree/devops

    ```json
    {
    "name": "Devops",
    "tooltip": "Devops tree",
    "children": [
      {
        "name": "DBs",
        "tooltip":"Databases",
        "children": [
          {
            "name": "Oracle DB",
            "tooltip": "From Oracle"
          },
          {
            "name": "PostgreSql",
            "tooltip": "open source object-relational database system"
          },
          {
            "name": "MongoDB",
            "tooltip": "NoSQL database program"
          }
        ]
  
      },
    ]
 }
```

## React

Moving right along, review the React project along with the associated Dockerfiles:

1. "services/client"
1. */services/client/Dockerfile*
1. */services/client/Dockerfile-minikube*

*k8s/react-deployment.yml*:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    name: react
  name: react
spec:
  progressDeadlineSeconds: 2147483647
  replicas: 1
  selector:
    matchLabels:
      app: react
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: react
    spec:
      containers:
      - image: vinaydhegde/react-kubernetes:latest
        imagePullPolicy: Always
        name: react
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
```

Again, either use my image or build and push your own image to Docker Hub:

```sh
$ docker build -t <YOUR_DOCKERHUB_NAME>/react-kubernetes ./services/client \
    -f ./services/client/Dockerfile-minikube
$ docker push <YOUR_DOCKERHUB_NAME>/react-kubernetes
```

Create the Deployment:

```sh
$ kubectl create -f ./k8s/react-deployment.yml
```

Verify that a Pod was created along with the Deployment:

```sh
$ kubectl get deployments react
NAME      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
react     1         1         1            1           4m

$ kubectl get pods
NAME                        READY     STATUS    RESTARTS   AGE
flask-6844c9569-5znvr       1/1       Running   0          5h
postgres-6fb596d5b-mqmzz    1/1       Running   0          8h
react-5d56c9549f-jgjhk      1/1       Running   0          9m
```

> How would you verify that the Pod and Deployment were created successfully in the dashboard?

*k8s/react-service.yml*:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: react
  labels:
    service: react
  name: react
spec:
  selector:
    app: react
  ports:
  - port: 8080
    targetPort: 8080
```

Create the service:

```sh
$ kubectl create -f ./k8s/react-service.yml
```

Ensure [http://devops-tree/](http://devops-tree/) works as expected.

<img src="/assets/img/blog/flask-kubernetes/bookshelf.png" style="max-width:80%;padding-top:20px;padding-bottom:10px;" alt="devops-tree app">

## Scaling

Kubernetes makes it easy to scale, adding additional Pods as necessary, when the traffic load becomes too much for a single Pod to handle.

For example, let's add another Flask Pod to the cluster:

```sh
$ kubectl scale deployment flask --replicas=2
```

Confirm:

```sh
$ kubectl get deployments flask

NAME      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
flask     2         2         2            2           45m
```

```sh
$ kubectl get pods -o wide

NAME                        READY     STATUS    RESTARTS   AGE       IP           NODE
flask-6844c9569-5znvr       1/1       Running   0          20s       172.17.0.12  minikube
flask-6844c9569-4xmws       1/1       Running   0          4h        172.17.0.13  minikube
postgres-6fb596d5b-mqmzz    1/1       Running   0          9h        172.17.0.3   minikube
react-5d56c9549f-jgjhk      1/1       Running   0          14m       172.17.0.4   minikube
```

Make a few requests to the service:

```sh
$ for ((i=1;i<=10;i++)); do curl http://devops-tree/devops; done
```

You should see different `container_id`s being returned, indicating that requests are being routed appropriately via a round robin algorithm between the two replicas:

```sh
{"container_id":"flask-6844c9569-5znvr"}
{"container_id":"flask-6844c9569-4xmws"}
{"container_id":"flask-6844c9569-5znvr"}
```

What happens if you scale down as traffic is hitting the cluster? Open two terminal windows and test this on your on. You should see traffic being re-routed appropriately. Try it again, but this time scale up.

## Helpful Commands

| Command                     | Explanation                                          |
|-----------------------------|------------------------------------------------------|
| `minikube get-k8s-versions` | Lists the supported Kubernetes versions              |
| `minikube start`            | Starts a local Kubernetes cluster                    |
| `minikube ip`               | Displays the IP address of the cluster               |
| `minikube dashboard`        | Opens the Kubernetes dashboard in your browser       |
| `kubectl version`           | Displays the Kubectl version                         |
| `kubectl cluster-info`      | Displays the cluster info                            |
| `kubectl get nodes`         | Lists the Nodes                                      |
| `kubectl get pods`          | Lists the Pods                                       |
| `kubectl get deployments`   | Lists the Deployments                                |
| `kubectl get services`      | Lists the Services                                   |
| `minikube stop`             | Stops a local Kubernetes cluster                     |
| `minikube delete`           | Removes a local Kubernetes cluster                   |

> Check out the [Kubernetes Cheatsheet](https://github.com/dennyzhang/cheatsheet-kubernetes-A4) for more commands.

## Automation Script

Ready to put everything together? Let’s write a script that will:

1. Create a PersistentVolume and a PersistentVolumeClaim
1. Add the database credentials via Kubernetes Secrets
1. Create the Postgres Deployment and Service
1. Create the Flask Deployment and Service
1. Enable Ingress
1. Apply the Ingress rules
1. Create the Vue Deployment and Service


Add a new file called *deploy.sh* to the project root:

```sh
#!/bin/bash


echo "Creating the volume..."

kubectl apply -f ./k8s/persistent-volume.yml
kubectl apply -f ./k8s/persistent-volume-claim.yml


echo "Creating the database credentials..."

kubectl apply -f ./k8s/secret.yml


echo "Creating the postgres deployment and service..."

kubectl create -f ./k8s/postgres-deployment.yml
kubectl create -f ./k8s/postgres-service.yml
POD_NAME=$(kubectl get pod -l service=postgres -o jsonpath="{.items[0].metadata.name}")
kubectl exec $POD_NAME --stdin --tty -- createdb -U postgres devops


echo "Creating the flask deployment and service..."

kubectl create -f ./k8s/flask-deployment.yml
kubectl create -f ./k8s/flask-service.yml
FLASK_POD_NAME=$(kubectl get pod -l app=flask -o jsonpath="{.items[0].metadata.name}")
kubectl exec $FLASK_POD_NAME --stdin --tty -- python manage.py recreate_db
kubectl exec $FLASK_POD_NAME --stdin --tty -- python manage.py seed_db


echo "Adding the ingress..."

minikube addons enable ingress
kubectl apply -f ./k8s/minikube-ingress.yml


echo "Creating the React deployment and service..."

kubectl create -f ./k8s/react-deployment.yml
kubectl create -f ./k8s/react-service.yml
```

Try it out!

```sh
$ sh deploy.sh
```

Once done, create the `devops` database, apply the migrations, and seed the database:

```sh
$ POD_NAME=$(kubectl get pod -l service=postgres -o jsonpath="{.items[0].metadata.name}")
$ kubectl exec $POD_NAME --stdin --tty -- createdb -U postgres devops

$ FLASK_POD_NAME=$(kubectl get pod -l app=flask -o jsonpath="{.items[0].metadata.name}")
$ kubectl exec $FLASK_POD_NAME --stdin --tty -- python manage.py recreate_db
$ kubectl exec $FLASK_POD_NAME --stdin --tty -- python manage.py seed_db
```

Update */etc/hosts*, and then test it out in the browser.

## Conclusion

In this post we looked at how to run a Flask-based microservice on Kubernetes.

At this point, you should have a basic understanding of how Kubernetes works and be able to deploy a cluster with an app running on it.

Additional Resources:

1. [Learn Kubernetes Basics](https://kubernetes.io/docs/tutorials/kubernetes-basics/)
1. [Configuration Best Practices](https://kubernetes.io/docs/concepts/configuration/overview/)
1. [Deploy and Run an Application in Kubernetes](https://vinaydhegde.github.io/)


You can find the code in the [devops-tree-k8s](https://github.com/vinaydhegde/devops-tree-k8s) repo on GitHub.
