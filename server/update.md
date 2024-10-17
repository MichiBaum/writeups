# Update Server

Guide to update Debian-based server systems.

## Update package lists

    sudo apt update

## Perform system upgrades

    sudo apt upgrade
    sudo apt dist-upgrade

## Reboot server

    sudo reboot











## List all installed packages with versions

    dpkg -l

    dpkg -l | grep '^ii'

