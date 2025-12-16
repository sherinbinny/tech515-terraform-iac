# **Ansible Playbook: Provisioning App VM**

## **Objective**

Use Ansible to provision an app VM (`web` node) by installing required dependencies, cloning the app repository, installing Node.js packages, and running the app in the foreground. Node.js version 20 is used to ensure compatibility with modern packages.

---

## **Inventory Setup**

Create `/etc/ansible/hosts` with:

```ini
[test:children]
web
db

[web]
ec2-app-instance ansible_host=<APP_NODE_PRIVATE_IP> ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/tech515-sherin-aws.pem

[db]
ec2-db-instance ansible_host=<DB_NODE_PRIVATE_IP> ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/tech515-sherin-aws.pem
```

* Replace `<APP_NODE_PRIVATE_IP>` and `<DB_NODE_PRIVATE_IP>` with the actual private IPs.
* This allows Ansible to target the app node and database node correctly.

---

**Notes:**

1. `force: yes` ensures the git clone overwrites any previous files without breaking.
2. `become_user: ubuntu` ensures correct permissions on `/home/ubuntu/app`.
3. `async: 0` and `poll: 0` runs the app in the foreground but doesn’t block Ansible from finishing.
4. No double `/app/app` folder — all tasks target `/home/ubuntu/app`.

---

## **Execution Steps**

1. Verify you can SSH from the controller to the app node:

```bash
ssh -i ~/.ssh/tech515-sherin-aws.pem ubuntu@<APP_NODE_PRIVATE_IP>
```

2. Test connectivity with Ansible:

```bash
ansible all -m ping
```

3. Run the playbook:

```bash
ansible-playbook prov_app_with_node.yml
```

4. Check the app:

* Nginx is installed and running.
* Node.js 20 and npm are installed.
* App is cloned in `/home/ubuntu/app`.
* Dependencies installed via `npm install`.
* App started via `npm start`.




Got it — let’s lay out a **clear step-by-step guide** for your task of provisioning the app VM, installing dependencies, and running the app with Ansible. I’ll include everything from inventory setup to running the playbook.

---
<br><br><br>

## **1️⃣ Ensure your environment is ready**

1. Controller node is up and you can SSH into app-node.
2. Private key is on the controller and can SSH to app-node:

```bash
ssh -i ~/.ssh/tech515-sherin-aws.pem ubuntu@<app-node-IP>
```

3. Ansible installed on the controller:

```bash
ansible --version
```

---

## **2️⃣ Prepare the inventory file**

Create `/etc/ansible/hosts`:

```ini
[web]
ec2-app-instance ansible_host=172.31.49.101 ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/tech515-sherin-aws.pem
```

* Replace `172.31.49.101` with your actual app-node private IP.

---

## **3️⃣ Create the playbook**

File: `prov_app_with_npm_start.yml`

```yaml
---
- name: install app dependencies and run app
  hosts: web
  become: yes
  tasks:

    - name: Install Nginx
      apt:
        name: nginx
        state: present
        update_cache: yes

    - name: Install Node.js and npm
      apt:
        name: nodejs
        state: present
        update_cache: yes

    - name: Install npm (if not installed)
      apt:
        name: npm
        state: present

    - name: Clone the app repository to target node
      git:
        repo: 'https://github.com/yourusername/your-app-repo.git'
        dest: /home/ubuntu/app
        version: main

    - name: Install app dependencies (npm install)
      command: npm install
      args:
        chdir: /home/ubuntu/app/app

    - name: Run the app (npm start) in foreground
      shell: npm start
      args:
        chdir: /home/ubuntu/app/app
```

---

## **4️⃣ Run the playbook**

From your controller:

```bash
ansible-playbook /etc/ansible/hosts prov_app_with_npm_start.yml
```

* Ansible will:

  1. Install Nginx
  2. Install Node.js & npm
  3. Clone your app repo
  4. Run `npm install`
  5. Start the app

---

## **5️⃣ Verify the app is running**

1. SSH into the app-node:

```bash
ssh -i ~/.ssh/tech515-sherin-aws.pem ubuntu@172.31.49.101
```

2. Check if the app is listening (example port 3000):

```bash
ss -tulnp | grep node
```

3. Or hit the app from the browser using your public IP if the security group allows the port:

```
http://<app-node-public-IP>:3000
```

## Commands used

```
 103  ssh -i "~/.ssh/tech515-sherin-aws.pem" ubuntu@172.31.49.101
  104  sudo nano prov_app_with_npm_start.yml
  105  rm prov_app_with_npm_start.yml
  106  ls
  107  cd /etc/ansible/
  108  sudo nano prov_app_with_npm_start.yml
  109  ansible-playbook prov_app_with_npm_start.yml
  110  sudo nano prov_app_with_npm_start.yml
  111  ansible-playbook prov_app_with_npm_start.yml
  112  sudo nano prov_app_with_npm_start.yml
  113  ansible-playbook prov_app_with_npm_start.yml
  114  ssh -i "~/.ssh/tech515-sherin-aws.pem" ubuntu@172.31.49.101
  115  rm prov_app_with_npm_start.yml
  116  sudo nano prov_app_with_npm_start.yml
  117  rm prov_app_with_npm_start.yml
  118  sudo rm prov_app_with_npm_start.yml
  119  ls
  120  sudo nano prov_app_with_npm_start.yml
  121  ansible-playbook prov_app_with_npm_start.yml
  122  sudo rm prov_app_with_npm_start.yml
  123  ls
  124  sudo nano prov_app_with_npm_start.yml
  125  ansible-playbook prov_app_with_npm_start.yml
  126  sudo nano prov_app_with_npm_start.yml
  127  ansible-playbook prov_app_with_npm_start.yml
```