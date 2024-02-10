---
title: "Android-x86 and Frida with VirtualBox on Linux(amd64)"
date: 2024-01-31T11:02:34+09:00
draft: false
tags: [android,android-x86,virtualbox,frida,ubuntu]
---


# Install Android-x86 with VirtualBox on Ubuntu 22.04

References:
- https://www.android-x86.org/documentation/virtualbox.html

1. Download an ISO image (https://sourceforge.net/projects/android-x86/files/Release%209.0/ )
    * `android-x86_64-9.0-r2.iso` , in my case. 
2. Create a new VirtualBox VM
    - Type: Linux, Version: Linux 2.6/3.x/4.x/5.x (64-bit)
    - Hardware: Assign 4GB RAM
    - Hard Disk:
        - Create a Virtual Hard Disk Now
        - Give 8GB HDD
        - VDI (do not check Pre-allocate Full Size)
3. Run VM and Boot with the downloaded .iso file 
    - from the Boot menu, Choose "Installation"
4. Install
    - Select "Create/Modify partitions" by pressing 'C'
    - Decline GPT
    - Create a New, Primary partition that uses the entire space
        - Choose New, Choose Primary
        - Leave it (the maximum space Size is displayed)
        - Set it Bootable (The Flags are marked as 'Boot')
        - Write (yes)
        - Quit
    - Choose the just created partition
    - format 'ext4' (for sure)
    - Install GRUB
    - Yes to install /system directory read-write
    - Run Android-x86
5. Remove the .iso image and reboot
6. it will boot with GRUB 'Android-x86 9.0-r2" is being chosen
7. It might boot without GUI, then 
    - Poweroff the VM
    - "Settings - Display - Graphics Controller" needs to be 'VBoxVGA'
8. ETC
    - Wifi will be available as VirtWifi
    - ALT-F1 goes to  the text shell, ALT-F7 comes back to GUI
    - When the screen is off due to the power saving supports, the "Power" button brings the screen back. The "ACPI Shutdown(HOST + H)" from the main menu corresponsds to the power button. 

## ADB

- Power off the machine. Create a "Host-Only Network" poweroff. Uncheck(disable) NAT.
- After the system is booted, figure out the IP (you can go to the text mode by ALT-F1). 
- Install ADB on the host: `sudo apt install adb`
- Connect/Disconnect 
    - `adb connect 192.168.56.102:5555`
    - `adb disconnect`
- Shell access: `adb shell`
- File push: 
    - `adb shell "cd /data/local && mkdir tmp"`
    - `$ adb push ***.apk /data/local/tmp`

=================================================================
# Install Frida-server to the Android-x86 VM

Referenes: 
    - https://hacklido.com/blog/338-getting-started-with-frida-setting-up-on-an-emulator

- Confirm the architecture spec. of the Android VM
    ```
    $ adb shell getprop ro.product.cpu.abi
    x86_64
    ```
### Download, install, and execute Frida

- Download a server (referring the above architecture spec.) at https://github.com/frida/frida/releases 
    ```
    $ wget https://github.com/frida/frida/releases/download/16.1.11/frida-server-16.1.11-linux-x86_64.xz
    $ mkdir frida-server
    $ mv ./frida-server-16.1.11-android-x86_64.xz ./frida-server
    $ xz -d -v ./frida-server/frida-server-16.1.11-android-x86_64.xz
    ```
- Upload the downloaded Frida-server to the VM; Set its permission bit properly
    ```
    $ adb push ./frida-server/frida-server-16.1.11-android-x86_64 /data/local/tmp/frida-server
    $ adb shell "chmod 755 /data/local/tmp/frida-server"
    ```
- Execute the Frida server
    ```
    $ adb shell
    $ su
    :/ # /data/local/tmp/frida-server &                                            
    [1] 3704
    ```

- Troubleshooting
    - `not executable: magic FD37` : decompress .xz 
    - `Unable to save SELinux policy to the kernel: Permission denied` : disable SELinux
    ```
    $ adb shell
    x86_64:/ $ su
    :/ # setenforce 0
    ```
    - `Unable to start: Error binding to address 127.0.0.1:27042: Address already in use` : kill existing processes and re-run the Frida server

=================================================================
# Frida-client

### Download, install, and connect

- `pip install frida-tools`
- `pip install frida`
- Test
    ```
     frida-ps -U
    PID  Name
    ----  ----------------------------------------------
    1695  Google App                                    
    2734  Google Play Store                             
    3546  Settings                                      
    2334  Taskbar                                       
    1099  adbd                                
    ```

