# pi-rack
Scripts and tools for Raspberry Pi devices in a home-lab environment

## Rack Mount
UCTRONICS Pi Rack Pro for Raspberry Pi 4B, 19" 1U Rack Mount, Support for 4 2.5" SSDs, Secure Shutdown, 0.96" Color Display for Raspberry Pi
Guide for display configuration on Bullseye OS & Ubuntu OS: https://github.com/UCTRONICS/SKU_RM0004

## Router
[RPi Compute Module 4 IoT Router Broad Mini](https://wiki.dfrobot.com/Compute_Module_4_IoT_Router_Board_Mini_SKU_DFR0767)

Note: Requires a custom adapter for the rack mount

## DNS
Docker
- Pi-Hole - add-blocker + cache
  - Handle client DNS requests
  - Delegate requests to the local Unbound installation
- Unbound - a secure recursive DNS server
  - Listen only for queries from the local Pi-hole installation (on port 5335)
  - Listen for both UDP and TCP requests
  - Verify DNSSEC signatures, discarding BOGUS domains
 
Links:
- [pi-hole in a docker container](https://github.com/pi-hole/docker-pi-hole)
- [pi-hole docs for unbound](https://docs.pi-hole.net/guides/dns/unbound/)

## VPN
Pi-VPN

## Monitoring
Zabbix
