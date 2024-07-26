# MUNI-ICS test
# Install and run an Nginx Docker container
------------
## Requirements
### Target machine
#### 1. Create user for ansible
```
sudo useradd ansible
sudo chown ansible:ansible /home/ansible
```
#### 2. Add passwordless sudo
```
sudo visudo
```
Add this line to the file: `ansible ALL=(ALL) NOPASSWD: ALL`

#### 3. It's possible you'll need to install Python, I didn't need to on Ubuntu 24


---------------
### Host machine
#### 1. Install ansible via pipx or pip (distro packages are usually outdated)

[Detailed guide](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#installing-and-upgrading-ansible-with-pipx)
```
pipx install --include-deps ansible
```


If you don't have pipx, you can install it like this on debian-based distros:
```
sudo apt install pipx
```

#### 2. Generate ssh key-pair if you don't have one (or want to use a specific one for ansible) and copy it to target machine

[Detailed guide](https://www.ssh.com/academy/ssh/keygen)
```
ssh-keygen
```

Then with key:
```
ssh-copy-id -i ~/.ssh/<your key> <your user>@<host address>
```
#### 3. Copy or move the public key to Ansible users ssh configuration

ssh to target machine
```
ssh <your user>@<host address>
```
The key is located in `~/.ssh/authorized_keys` there may already be your other keys here, so make sure to move/copy the correct (usually the last) one.

Place the public key into `/home/ansible/.ssh/authorized_keys` (create the file and directories if there are none)

Make sure the permissions are correct:
```
sudo chown ansible:ansible /home/ansible/.ssh /home/ansible/.ssh/authorized_keys
sudo chmod 700 /home/ansible/.ssh
sudo chmod 600 /home/ansible/.ssh/authorized_keys
```


## Running Ansible playbook

#### 1. Clone this repository to your machine and cd into the `ansible` folder
```
git clone git@github.com:KaiseerKenopsia/MUNI_ICS_test_public.git
cd ansible
```

#### 2. Put your target machine IP or hostname into a file named `inventory` in the ansible folder
```
echo "[example servers]" >> inventory
echo "10.0.0.42" >> inventory
```

#### 3. Then you can start the playbook with:
```
ansible-playbook main.yml
```

Additionally, tags some tags are available:
  - install - Installs Docker
  - update - Updates Docker and Nginx container image
  - harden - Hardens security configuration instead of just warning about it
  - container - Updates the image to the latest nginx version and makes sure the container is running
  - config - Updates configurations, so far only for firewall access, nothing else needs to be in a var

## Container volumes

The nginx configuration on target machine is located on `/etc/containers/nginx`
and the webserver root folder is in `/var/containers/nginx/www`
Tags can be used like this:
```
ansible-playbook main.yml --tags update
```
