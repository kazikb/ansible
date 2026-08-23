# Ansible

This repository contains Ansible roles and playbooks for configuring supported
Debian, Ubuntu and Enterprise Linux hosts. Each role documents its requirements,
variables and usage separately.

The roles and playbooks are developed for my personal lab environment and are
provided "as is". Review and test them on disposable hosts before using them
in your own environment.

Roles
-----

| Role | Description |
| --- | --- |
| [`access_management`](roles/access_management/README.md) | Manages local users, groups, SSH access, sudo rules and the root account. |
| [`docker_engine`](roles/docker_engine/README.md) | Installs and configures Docker Engine. |
| [`host_hardening`](roles/host_hardening/README.md) | Manages OpenSSH hardening, the host firewall, sysctl parameters and kernel modules. |
| [`system_baseline`](roles/system_baseline/README.md) | Configures a common operating system baseline. |

Normal Operation
----------------

Create and activate a Python virtual environment, then install the runtime
requirements and Ansible Galaxy collections:

```bash
python3 -m venv .venv
source .venv/bin/activate
python3 -m pip install -r requirements.txt
ansible-galaxy collection install -r requirements.yml
```

Keep inventories and host variables outside the tracked repository, preferably
under `local/`, and pass the inventory explicitly with `-i`.

Development
-----------

Create and activate a Python virtual environment, then install the development
requirements and Ansible Galaxy collections:

```bash
python3 -m venv .venv
source .venv/bin/activate
python3 -m pip install -r requirements-dev.txt
ansible-galaxy collection install -r requirements.yml
```
