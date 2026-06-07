# Ansible Setup for GPU Servers

This directory contains Ansible configurations and playbooks for provisioning and managing GPU servers.

---

## Prerequisites (Host & Clients)

Perform the following steps on the target servers (as `adminuser`) unless otherwise noted.

### 1. Disable sudo password
```bash
sudo visudo
```

Update:
```diff
- %sudo   ALL=(ALL:ALL) ALL
+ %sudo   ALL=(ALL:ALL) NOPASSWD:ALL
```

---

### 2. Create `ansible` user (UID: 2000)
```bash
sudo adduser --uid 2000 ansible
sudo gpasswd -a ansible sudo
```

---

### 3. Configure SSH for passwordless login
```bash
sudo vi /etc/ssh/sshd_config
```

Add:
```bash
Match User ansible
    PasswordAuthentication no
```

---

### 4. Ensure `python` command is available
```bash
sudo ln -s /usr/bin/python3 /usr/bin/python
```

---

### 5. Copy SSH key to clients (from your local machine)
```bash
ssh-copy-id -i ~/.ssh/office-gpu-server-ansible ansible@gpu0x.local
```

---

### 6. Verify user configuration
Ensure:
- All users are listed in `group_vars/var_users`
- Sudo users have valid passwords

---

## Ansible Setup

### 0. Move to working directory

---

### 1. Install dependencies
Install:
- Python (3.10.x)
- Poetry  

> Python version is fixed due to dependency with `ansible-generator`.

---

### 2. Install Ansible
```bash
poetry add ansible-core
poetry run ansible --version
```

---

### 3. Initialize project structure
```bash
poetry add ansible-generator
poetry run ansible-generate
```

---

### 4. Generate configuration file
```bash
poetry run ansible-config init --disabled > ansible.cfg
```

---

## Inventory Setup

### 1. Enable inventory plugins
Add to `ansible.cfg`:
```bash
enable_plugins = host_list, yaml
```

---

### 2. Create inventory file
Create:
```bash
inventory.yml
```

---

### 3. Validate inventory
```bash
poetry run ansible-inventory -i inventory.yml --list
poetry run ansible-inventory -i inventory.yml --graph
poetry run ansible GPU0x -m ping -i inventory.yml
```

> Note: `ansible all` may not work; use host or group names instead.

---

## Pre-Run Checklist

Review the following before running playbooks:

### [1] `group_vars/var_users`
- Required users are included  
- Unnecessary users are excluded  
- Users are correctly categorized (admins vs users)  
- UIDs are unique  
- Group memberships are correct  
- Jupyter container settings are correct  
- Passwords are properly set  

---

### [2] `users/ssh_public_keys`
- All users have authorized_keys files  
- Naming format: `authorized_keys_<uid>_<username>`  
- Contains both:
  - Ansible public key  
  - User’s public key  

---

### [3] `docker/compose_files`
- Docker Compose files exist for all users  
- Naming format: `docker-compose_<uid>_<username>`  
- Verify configuration:
  - `devices`  
  - GPU resource allocation  
  - port mappings  
  - volume paths  

---

### [4] `aws/settings/credentials`
- `aws_access_key_id` is set correctly  
- `aws_secret_access_key` is set correctly  

---

## Running Playbooks

Example:
```bash
poetry run ansible-playbook -i inventory.yml -l GPU02 -K roles/add_users.yml
```

> To run without `-K`, ensure passwordless sudo is configured for the `ansible` user.

---

## Execution Order

Recommended order:

```bash
wol/setup_wol

users/add_users
users/configure_sshd
users/keygen_ansible
users/distribute_public_keys
users/quota_installation
users/quota_per_user

nas/mount_nas
nas/setup_automount
nas/make_user_directory

# Optional reboot

gpu/install_nvidia_driver

docker/install_docker
docker/setup_docker_for_gpu
docker/setup_docker_rootless
docker/setup_jupyter_containers

aws/setup_aws_cli
aws/setup_cloudwatch_agent
```

---

## Integration Testing

Verify the setup with the following steps:

1. Power on server using Wake-on-LAN  
2. SSH into the server using your key  
3. Retrieve Jupyter token  
4. Access Jupyter via browser  
5. Verify GPU availability:
```bash
nvidia-smi
```

6. Verify NAS access:
```bash
ls /nas
```

---

## Additional Notes

- To disable automatic apt upgrades:
```bash
roles/apt/disable_apt_autoupgrade.yml
```

---