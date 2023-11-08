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
- Set IPv4 address

/etc/dhcpcd.conf:
```
interface eth0
static ip_address=192.168.1.16/24
static routers=192.168.1.1
static domain_name_servers=192.168.1.2,192.168.1.1
```

## Docker
Create .env inside the `mon/` folder and modify its content.
```
cd mon/
cp .env.example .env
```

