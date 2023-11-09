# Setup DNS(ns) Pi

Hardware:
- RPi 4 Model B
  - RAM 4GB
  - Quad core Cortex-A72 (ARM v8) 64-bit SoC @ 1.8GHz

OS Installation:
- Latest Raspberry Pi OS Lite (64-bit). tested with:
  -  Debian 11 (bullseye)
  -  Debian 12 (bookworm)
- Set hostname
- Set IPv4 address
- Enable ssh access

host: kleo
IP: 192.168.1.2


## Set IPv4 address
### bullseye
/etc/dhcpcd.conf:
```
interface eth0
static ip_address=192.168.1.2/24
static routers=192.168.1.1
static domain_name_servers=192.168.1.2
```
### bookworm
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
sudo nmcli con mod "Wired connection 1" ipv4.addresses 192.168.1.2/24 ipv4.method manual
sudo nmcli con mod "Wired connection 1" ipv4.gateway 192.168.1.1
sudo nmcli con mod "Wired connection 1" ipv4.dns "192.168.1.2"
```

Restart the Pi with `sudo reboot` and verify changes:
```
nmcli -p connection show "Wired connection 1"

...
-------------------------------------------------------------------------------
ipv4.method:                            manual
ipv4.dns:                               192.168.1.2
ipv4.dns-search:                        --
ipv4.dns-options:                       --
ipv4.dns-priority:                      0
ipv4.addresses:                         192.168.1.2/24
ipv4.gateway:                           192.168.1.1
...
```

## Docker
Create .env and modify content.
```
cp .env.example .env
```

## Traefik
Create .htpasswd file and generate password for traefik web UI base auth
```
cp .htpasswd.example .htpasswd
```

## Unbound
Create a-records.conf file and list your local DNS records
```
cp a-records.conf.example a-records.conf
```
### Troubleshooting
work inside the container with `docker exec -it pihole bash`
- Unable to save any configs from PiHole admin page

Make sure the following config is NOT set for the pihole container inside the docker-compose file:
```
security_opt:
  - no-new-privileges:true
```

- `pihole -g` returns DNS resolution is currently unavailable error

Make sure `/etc/resolv.conf` inside the container has the following content:
```
search home
nameserver 127.0.0.1
```
