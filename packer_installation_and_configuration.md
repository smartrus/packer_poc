# Packer installation and configuration

Used this manual https://www.packer.io/intro/getting-started/install.html

```
sudo -i
apt-get install unzip -y
curl -O https://releases.hashicorp.com/packer/1.2.5/packer_1.2.5_linux_amd64.zip
mkdir /bin/packer
unzip packer_1.2.5_linux_amd64.zip -d /bin/packer
export PATH=$PATH:/bin/packer
cd /usr/bin
ln -s /bin/packer/packer packer
```

Installing Docker https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-using-the-repository

Setting up the Docker repo

```
sudo apt-get update

sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```

Installing Docker CE

```
sudo apt-get update

sudo apt-get install docker-ce

sudo docker run hello-world
```

To test packer I followed this post https://ixis.co.uk/blog/pack-it-packaging-docker-images-packer

```
vi ubuntu.json
{
    "builders": [
        {
            "type": "docker",
            "image": "ubuntu:16.04",
            "commit": "true",
            "pull": "true",
            "changes": [
                "ENTRYPOINT [\"nginx -g 'daemon off;'\"]"
            ]
	}
    ],
    "provisioners": [
        {
            "type": "shell",
            "script": "install-ansible.sh"
        },
        {
            "type": "ansible-local",
            "playbook_file": "common.yml"
        }
    ]
 }

###############################################

vi install-ansible.sh

#!/bin/bash

apt-get update
apt-get install software-properties-common -y
apt-add-repository ppa:ansible/ansible -y
apt-get update
apt-get install ansible -y

###############################################

vi common.yml
---

    - hosts: localhost

      tasks:

      - name: install nginx
        apt:
          name: nginx
          state: latest
        when: ansible_distribution == "Ubuntu"
```

Then we validate and run build

```
packer validate ubuntu.json
packer build ubuntu.json
```
