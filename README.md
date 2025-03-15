## Overview

The main purpose of this project is to generate vulnerable virtual machines (VMs) intended for exploitation exercises by cybersecurity students. This project takes inspiration from the well-known [SecGen repository](https://github.com/secgen) but uses Ansible playbooks instead of Puppet for configuration—providing a more straightforward and modular way to set up the VMs and containerized vulnerabilities.

Each vulnerability is configured either directly on the VM or within a Docker container, and a flag is generated for exploitation challenges. The project is designed to be easily deployed using a hypervisor of your choice (we use VirtualBox) along with Vagrant for VM provisioning.

## Prerequisites

- **Operating System:** Linux (Debian/Ubuntu recommended)
- **Virtualization:** [VirtualBox](https://www.virtualbox.org/) (or another hypervisor)
- **Provisioning Tool:** [Vagrant](https://www.vagrantup.com/)
- **Configuration Management:** [Ansible](https://www.ansible.com/)  
  (Tested with Ansible 10.7.0 / ansible-core 2.17.7)
- **Python Virtual Environment:** Python 3 (with a `venv`, using same location as the requirements.txt file)

### Installing Dependencies

On Debian/Ubuntu, you can install VirtualBox and Vagrant using:

```bash
sudo apt-get update
sudo apt-get install -y virtualbox vagrant
```

## Project Setup

Clone the repository and set up the virtual environment

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

### Initializing the Vagrant Environment

After cloning the repository and setting up the virtual environment, initialize your Vagrant environment:

```bash
vagrant init
```

This command creates a basic Vagrantfile (or uses the provided one) along with the .vagrant folder for managing the VM state.
Modify the inventory/hosts.ini and the files in group_vars/host_vars as needed for your environment. In this example, we use a single host (testserver) on which the vulnerabilities are run and tested. Note that this structure is designed to support multiple VMs running different vulnerabilities. Be sure to set the appropriate host IP and networking settings according to your setup.

The provided Vagrantfile is pre-configured to define the VM’s networking (e.g we use bridged networking), CPU, memory, and the Debian base image version. Adjust these parameters as needed.
To start the VM, run:

```bash
vagrant up
```

### Deploying Vulnerabilities

Deploying a vulnerability is as simple as running the corresponding Ansible playbook. For example, to deploy a netcat backdoor vulnerability:

```bash
ansible-playbook vulnerabilities/netcat_backdoor/nc_backdoor_chroot_esc.yml
```

Some vulnerabilities can be easily configured regardless of the underlying OS (e.g. Crackable User Account). However, legacy exploits—such as UnrealIRC3.2, released nearly fifteen years ago—often encounter issues when compiled on modern operating systems (Debian 12) due to outdated libraries, misconfigurations, and compatibility problems. For instance, while UnrealIRC3.2 compiled successfully on Debian 8, attempts to compile it on newer distributions have failed.

The workaround is to compile these legacy vulnerabilities on an older Debian version and then containerize them. This approach isolates the vulnerable software in a Docker container, ensuring it runs consistently regardless of the host OS. It is strongly recommended to first inspect whether a vulnerability has been containerized. If it has, deploy it using the containerized configuration; if not, it likely means the vulnerability is straightforward to configure on the host.

In order to run a vulnerable container, first, set up Docker on your VM using the provided Docker module playbook:

```bash
ansible-playbook vulnerabilities/docker_module/config_docker.yml
```

A generic container template is provided. To deploy a specific vulnerability (for example, UnrealIRC3.2), use a configuration file with vulnerability-specific variables and deploy the container with:

```bash
ansible-playbook vulnerabilities/docker_module/container_template.yml -e "vulnerability_config=container_configs/unrealirc_config.yml"
```

## Using Vagrant

If you're using the `vagrant` user as the `ansible_user`, then SSH-ing into the VM is as simple as:

```bash
vagrant ssh
```

When you're done with your lab environment, you can destroy the VM with:

```bash
vagrant destroy -f
```

This command will force the VM to shut down and be removed, freeing up system resources.
