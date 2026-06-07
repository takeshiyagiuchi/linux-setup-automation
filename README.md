# Linux Setup Automation for GPU Servers

This repository provides tools for automated setup and management of GPU servers using **Ubuntu autoinstall** and **Ansible**.

It was developed to streamline repeated server provisioning tasks, reduce manual configuration effort, and ensure consistent environments across multiple machines.

---

## Overview

Setting up GPU servers manually is time-consuming and error-prone, especially when repeated installations are required. This repository automates the entire workflow:

- OS installation using Ubuntu autoinstall  
- System configuration using Ansible  
- Software and environment setup for GPU workloads  

The goal is to enable **reproducible, consistent, and scalable server setup** with minimal manual intervention.

---

## Motivation

In practice, GPU servers often require:
- Frequent OS reinstallation  
- Reconfiguration of networking and storage  
- Installation of GPU drivers, Docker, and user environments  

Manual setup leads to:
- Inconsistent configurations  
- Increased setup time  
- Higher risk of human error  

To address these issues, this project combines:
- **Autoinstall** → fully automated OS installation  
- **Ansible** → post-install configuration and provisioning  

---

## Why Ubuntu

Ubuntu was chosen as the base operating system for this project due to its wide adoption, strong community support, and compatibility with GPU and machine learning workflows.

As one of the most popular open-source Linux distributions, Ubuntu provides:
- Extensive documentation and community resources  
- Reliable support for NVIDIA drivers and CUDA environments  
- Compatibility with automation tools such as Ansible and cloud-init (autoinstall)  

These factors make Ubuntu a practical and stable choice for building reproducible and maintainable GPU server environments.

---

## Features

- 🔧 Automated Ubuntu installation via custom ISO  
- ⚙️ Infrastructure provisioning with Ansible  
- 👥 User and SSH key management  
- 🐳 Docker and GPU environment setup  
- 📦 Reproducible and consistent system configuration  
- 🔁 Designed for repeated deployments  

---

## Repository Structure

``` id="repo1"
linux-setup-automation/
├── autoinstall/    # Custom Ubuntu ISO generation with autoinstall
├── ansible/        # Ansible playbooks for provisioning and configuration
```

---

## Workflow

The typical workflow is:

1. **Generate autoinstall ISO**
   - Preconfigure OS, users, and networking  
2. **Install Ubuntu**
   - Boot target machine using the generated ISO  
3. **Run Ansible playbooks**
   - Configure system, users, Docker, GPU drivers, etc.  

This approach separates:
- **OS installation (immutable base)**  
- **Configuration management (flexible and repeatable)**  

---

## Example Use Case

- Provisioning multiple GPU servers for machine learning workloads  
- Rebuilding servers after system changes or failures  
- Ensuring consistent environments across development and production  

---

## Getting Started

### Autoinstall
See:
- `autoinstall/README.md`

### Ansible
See:
- `ansible/README.md`

---

## Key Benefits

- **Time-saving**: eliminates repetitive manual setup  
- **Consistency**: identical environments across servers  
- **Scalability**: easily extend to additional machines  
- **Reproducibility**: infrastructure as code approach  

---

## Future Improvements

- Support for additional Linux distributions  
- Cloud-based provisioning (e.g., AWS, GCP)  
- CI/CD integration for infrastructure updates  
- Improved modularization of Ansible roles  

---

## Notes

- This project was developed based on real-world usage managing GPU servers  
- Some configurations may be environment-specific and require adjustment  

---