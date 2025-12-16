``` 
    1  sudo apt update
    2  sudo apt upgrade -y
    3  sudo apt-add-repository ppa:ansible/ansible
    4  sudo apt-get update
    5  sudo apt install ansible -y
    6  ansible --version
    7  cd /etc/ansible/
    8  ls
    9  cd
   10  ls
   11  ls -a
   12  cd .ssh
   13  ls
   14  nano tech515-sherin-aws.pem
   15  cat tech515-sherin-aws.pem
   16  nano tech515-sherin-aws.pem
   17  ls -l
   18  chmod 400 tech515-sherin-aws.pem
   19  ls -l
   20  ssh -i "tech515-sherin-aws.pem" ubuntu@172.31.49.101
   21  nano tech515-sherin-aws.pem
   22  ls -l
   23  ssh -i "tech515-sherin-aws.pem" ubuntu@172.31.49.101
   24  rm ~/.ssh/tech515-sherin-aws.pem
   25  ls -l
   26  nano tech515-sherin-aws.pem
   27  ls -l
   28  chmod 400 tech515-sherin-aws.pem
   29  ls -l
   30  ssh -i "tech515-sherin-aws.pem" ubuntu@172.31.49.101
   31  history
```


To go in hosts file:
```
ec2-instance ansible_host=172.31.49.101 ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/tech515-sherin-aws.pem

ec2-app-instance ansible_host=172.31.49.101 ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/tech515-sherin-aws.pem


ansible web -m copy -a "src=/home/ubuntu/.ssh/tech515-sherin-aws.pem dest=/home/ubuntu/.ssh/tech515-sherin-aws.pem mode=0600" -i /etc/ansible/hosts

```