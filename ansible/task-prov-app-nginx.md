# Stage 3 – Configure Nginx Reverse Proxy

## **Objective**

The goal of this stage is to configure Nginx on the app node to act as a **reverse proxy**. All HTTP requests (port 80) will be forwarded to the Node.js application running on **port 3000**.

This allows users to access the app via the standard HTTP port without specifying the port explicitly.

---

## **Prerequisites**

* App node is running and reachable.
* Node.js (v20) and npm are installed on the app node.
* Application code is cloned into `/home/ubuntu/app`.
* Security group allows ports **22**, **80**, and **3000**.
* Nginx installed (via playbook task).

---

## **Tasks**

1. **Install and configure Nginx**

   * Create Nginx config file to forward traffic from port 80 to Node.js app on port 3000.
   * Ensure the default Nginx site is disabled.
   * Reload Nginx whenever configuration changes.

2. **Playbook Implementation**
   The following Ansible tasks are included in the playbook:

   ```yaml
   - name: Configure Nginx reverse proxy for Node.js app
     copy:
       dest: /etc/nginx/sites-available/sparta-app
       content: |
         server {
             listen 80;
             server_name _;

             location / {
                 proxy_pass http://localhost:3000;
                 proxy_http_version 1.1;
                 proxy_set_header Upgrade $http_upgrade;
                 proxy_set_header Connection 'upgrade';
                 proxy_set_header Host $host;
                 proxy_cache_bypass $http_upgrade;
             }
         }
     notify: reload nginx

   - name: Enable sparta-app site
     file:
       src: /etc/nginx/sites-available/sparta-app
       dest: /etc/nginx/sites-enabled/sparta-app
       state: link

   - name: Remove default Nginx site
     file:
       path: /etc/nginx/sites-enabled/default
       state: absent
     notify: reload nginx

   handlers:
     - name: reload nginx
       service:
         name: nginx
         state: reloaded
   ```

---

## **Execution**

Run the playbook from the controller:

```bash
ansible-playbook prov_app_with_pm2.yml
```

or

```bash
ansible-playbook prov_app_with_npm_start.yml
```

---

## **Verification**

1. SSH into the app node:

```bash
ssh -i "tech515-sherin-aws.pem" ubuntu@<app-node-ip>
```

2. Test Nginx configuration:

```bash
sudo nginx -t
```

3. Reload Nginx (if necessary):

```bash
sudo systemctl restart nginx
```

4. Test access via browser or curl:

```bash
curl http://<app-node-ip>
```

The app should now be accessible via **port 80**.