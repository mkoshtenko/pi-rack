# Content for DNS Pi

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
- `pihole -g` returns DNS resolution is currently unavailable error
Make sure `/etc/resolv.conf` inside the container has the following content:
```
search home
nameserver 127.0.0.1
```
