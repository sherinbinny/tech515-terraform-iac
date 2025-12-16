## 1️⃣ Baseline check (don’t break what already works)

Before touching the DB:

```bash
ansible-playbook prov-app.yml
```

✔️ App runs in the background
✔️ App works **without DB**
If this fails, stop. Fix it first. No heroics.

---

## 2️⃣ Create `prov-db.yml` (Database provisioning playbook)

### Purpose

Provision MongoDB **v7.0**, open it to external connections, and ensure it’s running on boot.

### `prov-db.yml`

```yaml
---
- name: Provision MongoDB on database VM
  hosts: db
  become: true

  tasks:
    - name: Import MongoDB GPG key
      apt_key:
        url: https://pgp.mongodb.com/server-7.0.asc
        state: present

    - name: Add MongoDB repository
      apt_repository:
        repo: "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/7.0 multiverse"
        state: present
        filename: mongodb-org-7.0

    - name: Update apt cache
      apt:
        update_cache: yes

    - name: Install MongoDB 7.0
      apt:
        name: mongodb-org
        state: present

    - name: Configure MongoDB to allow external connections
      replace:
        path: /etc/mongod.conf
        regexp: "bindIp:.*"
        replace: "bindIp: 0.0.0.0"

    - name: Enable and restart MongoDB
      service:
        name: mongod
        state: restarted
        enabled: yes
```

**Why this is solid**

- Uses official Mongo repo (not Ubuntu’s outdated one)
- `replace` keeps it idempotent (no duplicate configs)
- `service` handles enable + restart in one go (clean ops)

---

## 3️⃣ Ad-hoc verification from Ansible controller

### Check MongoDB status

```bash
ansible db -a "systemctl status mongod"
```

You want:

```
Active: active (running)
```

---

### Check `bindIp` config

```bash
ansible db -a "grep bindIp /etc/mongod.conf"
```

Expected output:

```
bindIp: 0.0.0.0
```

If that’s not there — Mongo isn’t listening externally, full stop.

---

## 4️⃣ Manual DB test from the app VM (required by task)

### SSH into app VM

```bash
ssh ubuntu@<app-ip>
```

### Create DB_HOST environment variable

```bash
export DB_HOST=mongodb://<db-private-ip>:27017/posts
```

### Verify it exists

```bash
echo $DB_HOST
```

If nothing prints, you did nothing. Classic mistake.

---

### Restart app manually

Example (adapt to your setup):

```bash
pm2 restart all
```

or

```bash
node app.js &
```

Now test:

```bash
curl http://localhost:3000/posts
```

✔️ JSON response = DB connectivity confirmed
✔️ No response = check security group / bindIp / DB_HOST value

---

## 5️⃣ Update **app playbook** to use environment variables

### Example: `prov-app.yml`

```yaml
---
- name: Provision app VM
  hosts: web
  become: true

  vars:
    db_host: "mongodb://{{ hostvars['db-instance'].ansible_host }}:27017/posts"

  tasks:
    - name: Install Node dependencies
      npm:
        path: /home/ubuntu/app

    - name: Start app in background with DB_HOST
      shell: |
        pm2 start app.js --name sparta-app
      args:
        chdir: /home/ubuntu/app
      environment:
        DB_HOST: "{{ db_host }}"
```

**Why this matters**

- No hardcoding
- Environment-driven config (12-factor compliant)
- App and DB now decoupled but orchestrated

---

## 6️⃣ Create the **single master playbook**: `prov-app-all.yml`

This is your executive summary playbook. One command. Two plays. No drama.

```yaml
---
- name: Provision application
  hosts: web
  become: true
  roles:
    - app

- name: Provision database
  hosts: db
  become: true
  roles:
    - db
```

👉 If you’re **not using roles**, inline it like this:

```yaml
---
- import_playbook: prov-app.yml
- import_playbook: prov-db.yml
```

Both are valid. Second is simpler for coursework.

---

## 7️⃣ Final test (non-negotiable)

Run:

```bash
ansible-playbook prov-app-all.yml
```

Then open:

```
http://<app-public-ip>:3000/posts
```

✔️ App loads
✔️ `/posts` returns data
✔️ No SSH fiddling afterward

That’s production-grade behavior.


## Commands used

```
  129  cd /etc/ansible/
  130  sudo nano prov_app_with_npm_start.yml
  131  ansible-playbook prov_app_with_npm_start.yml
  132  sudo nano prov_app_with_npm_start.yml
  133  ansible-playbook prov_app_with_npm_start.yml
  134  sudo nano prov_app_with_npm_start.yml
  135  ansible-playbook prov_app_with_npm_start.yml
  136  ssh -i "~/.ssh/tech515-sherin-aws.pem" ubuntu@172.31.49.101
  137  rm prov_app_with_npm_start.yml
  138  sudo nano prov_app_with_npm_start.yml
  139  rm prov_app_with_npm_start.yml
  140  sudo rm prov_app_with_npm_start.yml
  141  ls
  142  sudo nano prov_app_with_npm_start.yml
  143  ansible-playbook prov_app_with_npm_start.yml
  144  sudo rm prov_app_with_npm_start.yml
  145  ls
  146  sudo nano prov_app_with_npm_start.yml
  147  ansible-playbook prov_app_with_npm_start.yml
  148  sudo nano prov_app_with_npm_start.yml
  149  ansible-playbook prov_app_with_npm_start.yml
  150  history
  151  sudo nano prov_app_with_pm2.yml
  152  ls
  153  ansible-playbook prov_app_with_pm2.yml
  154  ssh -i "~/.ssh/tech515-sherin-aws.pem" ubuntu@172.31.49.101
  155  ansible all -m ping
  156  cd /etc/ansible
  157  ansible web -m ping -i /etc/ansible/hosts
  158  sudo nano prov_app_with_npm_start.yml
  159  ansible-playbook prov_app_with_npm_start.yml
  160  ssh -i ~/.ssh/tech515-sherin-aws.pem ubuntu@172.31.49.101
  161  sudo nano prov_app_with_pm2.yml
  162  ansible-playbook prov_app_with_pm2.yml
  163  ssh -i ~/.ssh/tech515-sherin-aws.pem ubuntu@172.31.49.101
  164  ansible all -m ping
  165  cd /etc/ansible
  166  ls
  167  sudo nano prov-db.yml
  168  ansible-playbook prov-db.yml --check
  169  ansible-playbook prov-db.yml
  170  ansible db -a "systemctl status mongod"
  171  ansible db -a "grep -E '^\s*bindIp' /etc/mongod.conf || true"
  172  ansible db -i /etc/ansible/hosts -m shell -a "grep -E '^\s*bindIp' /etc/mongod.conf || true"
  173  ansible db -m shell -a "grep -E '^\s*bindIp' /etc/mongod.conf || true"
  174  ansible db -m shell -a "ss -tuln | grep 27017 || true"
  175  ssh -i ~/.ssh/tech515-sherin-aws.pem ubuntu@172.31.49.101
  176  ls
  177  sudo nano prov-app-all.yml
  178  ansible-playbook prov-app-all.yml -vvv
  179  sudo nano prov-app-all.yml
  180  ansible-playbook prov-app-all.yml
  181  sudo nano prov-app-all.yml
  182  ansible-playbook prov-app-all.yml
  183  sudo nano prov-app-all.yml
  184  ansible-playbook prov-app-all.yml
  185  ansible db -a "systemctl status mongod"
  186  ansible db -m shell -a "grep -E '^\s*bindIp' /etc/mongod.conf || true"
  187  ansible web -m shell -a "pm2 list || true"
  188  ansible web -m shell -a "ss -tuln | grep 3000 || true"
  189  curl http://3.253.54.84/posts
  190  ssh -i ~/.ssh/tech515-sherin-aws.pem ubuntu@172.31.49.101
  191  ansible-playbook prov-app-all.yml
  192  ansible db -a "systemctl status mongod"
  193  ansible db -m shell -a "grep -E '^\s*bindIp' /etc/mongod.conf || true"
  194  ansible web -m shell -a "ss -tuln | grep 3000 || true"
  195  ansible web -m shell -a "pm2 list || true"
  196  ansible web -m shell -a "lsof -i :3000 || true"
  197  ansible web -m shell -a "pm2 kill"
  198  ansible web -m shell -a "pm2 delete all"
  199  ansible web -m shell -a "pm2 list"
  200  ansible-playbook prov-app-all.yml
  201  ansible web -m shell -a "pm2 list"
  202  ansible web -m shell -a "echo \$DB_HOST"
  203  sudo rm prov-app-all.yml 
  204  ls
  205  sudo nano prov-app-all.yaml
  206  ansible-playbook prov-app-all.yml
  207  sudo rm prov-app-all.yaml 
  208  sudo nano prov-app-all.yml
  209  ansible-playbook prov-app-all.yml
  210  sudo nano prov-app-all.yml
  211  ansible-playbook prov-app-all.yml
  212  ansible web -m shell -a "pm2 list"
  213  ansible web -m shell -a "pm2 env sparta-app"
  214  pm2 delete sparta-app || true
  215  pm2 kill
  216  sudo nano prov-app-all.yml
  217  sudo rm prov-app-all.yml 
  218  sudo nano prov-app-all.yml
  219  ansible-playbook prov-app-all.yml
  220  ansible web -m shell -a "pm2 list"
  221  ansible web -m shell -a "echo \$DB_HOST"
  222  sudo rm prov-app-all.yml 
  223  sudo nano prov-app-all.yml
  224  ansible-playbook prov-app-all.yml
  225  ansible db -m service -a "name=mongod state=started"
  226  ansible db -m shell -a "grep bindIp /etc/mongod.conf"
  227  ssh -i ~/.ssh/tech515-sherin-aws.pem ubuntu@172.31.49.101

```