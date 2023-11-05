# Content for DNS Pi

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

/etc/dhcpcd.conf:
```
interface eth0
static ip_address=192.168.1.2/24
static routers=192.168.1.1
static domain_name_servers=192.168.1.2
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

## Pihole
Create pihole-custom.list file and list your local DNS records
```
cp pihole-custom.list.example pihole-custom.list
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
