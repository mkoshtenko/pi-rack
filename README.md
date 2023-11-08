# pi-rack
Scripts and tools for Raspberry Pi devices in a home-lab environment

## Rack Mount
UCTRONICS Pi Rack Pro for Raspberry Pi 4B, 19" 1U Rack Mount, Support for 4 2.5" SSDs, Secure Shutdown, 0.96" Color Display for Raspberry Pi
Guide for display configuration on Bullseye OS & Ubuntu OS: https://github.com/UCTRONICS/SKU_RM0004

## Router
[RPi Compute Module 4 IoT Router Broad Mini](https://wiki.dfrobot.com/Compute_Module_4_IoT_Router_Board_Mini_SKU_DFR0767)

Note: Requires a custom adapter for the rack mount

## DNS
Check [ns README](ns/README.md) for more setup info

Overview:
- [Pi-Hole](https://pi-hole.net/) - add-blocker + cache
  - Handle client DNS requests
  - Delegate requests to the local Unbound installation
- [Unbound](https://unbound.docs.nlnetlabs.nl/en/latest/) - a secure recursive DNS server
  - Listen only for queries from the local Pi-Hole installation 
  - Listen for both UDP and TCP requests
  - Verify DNSSEC signatures, discarding BOGUS domains
- [Traefik](https://doc.traefik.io/traefik/getting-started/quick-start/) - reverse proxy
  - Redirect port 80 to the local Pi-Hole
  - TLS termination
 
Links:
- [pi-hole in a docker container](https://github.com/pi-hole/docker-pi-hole)
- [unbound pi-hole docs](https://docs.pi-hole.net/guides/dns/unbound/)
- [traefik docker guide](https://www.smarthomebeginner.com/traefik-docker-compose-guide-2022/)

## VPN
Pi-VPN

## Monitoring
Check [mon README](mon/README.md) for more setup info

Zabbix
