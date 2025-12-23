# commands

```
ls
cd /etc/ansible/
ansible.cfg hosts install_nginx.yml roles
sudo nano hosts
ansible all -m ping
ansible-playbook update_upgrade.yml
ansible ls
nano ansible.cfg
ansible-inventory
ansible-inventory --graph
ansible-inventory --list
ansible web -a "uname -a"
ansible web -a "date"
ansible web -m ansible.builtin.command -a "date"
ansible web -m command -a "date"
ansible web -m apt -a "update_cache=yes" --become
ansible web -m apt -a "upgrade=dist" --become
ansible web -m copy -a "src=/home/ubuntu/.ssh/tech515-sherin-aws.pem dest=/home/ubuntu/.ssh/tech515-sherin-aws.pem mode=0600" -i /etc/ansible/hosts
ansible web -m ping -i /etc/ansible/hosts
ansible web -i /etc/ansible/hosts -m stat -a "path=/home/ubuntu/.ssh/tech515-sherin-aws.pem"
ssh -i ~/.ssh/tech515-sherin-aws.pem ubuntu@172.31.49.101
ansible web -a "pwd"
ansible web -a "ls .ssh"
ansible web -a "ls -l .ssh"
ansible web -a "sudo systemctl status nginx"




ls -l ~/.ssh/tech515-sherin-aws.pem
chmod 400 /home/ubuntu/.ssh/tech515-sherin-aws.pem
```
