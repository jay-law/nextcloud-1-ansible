



# Overview

This tutorial is designed to walk users through installing Nextcloud in AWS (Amazon Web Services) using Terraform and Ansible.  It's more of a working example opposed to an in-depth tutorial.

In the first part of the tutorial, Terraform will be used to:
- Create an EC2 instance.  Used to host Nextcloud.
- Create security groups and assign them to the EC2 instance.  Provides access to Nextcloud.

In the second part of the tutorial, Ansible will be used to:
- Install Nextcloud on the EC2 instance.
- Create an admin user in Nextcloud.
- Add the IP to the trusted_domains.  Allows access to Nextcloud.

Tested with the following:
- Local machine
    - Ubuntu 20.04.4 LTS (64-bit)
    - Terraform 1.1.6
    - Ansible (core) 2.12.2
    - Python 3.8.10
- Remote machine (AWS EC2 instance)
    - Ubuntu 20.04 LTS (64-bit)

# Prerequisites

## Technologies Used

- **Nextcloud** - A self-hosted productivity platform.  
    - Skills required - None to little
- **AWS** - Cloud platform used to host the infrastructure.
    - Skills required- Very little
- **Terraform** - Tool used to create the infrastructure.
    - Skills required - None to little
- **Ansible** - Tool used to modify the software within the infrastructure.
    - Skills required - None to little

## Ensure AWS Creds Exist Locally

An existing AWS account is required.  Follow the instructions in this link to create an account - https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/.

Additionally, your AWS creds will need to configured locally.  Follow this link to get those setup - https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-files.html.

```bash
# Ensure AWS creds exist in `~/.aws/credentials`
$ cat ~/.aws/credentials
[default]
aws_access_key_id=djjgoijd
aws_secret_access_key=8dh3hgf8hfoih
```
# Configure Local Environment

## Clone This Repo

```bash
# Clone repo with SSH if GitHub account is configured with SSH keys
$ git clone git@github.com:jay-law/nextcloud-1-ansible.git

# or
# Clone repo with HTTPS
$ git clone https://github.com/jay-law/nextcloud-1-ansible.git

# cd into the repo directory
$ cd nextcloud-1-ansible/ 
```


## Copy Permission (.pem) File

Copy the `nextcloud-1.pem` file (created in the `nextcloud-1-terraform` tutorial) over to the `nextcloud-1-ansible` directory (current working directory).

```bash
# This code might work if the pem was downloaded to the default 'Downloads' directory
$ cp ~/Downloads/nextcloud-1.pem .

# Change the permission or Ansible will complain
$ sudo chmod 600 nextcloud-1.pem
```

## Get IP Address

The EC2 instance created from `nextcloud-1-terraform` will have an IP address.  There are multiple ways to get the address.  Once of the easiest is to manually copy the `Public IPv4 address` field from the EC2 Dashboard.

Replace the existing IP in the `hosts` file and the `public_ip` variable in the `install_nextcloud.yml` file.

## Install Ansible (Locally)

Ansible will be used to install Nextcloud on the EC2 instance.

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

# Install Nextcloud

```bash
# Perform a test run to ensure no issues will arise
$ ansible all -m ping -i hosts -u ubuntu --private-key nextcloud-1.pem

18.214.121.66 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}

# Run playbbok to install and configure Nextcloud
$ ansible-playbook install_nextcloud.yml -i hosts --private-key nextcloud-1.pem

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


Nextcloud should be up and running at the IP in the hosts file.  Paste the IP into your browser.  Use the admin username and password specified in the `install_nextcloud.yml` file to login.  No SSL was configured so you might get a 'Secure Site Not Available' warning.  Just select 'Continue to HTTP Site' in Firefox. 

Tinker around with these scripts to build on your AWS, Terraform, and Ansible experience.

Be sure to destroy the instance once you are finished tinkering.  These scripts have practically no security.

```bash
$ terraform destroy
```
