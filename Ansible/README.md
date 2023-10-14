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
pi.mydomain.com ansible_user=admin
```

Create local inventory and modify content
```
cp all_rpios_devices.ini devices.ini
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
ansible-playbook --inventory-file devices.ini rpi-os-lite-basic-playbook.yml
```

Setup for LCD and power button:
```
ansible-playbook --inventory-file devices.ini rpi-os-rack-display-setup-playbook.yml
```

Install Docker
```
ansible-playbook --inventory-file devices.ini docker-playbook.yml
```
## TODO:
- Use systemd instead of rc.local [example](https://github.com/UCTRONICS/SKU_RM0004/issues/3)
- Add ansible roles
