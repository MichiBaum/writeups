# Automatic server updates with cron-apt

## Install cron-apt

    apt install cron-apt


## Configuration

All of the configuration files for cron-apt are located in the directory “/etc/cron-apt/” and the default crontab entry is located at “/etc/cron.d/cron-apt.” To change the frequency in which cron-apt checks for updates you will need to edit the crontab entry.  
To configure cron-apt to send e-mail notifications, open the file “/etc/cron-apt/config” and adjust the following variables.

    # Configuration for cron-apt. For further information about the possible
    # configuration settings see /usr/share/doc/cron-apt/README.gz.

    MAILON="always"
    MAILTO="sysadmin@example.com"