## **1️⃣ How master playbooks work**

In Ansible, a master playbook is just a playbook that uses the `import_playbook` directive to include other playbooks.

* **Execution order**: Playbooks run **sequentially**, one after the other. They do **not run simultaneously**.
* **Failure behavior**:

  * If a playbook included by the master fails, by default **execution stops**, and the next playbooks are **not run**.
  * You can use `ignore_errors: yes` for individual tasks or playbooks if you want to continue on failure, but generally, stopping on failure is safer.

---

## **2️⃣ Create a master playbook**

You want a `master-playbook.yml` that runs:

1. `prov-db.yml` (database provisioning)
2. `prov-app.yml` (app provisioning)

Here’s a simple example:

```yaml
---
# master-playbook.yml
- import_playbook: prov-db.yml
- import_playbook: prov-app.yml
```

That’s it! ✅

**Notes:**

* `import_playbook` includes the playbooks at **parse time**, so tasks are executed in order.
* The playbooks themselves should handle any dependencies (like waiting for the DB to be ready before the app starts).

---

## **3️⃣ Running the master playbook**

From your controller machine:

```bash
ansible-playbook master-playbook.yml
```

* Make sure `inventory.ini` has your `web` and `db` hosts defined.
* After it finishes, check the app: `/posts` page should load and show data if the `prov-db.yml` playbook seeded it properly.