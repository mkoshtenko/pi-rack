# Ansible on OS X

```
brew install ansible    // install or update
```

config at: ~/.ansible.cfg

```
# config file for ansible -- https://ansible.com/
# ===============================================

[defaults]

# dafault value /etc/ansible/hosts
inventory = ~/ansible.hosts
```

inventory file example at: ~/ansible.hosts

```
[pi]
pi.mydomain.com ansible_user=piadmin
```

## Some commands
```
ansible all -m ping
ansible all -a 'uptime'
ansible pi -a "free -h"
```
