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

Copy the inventory file and modify content:
```
cp devices.ini.example devices.ini
```

## Some commands
```
ansible all -m ping
ansible all -a 'uptime'
ansible pi -a "free -h"
```

## Playbooks
Commands below assume execution from the root folder of the repo.

Initial setup for RPi OS:
```
ansible-playbook --inventory-file devices.ini ansible/rpi-os-lite-basic-playbook.yml
```

Setup for LCD and power button:
```
ansible-playbook --inventory-file devices.ini ansible/rpi-os-rack-display-setup-playbook.yml
```

Install Docker
```
ansible-playbook --inventory-file devices.ini ansible/docker-playbook.yml
```

## TODO:
- Use systemd instead of rc.local [example](https://github.com/UCTRONICS/SKU_RM0004/issues/3)
- Add ansible roles
- Make sure WiFi is disabled
- No swap and file-system caching
- Redirect logs
