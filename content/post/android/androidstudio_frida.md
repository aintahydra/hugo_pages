---
title: "Android studio and Frida on Apple silicon M2"
date: 2024-02-1T18:52:07+09:00
draft: false
tags: [android,androidstudio,frida,appple,silicon,M2,Mac,OSX]
---

# Install Android Studio and run an AVD on MacbookPro(M2)
- install with brew: `$ brew install --cask android-studio`
- search and run Android Studio from Spotlight
- choose, "More Actions" from the main popup, then "Virtual Device Manager"

# Install Android SDK Platform-Tools for Mac
- Download the platform tools (https://developer.android.com/tools/releases/platform-tools?hl=en)
- Unzip the downloaded file(ex, platform-tools_rXX.X.X-darwin.zip)
- Move the unzipped folder somewhere and create a symbolic link to it
    ```
    $ sudo ln -s /Users/XXX/local/platform-tools/adb /usr/local/bin/adb
    ```

# Run a virtual device
- Device
    - ((added a Pixel 6 device with Release S - API 31, ABI arm64-v8a, Android 12.0, Default System Image(Non-Google API))
    - lauch the added device

# Install Frida
- Confirm the arch spec. of the emulator: 
    ```
    adb shell getprop ro.product.cpu.abi
    arm64-v8a
    ```
- Download a Frida-server from https://github.com/frida/frida/releases
    - ex) frida-server-16.1.11-android-arm64.xz 

- Decompress the server and install it on the emulator
    ```
    $ adb shell "mkdir -p /data/local/tmp"
    $ adb push ~/Downloads/frida-server-16.1.11-android-arm64 /data/local/tmp/frida-server
    $ adb shell "chmod 755 /data/local/tmp/frida-server"
    $ 
    ```

- Launch the server
    ```
    $ adb shell
    emulator64_arm64:/ $ su
    emulator64_arm64:/ # /data/local/tmp/frida-server &
    [1] 2607
    ```

# Install Frida client

- install tools with pip
    ```
    $ pip install frida
    $ pip install frida-tools
    ```

# Test the Frida client against the Frida-server on AVD
```
$ frida-ps -U
 PID  Name
----  -----------------------------------------------------------------
1551   Phone
1002   SIM Toolkit
2276   Settings
 439      adbd
```
- If the system asks for "allowing Terminal-Initiated Display", say "Yes"
