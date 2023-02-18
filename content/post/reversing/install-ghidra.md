---
title: "Install Ghidra"
date: 2023-02-13T03:13:34+09:00
draft: false
tags: [ghidra]
---

# Install Java
1. Visit the Oracle page (https://www.oracle.com/java/technologies/downloads/ )
2. Download an installation .deb package
3. Install JDK
4. Set alternatives
```
sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/jdk-19/bin/java 1
sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/jdk-19/bin/javac 1
sudo update-alternatives --install /usr/bin/jar jar /usr/lib/jvm/jdk-19/bin/jar 1
```
5. set JAVA_HOME properly

# Install Ghidra
1. Download the ghidra: https://github.com/NationalSecurityAgency/ghidra/releases
2. Place the ghidra properly (e.g., under /usr/local)
3. Create a symbolic links at your conveniences
```
sudo ln -s DOWNLOADED_GHIDRA /usr/local/ghidra
sudo ln -s /usr/local/ghidra/ghidraRun /usr/local/bin/ghidraRun
```
4. Create a Ghidra.desktop file as follows, and place it /usr/share/applications
```
[Desktop Entry]
Exec=/usr/local/bin/ghidraRun
Encoding=UTF-8
Icon=/usr/local/ghidra/ghidra.ico
Name=Ghidra
Path=/usr/local/ghidra
Type=Application
Terminal=false
```
NOTE: If the icon does not properly show up, download a new image and use it as the icon image(a good icon image is at https://github.com/PapirusDevelopmentTeam/papirus-icon-theme/issues/1705 ).

