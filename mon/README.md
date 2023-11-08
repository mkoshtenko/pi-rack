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

## Check the mounted partitions
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


## Docker
Create .env inside the `mon/` folder and modify its content.
```
cd mon/
cp .env.example .env
```

