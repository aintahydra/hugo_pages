---
title: "Cheatsheet"
date: 2023-08-26T21:39:49+09:00
draft: false 
---

# A forensic cheet sheet

### get hash from a file
    - Windows `PS> get-filehash -algorithm sha1 <filename>`
    - Linux `md5sum|sha256sum <filename>`
    - Malware hashes
        - https://virustotal.com/
        - https://hash.cymru.com/

### Search
- search a file name: `find <DIR> -name <filename>`
- search a string: `grep -r <search_string> <targetFILES> ([--include '*.conf']`
    - or `find . -type f -print | xargs grep -i "search_string" /dev/null`
    - or `find . -name '*.ext' | xargs grep -r --color=auto -n 2>dev/null 'search_string'` (search and color them)
- search a string from a binary: `$ strings <binary_file> | grep <search_string>`

- Carving
    - tools: axiom, xways
    - the carving does not recover the file/dir structure

- xfs_undelete (https://github.com/ianka/xfs_undelete )
    - `xfs_undelete -o /mnt/external_harddisk /dev/sda3`: This stores the recovered files from /dev/sda3 in the directory /mnt/external_harddisk.
    - `xfs_undelete -t 2023-01-16 /dev/sda3`: This ignores files deleted before January 16th, 2023.

- covnert an encase image(.E01) to a raw (.001) file: ftk-imager

- mount a raw file (IMAGE.001) (https://blog.tinned-software.net/mount-raw-image-of-entire-disc/)
    - create a loop device: `losetup --partscan --find --show IMAGE.001`
    - find the loop device: `$ losetup -l`
    - mount the loop device: `mount -t FSYS_TYPE [-o offset=512] <loop_device> /media/MOUNT_TARGET_DIR`

## Analyze specific files (here in the example, *.html fiels)

1. get HTML-file names: `$ find . -name "*.html" > htmlfiles`
2. wrote a script
    ```
    #!/usr/bin/python3

    import sys
    import subprocess

    if (len(sys.argv) != 2):
            print("where is the input file, huh?");
            sys.exit()

    f = open(sys.argv[1])
    line = f.readline()
    while line:
        p = subprocess.run(['/usr/bin/file', line.rstrip('\n')])
        line = f.readline();
    f.close()
    ```
3. run the script 
    ```
    $ python3 ./__checkfiles.py htmlfiles | grep encrypted
    $ python3 ./__checkfiles.py htmlfiles | grep -v "HTML document" | grep -v "ASCII"
    ```
4. check the entropy of files : `$ ent <file_name> | awk '/Entropy =/ {print $3}'`

## Run Anti-virus 

1. install Clam-AV
2. Modify the above script (switch `/usr/bin/file` to `/usr/bin/clamscan`)
3. Run the script `$ python3 __checkfiles.py htmlfiles > __HTML_SCAN_RES.txt | grep "Infected files: 1"`

## CLAM-AV

- Clam-AV Update
```
$ service clamav-freshclam stop 
$ sudo freshclam
$ service clamav-freshclam start
```

- Clam-AV Scan
'''
$ clamscan -i -r .
'''



