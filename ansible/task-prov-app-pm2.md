# Stage 2 – Provision App with PM2 (Background Process)

## Goal

This playbook provisions an app VM, installs dependencies, and runs the Node.js app in the **background** using PM2, ensuring the app keeps running after SSH sessions end.

---

## Playbook

**Filename:** `prov_app_with_pm2.yml`

**Purpose:**

- Install required dependencies: Nginx, Node.js, npm
- Allow port 3000 for app access
- Clone the app repository from GitHub
- Install Node.js dependencies
- Install PM2 and run the app in background

---

## Hosts

- **Target group:** `web`
- **Ansible controller:** Your designated controller instance
- **App node:** The VM where the app will run

---

## Key Tasks

1. **Install Nginx**
   Ensures the web server is installed and ready.

2. **Install Node.js 20 and npm**
   Using Nodesource setup script to ensure the latest supported Node.js version.

3. **Allow port 3000**
   Configures firewall (UFW) to allow traffic to the app.

4. **Clone App Repository**
   Git clone into `/home/ubuntu/app` ensuring the latest code from GitHub.

5. **Install App Dependencies**
   Uses the `npm` Ansible module to install packages listed in `package.json`.

6. **Install PM2 Globally**
   PM2 ensures Node.js apps run as background processes (daemonized).

7. **Start App with PM2**
   Launches the app in the background under the process name `sparta-app`.

---

## How to Run

1. **Test Ansible connectivity**:

```bash
ansible all -m ping
```

2. **Run the playbook**:

```bash
ansible-playbook prov_app_with_pm2.yml
```

3. **Verify PM2 process on the app node**:

```bash
ssh -i "tech515-sherin-aws.pem" ubuntu@<app-node-ip>
pm2 list
```

4. **Access the app in browser**:

```
http://<app-node-ip>:3000
```

---

## Notes

- PM2 ensures the app continues running even after logout or SSH disconnection.
- All tasks use modules where possible for **idempotency**.
- Git cloning uses `force: yes` to always pull the latest version.
- Node.js 20 is used to satisfy modern package requirements and avoid engine errors.

---
