# Reconfiguring PiHole

I had previously removed Pi Hole from my homelab after a power outage knocked out Pi Hole for the second time.
Now that I have purchase 2 UPS units, I am reintroducing Pi Hole to my homelab.

I need to find the IP address of my Pi Hole, so I will use the nmap tool to scan my subnet for the device:

```bash
sudo nmap -sn 192.168.1.0/24 | grep -B2 "Raspberry Pi"
```
found IP address

connected to device via Mac terminal
```
ssh user@192.168.1.X
```

ran updates
```
sudo apt update && sudo apt ugprade
```
output:
```
You might want to run 'apt --fix-broken install' to correct these.
Unsatisfied dependencies:
 libpython3.13 : Depends: libpython3.13-stdlib (= 3.13.5-2) but 3.13.5-2+deb13u2 is installed
```
ran 'apt --fix-broken install'
```
sudo apt --fix-broken install
```
output:
```
Upgrading:
  libpython3.13

Summary:
  Upgrading: 1, Installing: 0, Removing: 0, Not Upgrading: 77
  1 not fully installed or removed.
  Download size: 0 B / 1,805 kB
  Space needed: 0 B / 26.2 GB available

Continue? [Y/n] y
Reading changelogs... Done
(Reading database ... 112278 files and directories currently installed.)
Preparing to unpack .../libpython3.13_3.13.5-2+deb13u2_armhf.deb ...
Unpacking libpython3.13:armhf (3.13.5-2+deb13u2) over (3.13.5-2) ...
Setting up libpython3.13:armhf (3.13.5-2+deb13u2) ...
Processing triggers for libc-bin (2.41-12+rpt1+deb13u2) ...
```
```
sudo apt autoremove
```
```
sudo apt update && sudo apt upgrade
```
output:
```
...
BEGIN failed--compilation aborted at /usr/lib/arm-linux-gnueabihf/perl-base/File/Temp.pm line 152.Compilation failed in require at /usr/bin/deb-systemd-helper line 92.BEGIN failed--compilation aborted at /usr/bin/deb-systemd-helper line 92.Bus errordpkg: cannot write to log file '/var/log/dpkg.log': Read-only file systemdpkg: unrecoverable fatal error, aborting: unable to flush updated status of 'raspberrypi-sys-mods': Read-only file systemError: Sub-process /usr/bin/dpkg returned an error code (2)Warning: Problem unlinking the file /var/cache/apt/pkgcache.bin - pkgDPkgPM::Go (30: Read-only file system)
```

This error is familiar from the last time I ssh'd into the PiHole. There was a power outage that corrupted the SD card, which will not be an easy fix. Because of this, I am going to setup the PiHole again.

## Reinstalling Pi-Hole

- reinstalled debian trixie using the raspberry pi imager
- found the raspberry pi's IP address via `nmap`

updating the OS
```
sudo apt update && sudo apt upgrade -y
```

setting the static IP

```
sudo nmcli con mod "Wired connection 1" ipv4.addresses 192.168.1.190/24
sudo nmcli con mod "Wired connection 1" ipv4.gateway 192.168.1.1
sudo nmcli con mod "Wired connection 1" ipv4.dns 192.168.1.1
sudo nmcli con mod "Wired connection 1" ipv4.method manual
sudo nmcli con up "Wired connection 1"
```

running the PiHole installer
```
curl -sSL https://install.pi-hole.net | bash
```

following the prompts and applying changes as useful for the homelab

Changed the admin password and added new lists from URL: https://github.com/hagezi/dns-blocklists#tif

running an update for the lists
```
pihole -g
```

Lists are now active and DNS queries are being blocked!!
