# loadbalancing

## Role installation

### create requirements file

```bash
cd ANSIBLE_FOLDER
mkdir requirements
touch requirements/loadbalancing.yml
```

### file content

```yaml
---
# ansible-galaxy install -p vss_galaxy_roles --force -r requirements/loadbalancing.yml
- src: "https://github.com/virsas/mod-ansible-rpm-loadbalancing"
  scm: git
  version: v1.1.1
  name: loadbalancing
  path: vss_galaxy_roles
```

## Install role

### Installation

```bash
ansible-galaxy install -p galaxy_roles --force -r requirements/loadbalancing.yml
```

### Best practices

If you are using git for your playbooks and sites configuration, add vss_galaxy_roles to your .gitignore file. You are free to download the role directly to your roles directory, but it will be then pushed to your repo too.

## Role access

### Direct access

```bash
$ cd vss_galaxy_roles
$ ls
loadbalancing
$ cd loadbalancing
$ ls -1
defaults
handlers
LICENSE
meta
README.md
tasks
templates
$
```

### Access from ansible (ansible.cfg)

```bash
[defaults]
gathering = smart
roles_path=./roles:../roles:../../roles:./vss_galaxy_roles
log_path=./ansible_run.log
retry_files_enabled=False
[ssh_connection]
scp_if_ssh=True
host_key_checking=False
```

## Files

### Playbook (./playbooks/loadbalancing.yml)

```yaml
---
- hosts: loadbalancing
  remote_user: ec2-user
  become: yes
  roles:
    - loadbalancing
```

### Inventory (./sites/NAME/inventory)

```txt
[loadbalancing]
loadbalancing_01
```

### Host vars (./sites/NAME/host_vars/loadbalancing_01.yml)

```yaml
---
ansible_ssh_host: 1.1.1.1
```

### Group vars (./sites/NAME/group_vars/loadbalancing.yml)

Please see the variables below to get the complete list of possible modifications. The YAML file below is just a basic example of a minimal configuration.

```yaml
---
CERT_EMAIL: system@example.org
CERT_DOMAINS:
  - example.org
  - www.example.org
  - admin-api.example.org

NGINX_BACKENDS:
  - name: admin
    method: ip_hash
    idle_connections: 8
    servers:
      - ip: 172.16.2.4
        port: 8000
NGINX_FRONTEND_PROXIES:
  - domain: admin-api.example.org
    timeout: 30s
    backend: admin
NGINX_FRONTEND_REDIRECTS:
  - domain: www.example.org
    redirect_to: www.example.com/org/
```

## Usage

```bash
ansible-playbook -i sites/NAME/inventory playbooks/loadbalancing.yml --diff
```

## Variables

```yml
CERT_EMAIL: info@example.com
CERT_DOMAINS:
  - example.com
  - www.example.com
  - api.example.com

# cronjob configuration for renewals
CRON_RENEWAL_MINUTE: 0
CRON_RENEWAL_HOUR: 2

# nginx configuration
NGINX_WORKER_CONNECTIONS: 1024
NGINX_KEEPALIVE_TIMOUT: 65
NGINX_HASH_MAX_SIZE: 4096
NGINX_MAX_BODY_SIZE: 100m
NGINX_SSL_PROTOCOLS: TLSv1.2 TLSv1.3
NGINX_SSL_CIPHERS: "HIGH:!aNULL:!MD5:!DSS"
NGINX_SSL_TIMEOUT: 1m

NGINX_MAX_FAILS: 1
NGINX_FAIL_TIMEOUT: 15s

NGINX_BACKENDS: []
NGINX_FRONTEND_PROXIES: []
NGINX_FRONTEND_REDIRECTS: []
```
