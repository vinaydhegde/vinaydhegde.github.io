---
title: "Ansible on Windows"
toc: true
toc_label: "Content"
toc_sticky: true
tags:
  - Ansible

date: 2021-06-27
header:
  teaser: /assets/images/nature.jpg
excerpt: "Ansible on Windows"
---

### Overview

Ansible is an open source IT automation engine that automates provisioning, configuration management, application deployment, orchestration, and many other IT processes. 

Ansible works by connecting to your nodes and pushing out small programs, called modules to them. 

**Idempotency** - An operation is idempotent if the result of performing it once is exactly the same as the result of performing it repeatedly without any intervening actions.

**Idempotency in Ansible Playbook** - When a playbook is executed to configure a system, the system should always have the same, well defined state. If a playbook consists of 10 steps, and the system deviates in step 4 from the desired state, then only this particular step should be applied.

### Usecases

1. **Infrastructure Provisioning**

From traditional bare metal through to serverless or function-as-a-service, automating the provisioning of any infrastructure is the first step in automating the operational life cycle of your applications. Ansible can provision the latest cloud platforms, virtualized hosts and hypervisors, network devices and bare-metal servers.

2. **Configuration Management**

It's likely you currently manage your systems with a collection of scripts and ad-hoc practices curated by a talented team of administrators. Or perhaps you're using an automation framework that requires a bit too much of your time to maintain. Virtualization and cloud technology have increased the complexity and the number of systems to manage is only growing.

You need a consistent, reliable and secure way to manage the environment - but many solutions have gone way too far the other direction, actually adding complexity to an already complicated problem. You need a system that builds on existing concepts you already understand and doesn’t require a large team of developers to maintain.

3. **Application Deployment**

Ansible is the simplest way to deploy your applications. It gives you the power to deploy multi-tier applications reliably and consistently, all from one common framework. You can configure needed services as well as push application artifacts from one common system.

Rather than writing custom code to automate your systems, your team writes simple task descriptions that even the newest team member can understand on first read - saving not only up-front costs, but making it easier to react to change over time.

### Ansible Terminologies

**Control Node** Ansible Server. Any machine with Ansible installed

**Managed Node** Ansible Client. The network devices (and/or servers) you manage with Ansible. Managed nodes are also sometimes called “hosts”.

**Inventory** A list of managed nodes. An inventory file is also sometimes called a “hostfile”

**Playbook** Ordered lists of tasks, saved so you can run those tasks in that order repeatedly

**Module** The units of code Ansible executes

**Task** The units of action in Ansible. You can execute a single task once with an ad-hoc command.

**Roles** Let you automatically load related vars_files, tasks, handlers, and other Ansible artifacts based on a known file structure. Once you group your content in roles, you can easily reuse them and share them with other users.

**Ansible Vault** encrypts variables and files so you can protect sensitive content such as passwords or keys rather than leaving it visible as plaintext in playbooks or roles.

### Usage

Run adhoc commands:

```
ansible <host-pattern> -m <module-name>
#For example:
ansible localhost -m ping
```

Run a Playbook:

```
ansible-playbook -i <inventory-file> <playbook-name>
#For example:
ansible-playbook -i inventory.yml setup_build_servers.yml
```

