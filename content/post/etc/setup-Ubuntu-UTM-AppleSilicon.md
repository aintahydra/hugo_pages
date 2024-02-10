---
title: "Run Ubuntu(amd64) with UTM on a Apple Silicon (M2) Macbook"
date: 2023-01-04T15:08:24
draft: false
tags: [Ubuntu,UTM,M2,Apple]
---

### Install 
Install a Ubuntu Server version(I installed the minimal Ubuntu 22.04 Server).


### Use the Shared Folder
Ref: https://docs.getutm.app/guest-support/linux/#virtfs

1. on the UTM Menu, "Edit" the configuration of the Ubuntu VM and set up a shared directory with the "VirtFS" (not SPECI WebDAV)
2. create a local directory on the guest
`$ sudo mkdir /media/share`
3. mount
`$ sudo mount -t 9p -o trans=virtio share /media/share -oversion=9p2000.L`



