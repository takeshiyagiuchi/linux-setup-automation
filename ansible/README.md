### What to do for the host and the clients beforehand (as svadmin)
1. Disable the sudo password. (on the server)
```
$ sudo visudo

- %sudo   ALL=(ALL:ALL) ALL
+ %sudo	ALL=(ALL:ALL) NOPASSWD:ALL
```
2. Add a user "ansible"(uid:2000) to the clients as sudoer. (on the server)
```
$ sudo adduser --uid 2000 ansible
$ sudo gpasswd -a ansible sudo
```
3. Configure sshd_config to set "NoPasswordAuth" for ansible. (on the server)
```
$ sudo vi /etc/ssh/sshd_config

+ Match User ansible
+    PasswordAuthentication no
```
4. Make /usr/bin/python available. (on the server)
```
$ ln -s /usr/bin/python3 /usr/bin/python
```
5. ssh-copy-id the ssh public key to ansible of the cilents. (on your PC)
```
$ ssh-copy-id -i ~/.ssh/office-gpu-server-ansible ansible@gpu0x.local
```
6. Make sure that all the users are listed in var_users and that the sudoers' passwords are all good.
<br>

### Ansible Setups

0. Move to the working directory
1. Install python(3.10.x) and poetry
   (The python version is specified due to the dependency with ansible-generator)
2. Add ansible-core
```
poetry add ansible-core
poetry run ansible --version
```

3. Add ansible-generator and generate a playbook structure
```
poetry add ansible-generator
poetry run ansible-generate
```

4. Generate ansible.cfg
```
poetry run ansible-config init --disabled > ansible.cfg
```
<br>

### Inventory Setups
1. Add the following line to ansible.cfg
```
enable_plugins=host_list, yaml
```
2. Create inventory.yml
3. Make sure the inventory file works right
```
poetry run ansible-inventory -i inventory.yml --list
poetry run ansible-inventory -i inventory.yml --graph
poetry run ansible GPU0x -m ping -i inventory.yml  # "ansible all" does not work. Need to ping by host or by group
```

*references <br>
https://docs.ansible.com/ansible/latest/inventory_guide/intro_inventory.html
https://docs.ansible.com/ansible/2.9_ja/plugins/inventory.html#inventory-plugins
https://docs.ansible.com/ansible/latest/network/getting_started/first_inventory.html
https://docs.ansible.com/ansible/latest/getting_started/get_started_inventory.html
https://stackoverflow.com/questions/44734179/specifying-ssh-key-in-ansible-playbook-file

<br>

### Review files before running playbooks
The followings are the points you should review before running playbooks:
```
[1] group_vars/var_users
 - Necessary users are in the list.
 - Unnecessary users are excluded from the list.
 - Each user is categorized in admins(sudoers) or users(non-sudoers) right.
 - The uids are not duplicated.
 - The groups each user joins are listed right.
 - Whether the Jupyter container is needed for each user is set right.
 - The password for each user is set right.

[2] users/ssh_public_keys
 - The authorized_keys files for all the users are created.
 - The filenames are following the naming rule; authorized_keys_<uid>_<username>
 - The authorized_keys file for each user includes the ansible's public key and the user's public key

[3] docker/compose_files
 - The docker-compose files for all the users are created.
 - The filenames are following the naming rule; docker-compose_<uid>_<username>
 - The following items in each docker-compose file are set right:
     - services.jupyter.devices
     - services.jupyter.deploy.resources.reservations.devices.count
     - services.jupyter.ports.published
     - services.jupyter.volumes.source

[4] aws/settings/credentials
 - The values of "aws_access_key_id" and "aws_secret_access_key" are set right.
```

<br>

### Run Ansible Playbooks
example of an add_users role:
```
poetry run ansible-playbook -i inventory.yml -l GPU02 -K roles/add_users.yml
```

!!! To run without the K option, add an ansible user with no password login.

<br>

### The order to run playbooks

```
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

~ reboot? ~

gpu/install_nvidia_driver

docker/install_docker
docker/setup_docker_for_gpu
docker/setup_docker_rootless
docker/setup_jupyter_containers

aws/setup_aws_cli
aws/setup_cloudwatch_agent
```

<br>

### Integration tests (Confirm the server is set up right.)

```
[1] Turn on the server using Wake-on-LAN.
[2] Connect to the server with your SSH public key as your user.
[3] Get the token for your Jupyter Notebook service.
[4] Log in to Jupyter Notebook from your web browser.
[5] View the GPU information from your Jupyter container. *Run the following command on your Jupyter container:
$ nvidia-smi
[6] Access the NAS directory from your Jupyter container. *Run the following command on your Jupyter container:
$ ls /nas
```

<br>

### Additional settings
- apt packages might need to be disabled. Use `roles/apt/disable_apt_autoupgrade.yml`.
