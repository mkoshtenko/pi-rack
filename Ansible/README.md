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
## Run playbooks
```
# use local inventory
ansible-playbook --inventory-file all_rpios_devices.ini rpi-os-lite-basic-playbook.yml
# or global inventory
ansible-playbook --inventory-file ~/ansible.hosts rpi-os-lite-basic-playbook.yml
```

