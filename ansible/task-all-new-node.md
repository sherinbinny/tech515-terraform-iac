## **Step 1: Create the VMs**

You need **two VMs**:

1. **Database VM** – to run MongoDB
2. **Web/App VM** – to run your Node.js app

For example:

| VM Type | Name    | Purpose    | IP (example)  |
| ------- | ------- | ---------- | ------------- |
| DB      | ec2-db  | MongoDB    | 172.31.57.254 |
| Web     | ec2-app | Node + PM2 | 172.31.49.101 |

* Make sure both are accessible via SSH using your controller machine.
* Ensure ports:

  * **MongoDB:** 27017 open to the web VM (or localhost for testing)
  * **Node app:** 3000 open to your browser for `/posts`

---

## **Step 2: Update your Ansible inventory**

Create or update `hosts.ini`:

```ini
[web]
ec2-app ansible_host=172.31.49.101 ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/your-key.pem

[db]
ec2-db ansible_host=172.31.57.254 ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/your-key.pem
```

* Replace the IPs and key file path with your actual VM details.
* Make sure the **hostnames match what you will use in the playbook** (`ec2-app`, `ec2-db`).

---

## **Step 3: Update your playbooks**

### 3a. Database playbook (`prov-db.yml`)

* Installs MongoDB v7
* Configures it to allow connections
* Restarts and enables the service

```yaml
- hosts: db
  become: yes
  tasks:
    - name: Install MongoDB
      apt:
        name: mongodb
        state: present
        update_cache: yes

    - name: Configure MongoDB bindIp
      lineinfile:
        path: /etc/mongodb.conf
        regexp: '^bindIp'
        line: 'bindIp = 0.0.0.0'
      notify:
        - Restart MongoDB

  handlers:
    - name: Restart MongoDB
      service:
        name: mongodb
        state: restarted
        enabled: yes
```

---

### 3b. App playbook (`app.yml`)

* Starts your Node app with PM2
* Passes the **DB_HOST environment variable** dynamically

```yaml
- hosts: web
  vars:
    db_host: "mongodb://{{ hostvars['ec2-db'].ansible_host }}:27017/posts"
  tasks:
    - name: Ensure PM2 is installed
      npm:
        name: pm2
        global: yes
        state: present

    - name: Stop any old PM2 processes
      shell: pm2 delete all || true

    - name: Start app with PM2
      shell: pm2 start app.js --name sparta-app
      args:
        chdir: /home/ubuntu/app/app
      environment:
        DB_HOST: "{{ db_host }}"

    - name: Restart app with updated environment
      shell: pm2 restart sparta-app --update-env
```

---

### 3c. Combined playbook (`prov-app-all.yml`)

```yaml
- import_playbook: prov-db.yml
- import_playbook: app.yml
```

* This runs the **database setup first**, then starts the **app on the web VM**.

---

## **Step 4: Run the playbook**

From your controller VM:

```bash
ansible-playbook -i hosts.ini prov-app-all.yml
```

* Ansible will SSH into the DB VM → install and configure MongoDB
* Then SSH into the Web VM → install PM2 and start your Node app with `DB_HOST` set

---

## **Step 5: Test the app**

1. Check PM2 is running:

```bash
ansible ec2-app -i hosts -m shell -a "pm2 status"
```

2. Visit the `/posts` page in your browser:


## Commands used

```
  281  ansible web -m shell -a "pm2 kill"
  282  ansible web -m shell -a "sudo lsof -i :3000 || echo 'port 3000 free'"
  283  ansible web -m shell -a "sudo kill -9 $(sudo lsof -ti:3000) || echo 'nothing running on 3000'"
  284  ansible web -m shell -a "sudo lsof -i :3000 || echo 'port 3000 free'"
  285  ansible web -m shell -a "systemctl status sparta-app || echo 'no systemd service'"
  286  ansible web -m shell -a "ps -ef | grep node"
  287  ansible web -m shell -a "sudo pm2 kill"
  288  ansible web -m shell -a "sudo lsof -i :3000 || echo 'port 3000 free'"
  289  ansible web -m shell -a "node -v && npm -v"
  290  ansible web -m shell -a "pm2 -v" -u ubuntu || ansible web -m shell -a "npm install pm2 -g" -u ubuntu
  291  ansible-playbook prov-app-all.yml
  292  ansible web -m shell -a "pm2 status" -u ubuntu
  293  ansible web -m shell -a "sudo fuser -k 3000/tcp || echo 'port 3000 free'"
  294  sudo rm prov-app.yml
  295  sudo nano prov-app.yml 
  296  sudo nano prov-app-all.yml 
  297  ansible-playbook prov-app-all.yml
  298  sudo nano prov-app.yml 
  299  ansible-playbook prov-app-all.yml
  300  ansible web -m shell -a "node -v && npm -v"
  301  ansible web -m shell -a "pm2 -v" -u ubuntu || ansible web -m shell -a "npm install pm2 -g" -u ubuntu
  302  ansible web -m shell -a "pm2 status" -u ubuntu
  303  ansible web -m shell -a "curl http://localhost:3000/posts"
  304  ansible web -m shell -a "pm2 logs sparta-app --lines 20"
  305  ansible web -m shell -a "DB_HOST='mongodb://172.31.62.118:27017/posts' node /home/ubuntu/app/app/app.js"
  306  ansible web -m shell -a "sudo lsof -i :3000"
  307  sudo rm prov-app.yml
  308  sudo nano prov-app.yml
  309  ansible-playbook prov-app-all.yml
  310  sudo nano prov-app.yml
  311  ansible-playbook prov-app-all.yml
  312  ansible web -m shell -a "sudo nginx -t"
  313  ansible web -m shell -a "systemctl status nginx"
  314  ansible web -m shell -a "sudo systemctl restart nginx"
  315  ansible web -m shell -a "sudo lsof -i :80 || echo 'port 80 free'"
  316  ansible web -m shell -a "npm install"
  317  ansible web -m shell -a "cd /home/ubuntu/app/app && npm install" -b
  318  ansible web -m shell -a "sudo systemctl restart nginx"
  319  ansible web -m shell -a "cd /home/ubuntu/app/app && pm2 start app.js --name sparta-app --update-env" -u ubuntu
  320  ansible db -m service -a "name=mongod state=started"
  321  ansible db -m shell -a "grep bindIp /etc/mongod.conf"
  322  ansible web -m shell -a "cd /home/ubuntu/app/app && npm install" -b
  323  ansible web -m shell -a "cd /home/ubuntu/app/app && pm2 start app.js --name sparta-app --update-env" -u ubuntu
  324  ansible web -m shell -a "cd /home/ubuntu/app/app && DB_HOST='mongodb://{{ hostvars['ec2-db-instance'].ansible_host }}:27017/posts' node seeds/seed.js" -u ubuntu
```