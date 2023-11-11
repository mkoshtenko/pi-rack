# Setup monitoring(mon) Pi
This Pi host will be responsible for monitoring the entire homelab environment and collecting logs from all other hosts.
The host will not have an SD card and will instead boot from an SSD drive to have more data storage with better I/O performance.

- IP: 192.168.1.16
- hostname: millie
- subdomain: mon.pi.lab

Hardware:
- RPi 4 Model B
  - RAM 8GB
  - Quad core Cortex-A72 (ARM v8) 64-bit SoC @ 1.8GHz
- SATA SSD WD Green 480GB or any other. Use WD Red/Black if you anticipate extensive I/O
- (optional) Uctronics Pi Rack PRO tray. Tested with:
  - UC-906 Rev.H6

## 1. OS Installation
Use the [Raspberry Pi Imager](https://www.raspberrypi.com/software/) to install necessary OS directly to the SSD drive. No SD card needed for this Pi host.

- Latest Raspberry Pi OS Lite (64-bit). tested with:
  -  Debian 12 (bookworm)
- Set hostname(e.g. `millie`) to make it accessible by `millie.local` after successful boot.
- Choose username and password.
- Enable public-key ssh access during installation or choose password-based ssh and use `ssh-copy-id` to setup public-key ssh after boot. This is required for using ansible playbooks.

## 2. Set static IPv4 address
Note: this works on Debian 12 (bookworm) based Raspberry Pi OS version.

List all profiles using `sudo nmcli -p connection show`.
Output example:
```
======================================
  NetworkManager connection profiles
======================================
NAME                UUID                                  TYPE      DEVICE
------------------------------------------------------------------------------------------------------------------
Wired connection 1  24223dc9-0a19-3c46-81a2-375b3a52213b  ethernet  eth0
lo                  1282547d-dacf-49ae-bd1a-6bc28f3c8709  loopback  lo
```

Set the configuration with the following commands:
```
sudo nmcli con mod "Wired connection 1" ipv4.addresses 192.168.1.16/24 ipv4.method manual
sudo nmcli con mod "Wired connection 1" ipv4.gateway 192.168.1.1
sudo nmcli con mod "Wired connection 1" ipv4.dns "192.168.1.2,192.168.1.1"
```

Restart the Pi with `sudo reboot`.

Verify changes using `ifconfig` or `nmcli -p connection show "Wired connection 1"`.
Output example:
```
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

## 3. Check the mounted partitions (optional)
List all connected block devices using the `lsblk` command.
```
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sda      8:0    0 447.1G  0 disk
├─sda1   8:1    0   512M  0 part /boot/firmware
└─sda2   8:2    0 446.6G  0 part /
```

Use the ‘df -h’ command to verify that the partitions are successfully mounted and listed in the output.
```
Filesystem      Size  Used Avail Use% Mounted on
udev            3.8G     0  3.8G   0% /dev
tmpfs           782M  1.1M  780M   1% /run
/dev/sda2       440G  2.1G  416G   1% /
tmpfs           3.9G     0  3.9G   0% /dev/shm
tmpfs           5.0M   12K  5.0M   1% /run/lock
/dev/sda1       510M   61M  450M  12% /boot/firmware
tmpfs           782M     0  782M   0% /run/user/1000
```

## 4. Use ansible to prepare the host
Note: All the following ansible(ansible-playbook) commands should be executed from the root folder of this repo.

Make sure you have this host in the devices.ini file with correct IP and user name.
Example:
```
[mon]
millie ansible_host=192.168.1.16
```

Do the initial setup by running the following command. This will perform updates and install necessary software like git.
```
ansible-playbook -i devices.ini -l mon ansible/rpi-os-lite-basic-playbook.yml
```

The following command will prapare the LCD display and Power button from the Uctronics Pi Rack PRO mount. 
Do NOT run it if you don't use this rack mount hardware in your lab setup.
```
ansible-playbook -i devices.ini -l mon ansible/rpi-os-rack-display-setup-playbook.yml
```

We need a docker environment on the host to run some software products like Zabbix in a container. 
The following command will install docker and docker-compose with necessary optimizations.
```
ansible-playbook -i devices.ini -l mon ansible/docker-playbook.yml
```

Prepare necessary folder structure and execute `docker-compose-mon.yml` on the host by running:
```
ansible-playbook -i devices.ini ansible/mon-playbook.yml
```

# Zabbix
Zabbix is software that monitors numerous parameters of a network and the health and integrity of servers. Zabbix uses a flexible notification mechanism that allows users to configure e-mail based alerts for virtually any event. This allows a fast reaction to server problems. Zabbix offers excellent reporting and data visualisation features based on the stored data.

[Container installation manual](https://www.zabbix.com/documentation/current/en/manual/installation/containers)

## Zabbix Server
Zabbix server is the central process of Zabbix software.

The server performs the polling and trapping of data, it calculates triggers, sends notifications to users. It is the central component to which Zabbix agents and proxies report data on availability and integrity of systems. The server can itself remotely check networked services (such as web servers and mail servers) using simple service checks.

The docker exec command allows you to run commands inside a Docker container. 
The following command line will give you a bash shell inside your zabbix-server-pgsql container:
```
docker exec -ti zabbix-server /bin/bash
```
The Zabbix server log is available through Docker's container log:
```
docker logs zabbix-server
```

## Zabbix Web
Zabbix web interface is a part of Zabbix software. It is used to manage resources under monitoring and view monitoring statistics.

- Zabbix web-interface based on Nginx web server with PostgreSQL database support

The docker exec command allows to run commands inside a Docker container. 
The following command line will give you a bash shell inside your zabbix-web-nginx-pgsql container:
```
docker exec -ti zabbix-web /bin/bash
```

The Zabbix web interface log is available through Docker's container log:
```
docker logs zabbix-web
```

Use the user name `Admin` with password `zabbix` to log in as a Zabbix superuser. Access to all menu sections will be granted. 
More details: https://www.zabbix.com/documentation/current/en/manual/quickstart/login

## Zabbix Agent 2
Zabbix agent is deployed on a monitoring target to actively monitor local resources and applications (hard drives, memory, processor statistics, etc.).
The agent gathers operational information locally and reports data to Zabbix server for further processing. 

Note: At the time of writing, the newest supported Raspberry Pi OS version is 11 (Bullseye). I'm trying to make it work on 12 (Bookworm) until the official release is ready.

Download instructions for all supported host configurations: https://www.zabbix.com/download

Verify agent2 is enabled and running on the host:
```
systemctl status zabbix-agent2
```


