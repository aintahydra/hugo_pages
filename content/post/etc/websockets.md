## Intro
- Websocket enables a browser to be capable of synchronous communication using TCP
- Python/Java: Websocket / C#: signalR / nodejs: socket.io(long polling)
- [Traditional] Only when a browser sends a request -> A web server responses back to the request <- (asynchronous, unidirectional) 
- [Websocket] After a connection is established between a browser and a webserver, whichever part can send messages any time via the connection (connection-oriented, bidirectional) 
- included in HTML5 standards, supported by most browsers

---
# a JAVA way
￼
## A JSP servlet
```java
import javax.websocket.OnClose;
import javax.websocket.OnError;
import javax.websocket.OnMessage;
import javax.websocket.OnOpen;
import javax.websocket.server.ServerEndpoint;

// Following 4 annotations are sufficient to deal with communication events
//  @OnOpen: when a browser accesses and connected
//  @OnMessage: a message arrives
//  @OnClose: when the connection with a browser is lost
//  @OnError: when an error occurs
@ServerEndpoint("/wsTake1") // the name of service host
public class Websocket {

  @OnOpen
  public void handleOpen() {
    System.out.println("Connected!");
  }

  @OnMessage
  public String handleMessage(String msg) {
    System.out.println("Message received: " + msg);
    String reply = "[RE]: " + msg;
    System.out.println("Message sent: "+ reply);
    return reply;
  }

  @OnClose
  public void handleClose() {
    System.out.println("... Disconnected");
  }

  @OnError
  public void handleError(Throwable t) {
    t.printStackTrace();
  }
}
```

## A JSP page to access the servlet
```html
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
  <head><title>Take #1</title></head>
  <body>
    <form>
      <!-- a text field for sending a msg -->
      <input id="txtMsg" type="text">
      <!-- the SEND button -->
      <input onclick="sendMessage()" value="SEND" type="button">
      <!-- the DISCONNECT button -->
      <input onclick="disconnect()" value="DISCONNECT" type="button">
    </form>
    <br />
    <!--  a text area that lists sent and received messages -->
    <textarea id="taMessages" rows="30" cols="80"></textarea>
    <script type="text/javascript">

      // Construct a WebSocket
      // - the name of service host: wsTake1
      // the websocket will start the connection, so @OnOpen will be called
      var webSocket = new WebSocket("ws://localhost:8080/wsProject/wsTake1");

      var msgTextArea = document.getElementById("taMessages");

      // Client listeners 
      // when connected to a websocket server
      webSocket.onopen = function(message) {
        messageTextArea.value += "Connected\n";
      };
      // when the connction to the websocket server is lost
      webSocket.onclose = function(message) {
        messageTextArea.value += "Disconnected\\n";
      };
      // when an error occurs
      webSocket.onerror = function(message) {
        messageTextArea.value += "Error!\n";
      };
      // when a message arrives
      webSocket.onmessage = function(message) {
        messageTextArea.value += "Recieve From Server => "+message.data+"\n";
      };

    // when the SEND button is pressed
    function sendMessage() {
      var message = document.getElementById("txtMsg");
      messageTextArea.value += "The message sent: " + message.value + "\n";
      webSocket.send(message.value);
      message.value = "";
    }
    // when the DISCONNECT button is pressed
    function disconnect() {
      webSocket.close();
    }
  </script>
</body>
</html>
```

---
## Tomcat Docker Container

- download and run a tomcat docker container:
    ```
    docker run --name tomcat -p 8080:8080 tomcat:latest
    (or)
    docker run -d -it --name tomcat -p 8080:8080 tomcat:latest
    ```
- access to the container
    ```
    docker exec -it tomcat /bin/bash
    ```
- copy files to the container
    ```
    docker cp ROOT.war tomcat:/usr/local/tomcat/webapps
    ```


---
# a Python way

## Prep
- download a library `pip install websockets`

## A Server
```python
import asyncio;
import websockets;
 
## create a native coroutine that will be called when websocket peer is connected
async def accept(websocket, path):
  print("Connected");
  ## repeat: waiting - handling - replying
  while True:
    # waiting until a message arrives
    msg = await websocket.recv();
    # handling the message
    print("Message received : " + msg);
    # replying back to the message
    await websocket.send("[RE]: " + msg);
 
# Create a websocket server at localhost:7777 
ws_server = websockets.serve(accept, "localhost", 7777);
asyncio.get_event_loop().run_until_complete(ws_server);
asyncio.get_event_loop().run_forever();
```
---
---
---
---

## A Webpage
```html
<html>
<head>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
  <title>Insert title here</title>
  </head>
<body>
  <form>
    <input id="textMessage" type="text">
    <input onclick="sendMessage()" value="Send" type="button">
    <input onclick="disconnect()" value="Disconnect" type="button">
  </form>
  <br />
  <textarea id="messageTextArea" rows="10" cols="50"></textarea>
  <script type="text/javascript">
    var webSocket = new WebSocket("ws://localhost:9998");
    var messageTextArea = document.getElementById("messageTextArea");
    webSocket.onopen = function(message){
      messageTextArea.value += "Server connect...\n";
    };
    webSocket.onclose = function(message){
      messageTextArea.value += "Server Disconnect...\n";
    };
    webSocket.onerror = function(message){
      messageTextArea.value += "error...\n";
    };
    webSocket.onmessage = function(message){
      messageTextArea.value += "Recieve From Server => "+message.data+"\n";
    };
    function sendMessage(){
      var message = document.getElementById("textMessage");
      messageTextArea.value += "Send to Server => "+message.value+"\n";
      webSocket.send(message.value);
      message.value = "";
    }
    function disconnect(){
      webSocket.close();
    }
  </script>	
</body>	
</html>
``` 

## Run ws_server using an Nginx Docker container
1. pull nginx: `docker pull nginx`
2. create a service directory and put the above script(ws_server.py) into the created directory: `mkdir a_ws_service; cp SOMEWHERE/ws_service.py a_ws_service`
3. run nginx container `docker run --name ws_nginx -v ./a_ws_service:/usr/share/nginx/html -d -p 80:80 nginx`

## An index.html file
```python
<!DOCTYPE html>
<html>
<head>
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
  <title>Insert title here</title>
  </head>
<body>
  <form>
    <input id="textMessage" type="text">
    <input onclick="sendMessage()" value="Send" type="button">
    <input onclick="disconnect()" value="Disconnect" type="button">
  </form>
  <br />
  <textarea id="messageTextArea" rows="10" cols="50"></textarea>
  <script type="text/javascript">
    var webSocket = new WebSocket("ws://localhost:9998");
    var messageTextArea = document.getElementById("messageTextArea");
    webSocket.onopen = function(message){
      messageTextArea.value += "Server connect...\n";
    };
    webSocket.onclose = function(message){
      messageTextArea.value += "Server Disconnect...\n";
    };
    webSocket.onerror = function(message){
      messageTextArea.value += "error...\n";
    };
    webSocket.onmessage = function(message){
      messageTextArea.value += "Recieve From Server => "+message.data+"\n";
    };
    function sendMessage(){
      var message = document.getElementById("textMessage");
      messageTextArea.value += "Send to Server => "+message.value+"\n";
      webSocket.send(message.value);
      message.value = "";
    }
    function disconnect(){
      webSocket.close();
    }
  </script>	
</body>	
</html>
```