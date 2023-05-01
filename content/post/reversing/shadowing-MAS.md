---
title: "Shadowing MAS 1"
date: 2023-03-04T20:16:46+09:00
draft: true
---

# Shadowing Malware Analysis Series(MAS)

## Ref.
- "Malware Analysis Series (MAS) – Article 1", https://exploitreversing.com/2021/12/03/malware-analysis-series-mas-article-1/

---
# Article 1

## Before putting in your head first 

Things to be considered before analyzing malware:
- Is it **packed**?
- What's the **communication tech./APIs**(Winsock2, Wininet, COM, WSK, etc.)?
- Does it **inject codes, or hook calls**?
- Any **anti-forensic, anti-debugging, anti-disassemble** techniques used?
- Is there any **API/DLL encoding**?
- Are **strings encrypted**? Which cryptography does it use?
- Does it use any **synchronization primitives**?
- Which **persistence methods** (registry, services, tasks, kernel drives, etc.) does it use?
- Does it inject **shellcodes** into an OS process?
- Does it install any filesystem **mini-filter driver**?
- Does it install any **kernel driver, callback, or timer**?

## Gathering Information 

The first sample has the following hash(SHA256):  8ff43b6ddf6243bd5ee073f9987920fa223809f589d151d7e438fd8cc08ce292

MalwareBazaar(https://bazaar.abuse.ch/)
The author's Malwoverview(https://github.com/alexandreborges/malwoverview)