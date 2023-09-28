---
title: "Install OpenVAS(GVM) on Kali"
date: 2023-03-04T20:16:46+09:00
draft: false
tags: [openvas,gvm]
---

# GVM? (or OpenVAS?)
- Greenbone Vulnerability Manager(GVM, a.k.a OpenVAS). OpenVAS is a fork of Nessus.

# Install GVM
1. How to install OpenVAS on Kali: `sudo apt install openvas`
2. tried `gvm-setup`, but Error occured:
    ```
    $ sudo gvm-setup                

    [>] Starting PostgreSQL service
    [-] ERROR: The default PostgreSQL version (15) is not 16 that is required by libgvmd
    [-] ERROR: Use pg_upgradecluster to update your PostgreSQL cluster
    ```
3. solve the problem
    ```
    $ psql --version                                                   
    psql (PostgreSQL) 16.0 (Debian 16.0-2)
    ```

    ```
    $ sudo vi /etc/postgresql/15/main/postgresql.conf
    ...
    port = 5433 # it was 5432
    ...
    ```

    and then, 
    ```
    $ sudo vi /etc/postgresql/16/main/postgresql.conf
    ...
    port = 5432 # it was 5433
    ...
    ```
    finally, restart postgresql

    ```
    $ sudo service postgresql restart
    $ sudo service postgresql status
    ```

4. restart gvm-setup
    ```
    $ sudo gvm-setup
    $ sudo gvm-check-setup
    $ sudo gvm-start  
    ```
5. Access the web interface
- visit https://localhost:9392 (or https://localhost:9392/login)

6. etc.
- run `sudo gvm-stop` if you want to stop it
- run `$ sudo -E -u _gvm -g _gvm gvmd --user=admin --new-password=<new passwd>` to change the password. In this case, run `$ id _gym` first to check if there are a user and a group named `_gym` exist. 

# ref
- https://www.linuxfordevices.com/tutorials/kali-linux/install-openvas
- https://stackoverflow.com/questions/67203580/installing-openvas-on-kali-debian-problem-with-postgresql-version
- https://forum.greenbone.net/t/reset-admin-password/4041/11

