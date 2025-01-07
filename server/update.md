# Update Server

Guide to update Debian-based server systems.

## Update package lists

```bash
sudo apt update
```

## Perform system upgrades

```shell
sudo apt upgrade
sudo apt dist-upgrade
```

## Reboot server

```shell
sudo reboot
```


## List all installed packages with versions

```shell
dpkg -l

dpkg -l | grep '^ii'
```


## Ubuntu Release Upgrade

```shell
fdisk -l
lsblk
df -h

lsb_release -a
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 20.04.6 LTS
Release:        20.04
Codename:       focal

uname -r
5.4.0-198-generic

ls /boot | grep vmlinuz | cut -d'-' -f2,3
vmlinuz
5.4.0-182
5.4.0-198
vmlinuz.old


dpkg -l | grep ^ii | grep 5.4.0-107 | awk -F' ' '{ print $2 }'
linux-headers-5.4.0-107
linux-headers-5.4.0-107-generic
linux-image-5.4.0-107-generic
linux-modules-5.4.0-107-generic
linux-modules-extra-5.4.0-107-generic

apt-get remove linux-headers-5.4.0-182 linux-headers-5.4.0-182-generic linux-image-5.4.0-182-generic linux-modules-5.4.0-182-generic linux-modules-extra-5.4.0-182-generic
apt autoremove --purge

sudo do-release-upgrade

uname -r
5.15.0-126-generic

ls /boot | grep vmlinuz | cut -d'-' -f2,3
vmlinuz
5.15.0-126
5.4.0-198
vmlinuz.old

lsb_release -a
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 22.04.5 LTS
Release:        22.04
Codename:       jammy
```