# VulnerSec – Open-Source Labs for Exploitation Practice & Research

VulnerSec is a **framework for generating self-contained vulnerable environments**—virtual machines *and* containerised services—that students can attack without risking the host network.  
The project is inspired by [SecGen](https://github.com/secgen) but replaces Puppet manifests with **Ansible roles**, giving educators a cleaner, more modular workflow that spins up in minutes on anything from VirtualBox to VMware or bare-metal ESXi.

---

## 1  Why Ansible roles?

* **Modularity** – each vulnerability lives in its own role (`vulnerabilities/roles/<name>`), so you can mix, match or extend scenarios without touching the surrounding playbooks.  
* **Simplicity** – agent-less SSH execution, YAML syntax, and Galaxy-style structure make it approachable for instructors who are not full-time DevOps engineers.  
* **Isolation safeguards** – roles ship with secure-by-default settings (host-only/NAT networking, Docker seccomp & AppArmor profiles, optional read-only file systems) to prevent VM or container escape while keeping the workflow frictionless.

---

## 2  Quick Start

### 2.1  Prerequisites

| Tool | Tested version |
|------|----------------|
| Virtualisation | VirtualBox 7.0 (any hypervisor is fine) |
| Provisioning | Vagrant 2.4 |
| Config-mgmt | Ansible **10.7.0** / ansible-core 2.17 |
| Python | 3.11 with `venv` |

```
bash
sudo apt update && sudo apt install -y virtualbox vagrant python3-venv
```

### 2.2  Clone & Setup

```
git clone https://github.com/ekrajchevska/vulnersec.git
cd vulnersec
python3 -m venv venv && source venv/bin/activate
pip install -r requirements.txt
```

### 2.3  Bring up the base VM

```
vagrant up           # launches the Debian box defined in Vagrantfile
vagrant ssh          # optional: log in as the 'vagrant' user
```
The Vagrantfile already configures bridged networking (change to host-only if you need extra containment), CPU/RAM, and SSH port-forwarding; tweak as required.

---

## 3  Deploying Vulnerabilities

Every exploit is an Ansible role:

```
# example: enable the SSH-root-login misconfiguration
ansible-playbook playbooks/deploy_vulnerability.yml -e role_name=ssh_root_login
```
`deploy_vulnerability.yml` is a thin wrapper that imports the chosen role; you may also invoke roles directly if you prefer.

### 3.1  Legacy software via Docker

Some packages (e.g. UnrealIRC 3.2) won’t compile on modern distros.
Compile once on an older base image, publish to Docker Hub, then deploy:

```
# one-off: install Docker
ansible-playbook playbooks/setup_docker.yml

# run the vulnerable container with a per-scenario config file
ansible-playbook playbooks/container_template.yml -e vulnerability_config=container_configs/unrealirc.yml
```

---

## 4  Repository layout

```
vulnersec/
├── inventory/            # hosts.ini plus group_vars/ & host_vars/
├── playbooks/
│   ├── deploy_vulnerability.yml
│   ├── setup_docker.yml
│   └── container_template.yml
├── vulnerabilities/
│   └── roles/
│       ├── crackable_user_account/
│       ├── nfs_root_share/
│       └── … (>10 roles)
└── Vagrantfile
```

Each role follows the standard Ansible skeleton (`tasks/`, `templates/`, `files/`, `defaults/`).
Flags are produced by the dedicated `flag` role and dropped into predictable paths so students can prove exploitation.

---

## 5  Cleaning out

```
vagrant destroy -f    # shuts down and deletes the VM
```

---

## 6  Citation

If you use VulnerSec in academic work, please cite:
> E. Krajchevska and V. Kjorveziroski, “VulnerSec: A Flexible, Automated and Open-Source Cyber-Security Framework,” Proc. Int. Conf. Information and Communication Technologies, 2025.
