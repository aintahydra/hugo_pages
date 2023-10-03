---
title: "Run DVWA on a Kali VM on the apple silicon(M2)"
date: 2023-02-21T06:45:51+09:00
draft: false
tags: [dvwa,kali,vmware,fusion,apple,m2]
---

# Installing VMs

### Tip: VMWare-Tools for Ubuntu/Kali VM on VMWare Fusion
- Installing VMWare-Tools
    ```
    $ sudo apt install open-vm-tools
    ```
- when shared folder doesn't work even after the vmware-tools are installed
    ```
    $ sudo umount /mnt/hgfs
    $ sudo mount -t fuse.vmhgfs-fuse .host:/ /mnt/hgfs -o allow_other
    ```

### Install Kali (.iso) on VMWare Fusion on Apple silicon(M2)
- Download Kali .iso for Apple silicon: https://cdimage.kali.org/kali-2023.2/kali-linux-2023.2a-installer-arm64.iso
- Install how-to: https://geekflare.com/kali-linux-installation-guide-vmware/
    - Installation method: "Install from disc or image".
    - Create a new VM with the downloaded _kali*.iso_.
    - When the "Finish" window pops up, click "Customized Settings".
    - Click the "Hard Disk" icon, and increase the disk space to 15 ~ 20 GB.
- Optional, Set Network "Bridged" (?)

### Install DVWA on the Kali VM; Run it. 
- Simple: `sudo apt install dvwa`
- then just `$ dvwa start` or `$ dvwa stop`
- The follwoings may not be needed:
    - If needed, `sudo apt -y install apache2 mariadb-server php php-mysqli php-gd libapache2-mod-php`
    - Check the `config.inc.php.dist` file
        - run `dpkg -L dvwa | grep config.inc.php.dist`, and find where the file is (in my case, it's under `/etc/dvwa/config`).
        - check if the configurations are right as follows:
        ```
        $ cat /etc/dvwa/config/config.inc.php
        ...
        $_DVWA = array();
        $_DVWA[ 'db_server' ]   = '127.0.0.1';
        $_DVWA[ 'db_database' ] = 'dvwa';
        $_DVWA[ 'db_user' ]     = 'dvwa';
        $_DVWA[ 'db_password' ] = 'p@ssw0rd';
        $_DVWA[ 'db_port'] = '3306';
        ...
        ```
    - Check if apache is running and run it if not. 
        ```
        $ sudo service apache2 status
        $ sudo service apache2 start 
        ```


### Install (Docker) DVWA on the Kali VM

- Install and execute the Docker Desktop
    - ref: https://hub.docker.com/r/vulnerables/web-dvwa
    - ref: https://docs.docker.com/desktop/install/mac-install/
- Double click `Docker.dmg` and Execute `Docker.app` from Applications

- Run a DVWA container. 
    ```
    $ docker run --rm -it -p 80:80 vulnerables/web-dvwa
    ```
- then, visit http://127.0.0.1
    - Login with admin/password
    - Press the 'Create/Reset database' button.
    - Set the difficulty level.

