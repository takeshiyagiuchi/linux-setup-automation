# Linux Setup Automation

This repository provides tools for **automated setup and management of Linux servers**, focusing on reproducible and consistent environments.

The current implementation targets **on-premise Ubuntu GPU servers**, with the goal of extending to more general Linux-based infrastructure.

---

## Motivation

I had the opportunity to set up and manage multiple GPU servers for development and experimentation. In practice, this involved:

- Reinstalling the operating system multiple times  
- Reconfiguring users, networking, and storage  
- Installing GPU drivers and development environments  

These tasks were **time-consuming, repetitive, and error-prone**, especially when performed manually across multiple machines.

This repository was created to automate this process and ensure that all servers can be provisioned in a **consistent and reproducible way**.

---

## Why Linux and Ubuntu

Linux was the natural choice for building development environments due to its flexibility, performance, and strong ecosystem for machine learning and system-level tools.

Among Linux distributions, **Ubuntu** was selected because it offers:

- Wide adoption and strong community support  
- Reliable compatibility with NVIDIA drivers and CUDA  
- Extensive documentation and package availability  
- Native support for automation tools such as cloud-init (autoinstall)  

These characteristics make Ubuntu a practical and stable foundation for GPU server environments.

---

## Approach

To address the challenges of manual setup, this project combines two complementary tools:

### 1. Autoinstall (OS Provisioning)
- Automates Ubuntu installation via custom ISO  
- Preconfigures users, SSH, and networking  
- Provides a consistent base system  

### 2. Ansible (Configuration Management)
- Automates post-install system configuration  
- Manages users, SSH keys, and permissions  
- Installs Docker, GPU drivers, and development tools  
- Enables repeatable and scalable provisioning  

---

## Workflow

The overall workflow is:

1. Generate a custom Ubuntu ISO with autoinstall  
2. Install the OS on target machines  
3. Run Ansible playbooks for configuration  

```
ISO → Install → Ansible → Ready Server
```

This separation ensures:
- A stable and reproducible base system  
- Flexible and maintainable configuration management  

---

## Repository Structure

``` id="repo2"
linux-setup-automation/
├── autoinstall/    # Automated Ubuntu installation (custom ISO)
├── ansible/        # Configuration and provisioning (Ansible playbooks)
```

---

## Key Benefits

- **Automation**: Eliminates repetitive manual setup  
- **Consistency**: Ensures identical configurations across servers  
- **Reproducibility**: Infrastructure can be rebuilt at any time  
- **Scalability**: Easily extend to additional machines  

---

## Use Cases

- Provisioning GPU servers for machine learning workloads  
- Rebuilding systems after failures or upgrades  
- Maintaining consistent environments across development and production  

---

## Future Work

- Extend support to other Linux distributions  
- Improve modularization of Ansible roles  
- Add support for cloud-based provisioning  
- Enhance documentation and usability  

---

## Notes

- This project is based on practical experience managing GPU servers  
- Some configurations may require adaptation for different environments  

---