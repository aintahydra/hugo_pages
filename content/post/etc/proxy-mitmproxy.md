---
title: "DVWA Walkthrough"
date: 2023-03-12T09:19:43+09:00
draft: false
tags: [proxy]
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

