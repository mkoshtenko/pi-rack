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
## Playbooks
Initial setup for RPi OS:
```
# use local inventory
ansible-playbook --inventory-file all_rpios_devices.ini rpi-os-lite-basic-playbook.yml
# or global inventory
ansible-playbook --inventory-file ~/ansible.hosts rpi-os-lite-basic-playbook.yml
```

Setup for LCD and power button:
```
# use local inventory
ansible-playbook --inventory-file all_rpios_devices.ini rpi-os-rack-display-setup-playbook.yml
# or global inventory
ansible-playbook --inventory-file ~/ansible.hosts rpi-os-rack-display-setup-playbook.yml
```

## TODO:
- Use systemd instead of rc.local [example](https://github.com/UCTRONICS/SKU_RM0004/issues/3)
- Add ansible roles
