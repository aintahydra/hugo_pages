---
title: "Run the REMnux VMs on a Apple Silicon (M2) Macbook"
date: 2023-01-05T07:26:12
draft: false
tags: [remnux,apple,OSX,M2,UTM]
---

I tried to run REMnux VM with the following settings:
- Macbook pro (M2)
- Mac OSX Sonoma
- UTM 4.4.5 (brew installed; `brew install --cask utm`)
- Downloaded the REMNux VM (from https://remnux.org/#distro)

# References

1. https://www.technomancer.com/archives/568
2. https://www.youtube.com/watch?v=krGvTMq29gA

## Convert the format of the REMNux disk image
`$ qemu-img convert -p -f vmdk -O vhdx REMnuxVM.vmdk REMnuxVM.vhdx`
    * `-p`: indicates the conversion progress.
    * `-f`: indicates the source image format.

## Import the converted disk image

1. run UTM
2. on the Welcome windows, choose "Create a New Virtual Machine"
3. on the Start window, choose the slow "Emulate" (not "Virtualize")
4. on the Operating System window, choose "Other" (not "Windows" nor "Linux")
5. on the Other window, 
    - check "Skip ISO boot"
6. on the "Hardware", leave the default options (must be like below:
    - Architecture: x86_64,
    - System: Standard PC(Q35 + ICH9, 2009) (alias of pc-q35-7.2) q35
    - Memory: 4G
    - CPU Cores: Default)
7. on the "Storage" window, specify the size as "1GB"
8. on the "Shared Directory" window, just proceed to the "next" step leaving nothing checked
9. on the "Summary" window, name the VM and check the "Open VM Settings"
10. On the setting menu, I adjusted a couple of options to make the VM less-useless
    - System
        - Check "Force Multicore" and assign more cores (4 in my case)
    - QEMU
        - Uncheck "UEFI Boot"
11. There are two IDE Drives displayed on the left menu
    - Leave the CD/DVD one (the first IDE Drive, in my case)
    - Choose the Disk Image one (The second IDE Drive, in my case), and "Delete"
    - Then, create a "New..." drive
        - Interface: IDE
        - Press "Import" and choose the converted ".vhdx" disk image
    - "Save" all the changes

The emulated VM will work but must be very slow. Be patient.
