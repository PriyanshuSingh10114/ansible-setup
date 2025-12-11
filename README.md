
# 🛠️ Ansible Setup on AWS EC2 and working with one master node and three servers

This guide explains how to set up an Ansible control node on an AWS EC2 instance and manage multiple remote EC2 servers using SSH keys and inventory groups.

---

## 🚀 Prerequisites

- AWS EC2 instances (Ubuntu recommended)
- SSH key pair (.pem file)
- Security Group allowing:
  - Port 22 (SSH)
- Basic knowledge of Linux and SSH

---

## 📌 1. Update System Packages

- sudo apt update && sudo apt upgrade -y

📌 2. Install Ansible on the Control Node

- sudo add-apt-repository --yes --update ppa:ansible/ansible

- sudo apt install ansible -y

Verify installation:

ansible --version

---

📌 3. Create a Directory for SSH Keys

mkdir ~/keys

chmod 700 ~/keys

Upload your PEM file to this folder:

mv your-key.pem ~/keys/

chmod 600 ~/keys/your-key.pem

📌 4. Configure the Ansible Inventory

Edit the inventory file:

sudo vim /etc/ansible/hosts

---

- Add your EC2 instances:

[servers]

- server_1 ansible_host=xxxxx

- server_2 ansible_host=xxxx

- server_3 ansible_host=xxxx

[servers:vars]

- ansible_user=ubuntu

- ansible_python_interpreter=/usr/bin/python3

- ansible_ssh_private_key_file=/home/ubuntu/keys/your-key.pem

📌 5. Fix SSH Host Key Issues

If the EC2 instance was recreated, SSH keys may mismatch. Clear old keys:

rm -f ~/.ssh/known_hosts

---

📌 6. Test SSH Connectivity

Manually test:

ssh -i ~/keys/your-key.pem ubuntu@52.66.235.170

📌 7. Test Ansible Connectivity

Run a ping test:

ansible servers -m ping


Expected output:

server_1 | SUCCESS => {"ping": "pong"}

server_2 | SUCCESS => {"ping": "pong"}

server_3 | SUCCESS => {"ping": "pong"}

📌 8. Running Ad-Hoc Commands

Check memory:

ansible servers -a "free -h"


Disk usage:

ansible servers -a "df -h"


Uptime:

ansible servers -a "uptime"

---

📌 9. Running a Sample Playbook

Create a playbook:

vim date_play.yml

- name: Dates Playbook
  hosts: servers
  gather_facts: false

  tasks:
    - name: Show date
      command: date

    - name: Show date again
      command: date

---

Run it:

ansible-playbook date_play.yml

📌 10. Common Issues & Fixes
❗ Host Key Verification Failed

Clear SSH known hosts:

rm -f ~/.ssh/known_hosts

❗ Permission Denied for PEM

Fix permissions:

chmod 600 ~/keys/your-key.pem



---
<h3>Deploying static page for testing </h3>

ansible-playbook deploy_static_page.yml
