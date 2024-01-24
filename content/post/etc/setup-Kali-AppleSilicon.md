---
title: "Setup Kali VM on a Apple Silicon (M2) Macbook"
date: 2023-01-04T11:07:05
draft: false
tags: [Kali,VMWare,Fusion,M2,Apple]
---

Ref: https://www.kali.org/docs/virtualization/install-vmware-guest-tools/

# Install VMWare-Tools
```
$ sudo apt update
$ sudo apt install -y --reinstall open-vm-tools-desktop fuse
$ sudo reboot -f
```

# Use Share Folders
- Run Kali-tweaks
    ```
    kali@kali:~$ kali-tweaks
    ```
- Select Virtualization
- Mount the shared folder
```
$ sudo mount-shared-folders
```
- If it doesn't work restart the Tools
```
$ sudo restart-vm-tools
```
