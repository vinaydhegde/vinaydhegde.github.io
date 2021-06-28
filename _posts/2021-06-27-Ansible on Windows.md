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

- **Infrastructure Provisioning**

From traditional bare metal through to serverless or function-as-a-service, automating the provisioning of any infrastructure is the first step in automating the operational life cycle of your applications. Ansible can provision the latest cloud platforms, virtualized hosts and hypervisors, network devices and bare-metal servers.

- **Configuration Management**

It's likely you currently manage your systems with a collection of scripts and ad-hoc practices curated by a talented team of administrators. Or perhaps you're using an automation framework that requires a bit too much of your time to maintain. Virtualization and cloud technology have increased the complexity and the number of systems to manage is only growing.

You need a consistent, reliable and secure way to manage the environment - but many solutions have gone way too far the other direction, actually adding complexity to an already complicated problem. You need a system that builds on existing concepts you already understand and doesn’t require a large team of developers to maintain.

- **Application Deployment**

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
ansible-playbook -i sample_inventory.yml sample_playbook.yml
```

### Setup Ansible Control Node (Server) & Managed Nodes (Clients)

Ansible is an agentless automation tool that you install on a *control node*. From the control node, Ansible manages machines and other devices remotely (by default, over the *SSH* protocol).

*winrm* (Windows Remote Management) protocol is used (instead of *SSH*) for connecting Windows Managed Node(s) from Ansible server.
When using 'winrm' protocol, there are multiple authentication types are available like, kerberos, CredSSP, NTLM etc. Here, I am using *CredSSP* authentication.

Please note, Ansible *control node* (server) cannot be setup on *Windows*.

#### Setup Ansible Control node (Server)

- Install Ansible on a Linux server by referring to: [Install Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#installing-ansible-on-specific-operating-systems)

- Install additional python modules which are required for connecting Windows managed nodes: `pywinrm` & `pywinrm[credssp]`
```
pip install "pywinrm>=0.3.0"
pip install pywinrm[credssp]
```

- Set below variables in host/inventory file or in the Playbook directly
```
ansible_user: <Username>
ansible_password: <Password>
ansible_connection: winrm
ansible_winrm_transport: credssp
```

#### Setup Windows Managed nodes(clients)

- **WinRM** setup

WinRM service has to be configured on clients so that Ansible control node can connect to it.

There are two main components of the WinRM service that governs how Ansible can interface with the Windows host: the `listener` and the `service` configuration settings.

Run below powershell script (from client machine) to configure WinRM service

```
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
$url = "https://raw.githubusercontent.com/ansible/ansible/devel/examples/scripts/ConfigureRemotingForAnsible.ps1"
$file = "$env:temp\ConfigureRemotingForAnsible.ps1"

(New-Object -TypeName System.Net.WebClient).DownloadFile($url, $file)

powershell.exe -ExecutionPolicy ByPass -File $file
```

- Enable **CredSSP**

*CredSSP* authentication is not enabled by default on a Windows host, but can be enabled by

`Enable-WSManCredSSP -Role Server -Force`

Now, both Control Node & Managed node(s) are setup. You can run an Ansible ad-hoc command to test it. For example:

`ansible localhost -m win_ping`

### What is Ansible Vault and How it is Setup & Used

Ansible Vault encrypts variables and files so you can protect sensitive content such as passwords or keys rather than leaving it visible as plaintext in playbooks or roles.

- Create a new encrypted file

```
ansible-vault create <vault_file>
```

You will be prompted to enter and confirm password.

Output would look like,

```
New Vault password:
Confirm New Vault password:
```

> NOTE: This is NOT the password/secret you are going to encrypt. This is the password used to open/view/edit the encrypted file which will contain your secrets. Your actual secrets are added in the next action. To automatically accept the password to read/access the encrypted file (without prompting for the password), we can create a file with password in it. Usage is explained in the next section.

- Once you confirm your password, Ansible will immediately open an editing window where you can enter your desired contents. For example:

```
db_passwd: <password>
```

- You can view the contents of existing encrypted file (which would contain variables for your password/secrets)

```
ansible-vault view <vault_file>
#It will ask to enter the password
```

- Edit/update the content of existing encrypted file (which would contain variables for your password/secrets)

```
ansible-vault edit <vault_file>
#It will ask to enter the password
```

- Automatically accept the password (without prompting for the password), to read/access encrypted file.

Create a file & add the password, which will be used to read/access encrypted file. Preferably, it should be readonly to the owner (should be readable to other users). For example:

```
$ cat vault_passwd_file 
my_password
```

- Run a Playbook which uses vault credentials/content

```
ansible-playbook <playbook> -i <inventory> --vault-password-file vault_passwd_file
```

Here, the playbook would use the secrets mentioned in the encrypted file which are decrypted using vault password file.

> Note: Which encrypted file has to be referred will be mentioned in the playbook. 


### Popular Ansible modules for Windows

Typically, windows module names will start with `win_`

Some of the popular Ansible modules for Windows are:

- **win_regedit** 	Add, modify or remove registry keys and values
- **win_reg_stat**	whether the key/property exists
- **win_psmodule**	Install Windows PowerShell modules
- **win_package**	Installs or uninstalls software packages for Windows
- **win_shel**l	Execute shell commands on target hosts
- **win_command**	Executes a command on a remote Windows node
- **win_group_membership**	Manage Windows local group membership
- **win_unzip**	Unzips compressed files and archives on the Windows node
- **win_timzone**	Sets Windows machine timezone
- **win_path**	Manage Windows path environment variables
- **win_environment** Modify environment variables on windows hosts
- **win_whoami**	Get information about the current user and process







