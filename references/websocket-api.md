# WebSocket API Reference

## Server Creation

WebSocket server is created from HTTP server:

**Java:**

```java
HttpServer httpServer = httpServer(12306);
WebsocketServer server = httpServer.websocket("/ws");
```

**JSON:**

```json
{
  "websocket": {
    "uri": "/ws",
    "sessions": [
      {
        "request": { "text": "foo" },
        "response": { "text": "bar" }
      }
    ]
  }
}
```

## Basic Request/Response

**Java:**

```java
server.request(by("foo")).response("bar");
```

## Connection Message

Send message when client connects:

**Java:**

```java
server.connected("connected");
```

**JSON:**

```json
{
  "websocket": {
    "uri": "/ws",
    "connected": "connected"
  }
}
```

## Ping/Pong

**Java:**

```java
server.ping("ping").pong("pong");
```

**JSON:**

```json
{
  "websocket": {
    "uri": "/ws",
    "pingpongs": [{
      "ping": "ping",
      "pong": "pong"
    }]
  }
}
```

## Broadcast

Broadcast to all connected clients:

**Java:**

```java
server.request(by("foo")).response(broadcast("bar"));
```

**JSON:**

```json
{
  "request": { "text": "foo" },
  "response": {
    "broadcast": { "content": "bar" }
  }
}
```

## Broadcast with Groups

Send to specific group of clients:

**Java:**

```java
server.request(by("subscribe"))
      .response(with("subscribed"), join(group("mygroup")));

server.request(by("broadcast"))
      .response(with("broadcasted"), broadcast("content", group("mygroup")));
```

**JSON:**

```json
[
  {
    "request": { "text": "subscribe" },
    "response": {
      "content": "subscribed",
      "group": "mygroup"
    }
  },
  {
    "request": { "text": "broadcast" },
    "response": {
      "content": "broadcasted",
      "broadcast": {
        "content": "content",
        "group": "mygroup"
      }
    }
  }
]
```
