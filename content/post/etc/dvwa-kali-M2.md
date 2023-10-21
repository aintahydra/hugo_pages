---
title: "Run DVWA on a Kali VM"
date: 2023-02-21T06:45:51+09:00
draft: false
tags: [dvwa,kali,vmware,fusion,apple,m2]
---

===========
## CAUTION - DO NOT be bothered by manually installing DVWA (Hightly possibly, it will fail).

===========
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

 Install Docker on Kali

- ref: https://www.kali.org/docs/containers/installing-docker-on-kali/
```
$ sudo apt update
$ sudo apt install -y docker.io
$ sudo systemctl enable docker --now 

#add the current user to the docker group
$ sudo usermod -aG docker $USER
$ cat /etc/group | grep docker 

# (... after LOGOUT, and re-LOGIN)
$ id 
... there should be docker GID in the printed GID list

#test
$ docker images
```

===========
# Install DVWA (manual and dockerized versions)

## (Manual) Install a DVWA linux package on the Kali VM
- Simple: `sudo apt install dvwa`
- then just `$ dvwa start` or `$ dvwa stop`
- The followings may not be needed:
    - If needed, `sudo apt -y install apache2 mariadb-server php php-mysqli php-gd libapache2-mod-php`
    - Check the `config.inc.php.dist` file
        - run `dpkg -L dvwa | grep config.inc.php.dist`, and find where the file is (in my case, it's under `/etc/dvwa/config`).
        - check if the configurations are right as follows:
        ```
        $ cat /etc/dvwa/config/config.inc.php
        ...
        $_DVWA = array();
        $_DVWA[ 'db_server' ]   = '127.0.0.1';
        #$_DVWA[ 'db_database' ] = 'dvwa';
        $_DVWA[ 'db_database' ] = 'userDVWA';
        $_DVWA[ 'db_user' ]     = 'dvwa';
        #$_DVWA[ 'db_password' ] = 'p@ssw0rd';
        $_DVWA[ 'db_password' ] = 'dvwa';
        $_DVWA[ 'db_port'] = '3306';
        ...
        ```
    - Check if apache is running and run it if not.
        ```
        $ sudo service apache2 status
        $ sudo service apache2 start
        ```
- Setup MySQL (https://nooblinux.com/how-to-install-dvwa/))
    - check MySQL status and run it if needed: `sudo systemctl start mysql`
    - set up the root passwd: `sudo mysql -u root -p` (I use 'toor' as the root password)
    - create a user: `MariaDB [(none)]> create user 'userDVWA'@'127.0.0.1' identified by "dvwa";`
    - grant permissions: `grant all privileges on dvwa.* to 'userDVWA'@'127.0.0.1' identified by 'dvwa';`

- Setup Apache
    - sudo vi /etc/php/8.2/apache2/php.ini
    ```
    allow_url_fopen = On
    ...
    #allow_url_include = Off
    allow_url_include = On
    ```  
    - sudo systemctl restart apache2


- Check up configuration
    - visit: http://127.0.0.1:42001/setup.php
    - if everything looks good, press "Create / Reset Database"


## (Dockerized) Install dockerized-DVWA (amd64)

- ref: https://hub.docker.com/r/vulnerables/web-dvwa

```
# possibly omit the following
($ docker pull vulnerables/web-dvwa)
...

$ docker run --rm -it -p 42001:80 vulnerables/web-dvwa 

$ docker ps    

$ docker stop XXXXXX(vulnerables/web-dvwa)

$ docker images
```

## <strike> Install (Docker-ized) DVWA on the Kali VM for apple silicons (M2) </strike> (not working)

- Install and execute the Docker Desktop
    - ref: https://docs.docker.com/desktop/install/mac-install/
- Double click `Docker.dmg` and Execute `Docker.app` from Applications

- Get dockerized DVWA: `docker pull vulnerables/web-dvwa`

- Run a DVWA container (with 42001 port)
    ```
    $ docker images                  
    REPOSITORY             TAG       IMAGE ID       CREATED       SIZE
    vulnerables/web-dvwa   latest    ab0d83586b6e   4 years ago   712MB

    $ docker run --rm -it -p 42001:80 ab0d  
    # or
    $ docker run --rm -it -p 42001:80 vulnerables/web-dvwa
    ```
- then, visit http://127.0.0.1:42001
    - Login with admin/password
    - Press the 'Create/Reset database' button.
    - Set the difficulty level.