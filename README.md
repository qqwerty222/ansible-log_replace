# Move /var/log file to new disk with ansible

This playbook has only one role.


## Instruction to reproduce

At first create VM and map port to be able to connect via ssh

Generate ssh keypair
```bash
$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/bohdan/.ssh/id_rsa): /home/bohdan/.ssh/ansible-log_replace
```

Send public key to VM
```bash
$ scp -P2230 /home/bohdan/ansible-log_replace bohdan@172.19.80.1:/home/bohdan/.ssh/key.pub
```

Connect to VM using password
```bash
$ ssh -p2230 bohdan@172.19.80.1
```

Add public key to authorized_keys
```bash
$ cat ~/.ssh/key.pub >> ~/.ssh/authorized_keys
```

Allow user execute sudo without password
```bash
$ sudo visudo 
...
@includedir /etc/sudoers.d
bohdan ALL=(ALL) NOPASSWD:ALL
```

Reconnect with private-key
```bash
$ exit
$ ssh -p2230 -i ~/.ssh/ansible-log_replace bohdan@172.19.80.1
```

## Run playbook

After you can connect new disk, in my case it is VHD in VirtualBox 

Add target machine ip into ./hosts
```ini
[servers]
172.19.80.1 
```

Specify access parameters into groups_vars/servers.yml
```yaml
---
ansible_user: bohdan
ansible_port: 2230
ansible_private_key_file: /home/bohdan/.ssh/ansible-log_replace

dev_name: sdb
source_dir: /var/log
```

Also you can encrypt this file to hide sensitive information
```bash
$ ansible-vault encrypt group_vars/server.yml
```

When all preparation done start playbook with:
```bash
$ ansible-playbook --ask-vault-pass playbook.yml
```