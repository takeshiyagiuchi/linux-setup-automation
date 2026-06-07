This directory contains programs to create an ISO file with autoinstall for GPU servers.  

<br>

## How to create a bootable USB
This process is assumed to be carried out on MacOS. When you work on Ubuntu, make at the to_docker directory.  

<br>

The main steps here are
1. Make an ISO image with autoinstall and
2. Burn the ISO image to USB.

<br>

### 1. Make an ISO image with autoinstall  
To make an ISO file, the current method we use requires it done on Ubuntu.
  
1-1) Review the parameters in variables.txt.  
Set the target Ubuntu version and target machine:  

    ## should be reviewed before make  
    UBUNTU_VERSION_MJR=22
    UBUNTU_VERSION_MDL=04
    UBUNTU_VERSION_MNR=2
    TARGET_MACHINE_HN=GPU01
    TARGET_MACHINE_IP=192.168.0.11


1-2) Add your SSH public-key for the ansible user to the `to_docker/config/post-processing/authorized_keys_ansible` file.  


1-3) Run `make all`.  

<br>

The ISO image is created on the current directory.

    e.g) ubuntu22042-GPU01.iso

<br>

### 2. Burn the ISO image to USB  
Refer to  
https://ubuntu.com/tutorials/create-a-usb-stick-on-macos#1-overview

<br><br>


## After installation
After autoinstall is finished, you should test it with the items:

- You can log in as adminuser.  
- A NIC is up with the expected settings.  
  $`ip a`  
- The netplan configuration file is properly written.  
  $`cat /etc/netplan/00-installer-config.yaml`  
- The partitions are properly set.  
  $`df -h`  
- You are able to sudo without password.  
- The user "ansible" is created.
  
  $`ls -la /home`  

  $`cat /etc/passwd | grep ansible`  
- You can connect to ansible via ssh without password.  
- You can find the "python" command.  
