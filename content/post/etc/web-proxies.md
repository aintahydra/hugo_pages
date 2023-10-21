---
title: "Web Proxies"
date: 2023-03-12T09:19:43+09:00
draft: false
tags: [proxy,web_proxy,burp,mitmproxy,zed,proxify]
---

===========
### Tip: common to burp suite, mitmproxy, and proxify

When a DVWM instance is running on a local machine and accessed by 127.0.0.1:PORT,
local proxy cannot intercept HTTP request correctly.
After a series of trial and error, the solution found was accessing the instance using a name, like:
- add a line to the hosts file: 127.0.0.1 DVWA
- visit DVWA:PORT instead of 127.0.0.1:PORT

=================
# mitmproxy

### install 

- KALI: mitmproxy is already installed on Kali.
- OSX: `brew install mitmproxy`

Ref: 
- https://mitmproxy.org/
- https://docs.mitmproxy.org/stable/mitmproxytutorial-userinterface/

=================
### how-to-use

Type `$ mitmproxy`, that's all. 

Test with curl: `$ curl --proxy http://127.0.0.1:8080 "http://wttr.in/salzburg"`
(In case of Firefox, add HTTP Proxy:127.0.0.1, Port:8080.)

Keys
- "UP" and "Down" : move the cursor ">>"
- "ENTER" : view the details of a flow
- "LEFT" and "RIGHT" : switch between flow details panes
- "q" : exit the current view
- "?" : list shorcuts
- ":" : open the command prompt

Commands
- ex) `console.view.flow @focus`
    - `console.view.flow` : opens the details view for a flow 
    - `@focus` : define the target flow

=================
### Intercept requests

- the shortcut, `i` or the command `set intercept`

Flow filter expression
- the first argument to `set intercept`
- `~u <regex>` : intercept specific URLs (`<regex>`)
- `~q` : intercept Requests only
- `&` : combine flow filters
- ex) `set intercept '~u /wttr.in & ~q'`
- test in the other terminal,
    - `curl --proxy http://127.0.0.1:8080 "www.google.com"`
    - and then `curl --proxy http://127.0.0.1:8080 "http://wttr.in/lagos"`
- `a` : resume the intercepted flow
- `X` : discard the flow 

=================
### Modify requests

- `e` : edit the intercepted flow
    - "arrow key" to select `path`, then ENTER 
    - modify "salzburg" to "munc"
    - press ESC to confirm the change
    - `q` to go back
    - `a` to resume the intercepted flow

=================
### Replay requests (Client-side replay)

- Client-side replay : replays previous client requests (sends the same request to the server again)
- Server-side replay : replays server responses for requests that match an earlier recorded request (https://docs.mitmproxy.org/stable/overview-features/#server-side-replay)

- generate a request to replay (by `curl --proxy http://127.0.0.1:8080 "http://wttr.in/salzburg?0`)
- after a request is generated, put the ">>" cursor on the request, press `r`
- (the flow can be edited as well)
- after the request is replayed, no new flow is recorded, BUT the old one is UPDATED

=================
# Other proxies

-----------------
## Zed Attack Proxy (ZAP)

REF:
    - https://www.kali.org/tools/zaproxy/

### Install
- apt install zaproxy 


-----------------
## <strike> Proxify </strike>
REF:
- install: 
    - https://www.kitploit.com/2020/12/proxify-swiss-army-knife-proxy-tool-for.html
    - <strike> https://www.kali.org/tools/proxify/ </strike>
- usage: https://gitlab.com/kalilinux/packages/proxify

<strike>
### install
- apt install proxify
</strike>

### install binary manually 
- download a binary from https://github.com/projectdiscovery/proxify/releases/
- create a directory for proxify binaries: `mkdir -p ~/local/proxify`
- move the downloaded file to the directory and unzip: `mv proxify_0.0.12_linux_amd64.zip ~/local/proxify; cd ~/local/proxify; unzip proxify*.zip`
- run: "~/local/proxify/proxify"

### lab
- captured packets to dockered-dvwa
```
$ cd ~/local/proxify
$ ./proxify -http-addr "127.0.0.1:80" 

                       _ ___    
   ___  _______ __ __ (_) _/_ __
  / _ \/ __/ _ \\ \ // / _/ // /
 / .__/_/  \___/_\_\/_/_/ \_, / 
/_/                      /___/

                projectdiscovery.io

[INF] Current proxify version v0.0.12 (latest)
[INF] HTTP Proxy Listening on 127.0.0.1:80
[INF] Socks5 Proxy Listening on 127.0.0.1:10080
[INF] Saving proxify traffic to logs
```

- on Firefox, (with network onnections - proxy is set to 127.0.0.1:8888), accessed: 127.0.0.1:80

- then, 
```
$ cd ~/local/proxify/logs

$ tail -f 127.0.0.1-ab2c790481173eab.txt 
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0

token=8b479aefbd90795395b3e7089ae0dc09&phrase=ChangeMe&send=Submit
HTTP/1.1 502 Bad Gateway
Content-Length: 0
Warning: 199 "martian" "[:RUNTIME] ztls fallback failed <- dial tcp 127.0.0.1:443: connect: connection refused" "Wed, 11 Oct 2023 03:50:38 GMT"
```