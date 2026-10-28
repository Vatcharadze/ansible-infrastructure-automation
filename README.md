# Ansible Infrastructure Automation

An Ansible project that automatically configures a server: it prepares the base environment, installs Docker, runs Nginx, and sets up monitoring (Prometheus).

## Table of Contents

- [Features](#features)
- [Project Structure](#project-structure)
- [Requirements](#requirements)
- [Usage](#usage)
- [Roles](#roles)
- [Configuration](#configuration)
- [Verification](#verification)
- [License](#license)

## Features

- **Common**: base server configuration
- **Docker**: automated Docker installation and startup
- **Nginx**: web server setup serving a static page
- **Monitoring**: server monitoring with Prometheus
- Idempotent playbook: re-running it does not break an already configured server

## Project Structure

```
.
├── ansible.cfg              # Core Ansible configuration
├── requirements.yml         # Required Ansible collections/roles
├── inventory
│   └── hosts.ini            # List of target servers
├── playbooks
│   └── site.yml             # Main playbook
└── roles
    ├── common               # Base server setup
    │   ├── defaults/main.yml
    │   └── tasks/main.yml
    ├── docker               # Docker installation
    │   ├── defaults/main.yml
    │   ├── handlers/main.yml
    │   ├── tasks/main.yml
    │   └── vars/main.yml
    ├── nginx                # Nginx setup
    │   ├── defaults/main.yml
    │   ├── files/index.html
    │   └── tasks/main.yml
    └── monitoring           # Monitoring setup
        ├── defaults/main.yml
        ├── files/prometheus.yml
        └── tasks/main.yml
```

## Requirements

**Control node (where Ansible runs):**

- Ansible 2.14+
- Python 3
- SSH access to the target server

**Target server:**

- Linux (Ubuntu/Debian recommended)
- SSH access with `sudo` privileges
- Python 3

## Usage

### 1. Clone the repository

```bash
git clone https://github.com/Vatcharadze/ansible-infrastructure-automation.git
cd ansible-infrastructure-automation
```

### 2. Install dependencies

```bash
ansible-galaxy install -r requirements.yml
```

### 3. Configure the inventory

Open `inventory/hosts.ini` and add your server details:

```ini
[servers]
my-server ansible_host=YOUR_SERVER_IP ansible_user=YOUR_USER
```

### 4. Test connectivity

```bash
ansible all -m ping
```

### 5. Run the playbook

```bash
ansible-playbook playbooks/site.yml
```

If `sudo` requires a password:

```bash
ansible-playbook playbooks/site.yml --ask-become-pass
```

Run only a specific role (if tags are defined in the playbook):

```bash
ansible-playbook playbooks/site.yml --tags nginx
```

Syntax check and dry run:

```bash
ansible-playbook playbooks/site.yml --syntax-check
ansible-playbook playbooks/site.yml --check
```

## Roles

| Role | Purpose |
|------|---------|
| `common` | System updates, base package installation, and server preparation |
| `docker` | Docker installation, enabling and starting the service |
| `nginx` | Runs Nginx and deploys the `index.html` page |
| `monitoring` | Runs Prometheus using the provided `prometheus.yml` configuration |

## Configuration

Each role's variables are defined in `roles/<role>/defaults/main.yml`. You can override them:

- directly in `defaults/main.yml`
- or at runtime:

```bash
ansible-playbook playbooks/site.yml -e "variable_name=value"
```

To change monitoring targets, edit `roles/monitoring/files/prometheus.yml`. To change the Nginx page, edit `roles/nginx/files/index.html`.

## Verification

After the playbook finishes:

```bash
# Docker
docker --version

# Nginx
curl http://YOUR_SERVER_IP

# Prometheus (default port 9090)
curl http://YOUR_SERVER_IP:9090
```

## License

No license has been added yet. If you want others to be able to use this project, add a `LICENSE` file (e.g. MIT).



