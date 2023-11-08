# Setup monitoring(mon) Pi

Hardware:
- RPi 4 Model B
  - RAM 8GB
  - Quad core Cortex-A72 (ARM v8) 64-bit SoC @ 1.8GHz
  - SATA SSD WD Green (or similar) 480GB

host: millie
IP: 192.168.1.16

OS Installation:
- Latest Raspberry Pi OS Lite (64-bit). tested with:
  -  Debian 12 (bookworm)
- Set hostname
- Enable ssh access during installation
- Use ssh-copy-id to setup passwordless ssh for ansible

## Set IPv4 address

List all profiles:
```
sudo nmcli -p connection show

======================================
  NetworkManager connection profiles
======================================
NAME                UUID                                  TYPE      DEVICE
------------------------------------------------------------------------------------------------------------------
Wired connection 1  24223dc9-0a19-3c46-81a2-375b3a52213b  ethernet  eth0
lo                  1282547d-dacf-49ae-bd1a-6bc28f3c8709  loopback  lo
```

Set the configuration
```
sudo nmcli con mod "Wired connection 1" ipv4.addresses 192.168.1.16/24 ipv4.method manual
sudo nmcli con mod "Wired connection 1" ipv4.gateway 192.168.1.1
sudo nmcli con mod "Wired connection 1" ipv4.dns "192.168.1.2,192.168.1.1"
```

Restart the Pi
```
sudo reboot
```

Verify changes:
```
nmcli -p connection show "Wired connection 1"

...
-------------------------------------------------------------------------------
ipv4.method:                            manual
ipv4.dns:                               192.168.1.2,192.168.1.1
ipv4.dns-search:                        --
ipv4.dns-options:                       --
ipv4.dns-priority:                      0
ipv4.addresses:                         192.168.1.16/24
ipv4.gateway:                           192.168.1.1
...
```

## Docker
Create .env inside the `mon/` folder and modify its content.
```
cd mon/
cp .env.example .env
```

