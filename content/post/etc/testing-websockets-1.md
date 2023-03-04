---
title: "Testing Websockets 1"
date: 2023-03-04T20:16:46+09:00
draft: false
tags: [websocket,socket]
---

## Intro
- Websocket enables a browser to be capable of synchronous communication using TCP
- Python/Java: Websocket / C#: signalR / nodejs: socket.io(long polling)
- [Traditional] Only when a browser sends a request -> A web server responses back to the request <- (asynchronous, unidirectional) 
- [Websocket] After a connection is established between a browser and a webserver, whichever part can send messages any time via the connection (connection-oriented, bidirectional) 
- included in HTML5 standards, supported by most browsers

# A simple websocket echo: a python server and an HTML client

## A Server in Python

Prep.: 
- download a library `pip install websockets`

```python
import asyncio;
import websockets;
 
## create a native coroutine that will be called when websocket peer is connected
async def accept(websocket, path):
  print("Connected!");
  ## repeat: waiting - handling messages - replying
  while True:
    # waiting until a message arrives
    msg = await websocket.recv();
    # handling the message
    print("Message received : " + msg);
    # replying back to the message
    await websocket.send("[RE]: " + msg);
    print("Just replied");
 
# Create a websocket server at localhost:7777 
ws_server = websockets.serve(accept, "localhost", 7777);
# get an event loop
loop = asyncio.get_even t_loop()
# wait until ws_server finishes
loop.run_until_complete(ws_server);
# run forever
loop.run_forever();
```

## An HTML client

Prep: 
- create a directory, called `a_ws_client` that will be mounted to an NGINX docker instance. 
- then create a websocket client page, `a_ws_client.html` file which has the following codes:
```html
<html><head>
  <title>Testing websockets 1</title>
</head><body>

  <form>
    <input id="txtMsg" type="text">
    <input onclick="sendmsg()" value="SEND" type="button">
    <input onclick="disconnect()" value="DISCONNECT" type="button">
  </form>
  <br />
  <textarea id="taMsgs" rows="30" cols="50"></textarea>

  <script type="text/javascript">
    var webSocket = new WebSocket("ws://localhost:7777");
    var messageTextArea = document.getElementById("taMsgs");

    //event listeners
    webSocket.onopen = function(message){
      messageTextArea.value += "Connected to the websocket server...\n";
    };

    webSocket.onclose = function(message){
      messageTextArea.value += "Server disconnected...\n";
    };

    webSocket.onerror = function(message){
      messageTextArea.value += "Error...\n";
    };

    webSocket.onmessage = function(message){
      messageTextArea.value += ("Recieved from the server => "+message.data+"\n");
    };

    function sendmsg(){
      var message = document.getElementById("txtMsg");
      messageTextArea.value += "Sending to the server => "+message.value+"\n";
      webSocket.send(message.value);
      message.value = "";
    }
    function disconnect(){
      webSocket.close();
    }
  </script>	
</body></html>
```

## How to run 

1. run a webserver (a NGINX docker container)
  * in the following example,  `./a_ws_client` will be mounted as `/usr/share/nginx/html`, the basic web document directory that NGINX uses
  ```
  $ docker run --name ws_nginx -v ./a_ws_client:/usr/share/nginx/html -d -p 80:80 -p 7777:7777 nginx
  ```

2. run a websocket server
  ```
  $ /python3 ws_server.py &
  ```

3. run the client  browser
  ```
  $ firefox a_ws_client/a_ws_client.html &
  ```

