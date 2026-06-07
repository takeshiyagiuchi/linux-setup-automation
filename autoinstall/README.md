# Autoinstall ISO for GPU Servers

This directory contains scripts and configurations to generate a custom Ubuntu ISO image with autoinstall for GPU servers.

---

## Creating a Bootable USB

The following steps assume the process is performed on **macOS**.  
If you are working on Ubuntu, use the `to_docker` directory instead.

The workflow consists of two main steps:

1. Create an ISO image with autoinstall  
2. Write the ISO image to a USB drive  

---

## 1. Create an ISO Image with Autoinstall

> ⚠️ The ISO build process must be executed on Ubuntu.

### 1.1 Configure Variables

Review and update parameters in `variables.txt`:

```bash id="iso1"
# Required configuration
UBUNTU_VERSION_MJR=22
UBUNTU_VERSION_MDL=04
UBUNTU_VERSION_MNR=2
TARGET_MACHINE_HN=GPU01
TARGET_MACHINE_IP=192.168.0.11
```

---

### 1.2 Add SSH Public Key

Add your SSH public key for the `ansible` user to:

```bash id="iso2"
to_docker/config/post-processing/authorized_keys_ansible
```

---

### 1.3 Build the ISO

Run:

```bash id="iso3"
make all
```

The ISO image will be generated in the current directory.

Example:

```bash id="iso4"
ubuntu22042-GPU01.iso
```

---

## 2. Write the ISO to a USB Drive

Follow the official Ubuntu guide:

https://ubuntu.com/tutorials/create-a-usb-stick-on-macos#1-overview

---

## Post-Installation Checklist

After the autoinstall process completes, verify the following:

### System Access
- Able to log in as `adminuser`  
- Able to use `sudo` without a password  

---

### Network Configuration
- Network interface is up and configured correctly:
```bash id="iso5"
ip a
```

- Netplan configuration is properly applied:
```bash id="iso6"
cat /etc/netplan/00-installer-config.yaml
```

---

### Storage
- Disk partitions are correctly configured:
```bash id="iso7"
df -h
```

---

### User Setup
- `ansible` user is created:
```bash id="iso8"
ls -la /home
cat /etc/passwd | grep ansible
```

- SSH access for `ansible` works without a password  

---

### Environment
- Python is available:
```bash id="iso9"
python --version
```

---