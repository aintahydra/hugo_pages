---
title: "Testing Websockets 1"
date: 2023-03-04T20:16:46+09:00
draft: true
---

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

// Following 4 annotations are sufficient to deal with communication events: @OnOpen, @OnMessage, @OnClose, and @OnError.
@ServerEndpoint("/wsAJavaWay") // the name of service host
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
  <head><title>Testing websockets - A java way</title></head>
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
    <textarea id="taMsgs" rows="30" cols="80"></textarea>
    <script type="text/javascript">

      // Construct a WebSocket
      // - the name of service host: wsTake1
      // the websocket will start the connection, so @OnOpen will be called
      var webSocket = new WebSocket("ws://localhost:7777");

      var msgTextArea = document.getElementById("taMsgs");

      // Client listeners 
      // when connected to a websocket server
      webSocket.onopen = function(message) {
        taMsgs.value += "Connected\n";
      };
      // when the connction to the websocket server is lost
      webSocket.onclose = function(message) {
        taMsgs.value += "Disconnected\\n";
      };
      // when an error occurs
      webSocket.onerror = function(message) {
        taMsgs.value += "Error!\n";
      };
      // when a message arrives
      webSocket.onmessage = function(message) {
        taMsgs.value += "Recieve From Server => " + message.data + "\n";
      }; 

    // when the SEND button is pressed
    function sendMessage() {
      var message = document.getElementById("txtMsg");
      taMsgs.value += "The message sent: " + message.value + "\n";
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

