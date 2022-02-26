
# Prerequisites

## Copy Permission (.pem) File

Copy over the `nextcloud-1.pem` generated in `nextcloud-1-terraform`. 

```bash
# Change the permission or Ansible will complain
$ sudo chmod 600 nextcloud-1.pem
```

## Get IP Address

The EC2 instance created from `nextcloud-1-terraform` will have an IP address.  There are multiple ways to get the address.  Once of the easiest is to manually copy the `Public IPv4 address` field from the EC2 Dashboard.

Replace the existing IP in the `hosts` file and the `public_ip` variable in the `install_nextcloud.yml` file.

# Install Ansible (Locally)

Guide - https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#installing-ansible-on-ubuntu

```bash
# Update repo
$ sudo apt update

# Install required packages
$ sudo apt install software-properties-common

# Add the Ansible repo
$ sudo add-apt-repository --yes --update ppa:ansible/ansible

# Install
$ sudo apt install ansible

# Verify install
$ ansible --version
```

# Execute Ansible Playbook

```bash
# Test run
$ ansible all -m ping -i hosts -u ubuntu --private-key nextcloud-1.pem

18.214.121.66 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}

# Run playbbok
$ $ ansible-playbook install_nextcloud.yml -i hosts --private-key nextcloud-1.pem

PLAY [all] *******************

TASK [Gathering Facts] *******************
ok: [18.214.121.66]

TASK [Installing Nextcloud with Snap] *******************
ok: [18.214.121.66]

TASK [Creating admin user] *******************
changed: [18.214.121.66]

TASK [Setting trusted_domains] *******************
changed: [18.214.121.66]

PLAY RECAP *******************
18.214.121.66              : ok=4    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

Nextcloud should be up and running at the IP in the hosts file. 