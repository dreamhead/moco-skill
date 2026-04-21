# SSE API Reference

Since 1.6.0. Supports Server-Sent Events streaming, including LLM-style token output.

## Imports

```java
import static com.github.dreamhead.moco.MocoSse.event;
import static com.github.dreamhead.moco.MocoSse.data;
import static com.github.dreamhead.moco.MocoSse.sse;
```

## Basic Usage

**Java:**

```java
server.request(by(uri("/events")))
      .response(sse(event("message", "Hello")));
```

**JSON:**

```json
{
  "request": { "uri": "/events" },
  "response": {
    "sse": [
      { "event": "message", "data": "Hello" }
    ]
  }
}
```

## Multiple Events

**Java:**

```java
.response(sse(
    event("message", "Hello"),
    event("message", " World")
));
```

**JSON:**

```json
"sse": [
  { "event": "message", "data": "Hello" },
  { "event": "message", "data": " World" }
]
```

## Event Types

Named events:

```java
event("message", "Hello World")
```

Anonymous data events (no event name):

```java
data("Hello World")
```

## Event Modifiers

Chain modifiers on events:

```java
event("message", "Hello").id("1")                    // Set event ID
event("message", "Hello").retry(3000)                 // Set reconnection time (ms)
event("message", "Hello").delay(50)                   // Set send delay (ms), must be > 0
event("message", "Hello").delay(1, TimeUnit.SECONDS)  // Delay with time unit
event("message", "Hello").id("1").retry(3000).delay(50)  // Combine
```

**JSON:**

```json
{ "event": "message", "data": "Hello", "id": "1", "retry": 3000, "delay": 50 }
```

Delay with time unit:

```json
{ "event": "message", "data": "Hello", "delay": { "duration": 1, "unit": "SECOND" } }
```

## Default Delay

Set delay for all events without their own delay:

**Java:**

```java
sse(event("message", "Hello"), event("message", " World")).delay(50)
sse(event("message", "Hello"), event("message", " World")).delay(1, TimeUnit.SECONDS)
```

**JSON:**

```json
"sse": {
  "delay": 50,
  "events": [
    { "event": "message", "data": "Hello" },
    { "event": "message", "data": " World" }
  ]
}
```

With time unit:

```json
"sse": {
  "delay": { "duration": 1, "unit": "SECOND" },
  "events": [
    { "event": "message", "data": "Hello" },
    { "event": "message", "data": " World" }
  ]
}
```

Supported units: `NANOSECOND`, `MICROSECOND`, `MILLISECOND`, `SECOND`, `MINUTE`, `HOUR`, `DAY`.

## File-Based Events

**Java:**

```java
.response(sse(file("events.txt")));
```

**JSON:**

```json
"sse": { "file": "events.txt" }
```

### SSE Event File Format

```
event: message
data: first event

event: message
data: second event
delay: 50

id: 001
event: update
data: third event
```

Fields: `id`, `event`, `data` (can repeat for multi-line), `retry`. Events separated by blank lines.

## Streaming Behavior

- Each event sent as separate HTTP chunk
- Events with `delay()` sent with specified interval
- Connection closed after all events sent
- Auto headers: `Content-Type: text/event-stream`, `Cache-Control: no-cache`, `Connection: keep-alive`, `X-Accel-Buffering: no`

## LLM Token Streaming Example

**Java:**

```java
server.request(by(uri("/chat")))
      .response(sse(
          event("message", "Hello").delay(50),
          event("message", " World").delay(50),
          event("message", "!").delay(50)
      ));
```

**JSON:**

```json
{
  "request": { "uri": "/chat" },
  "response": {
    "sse": [
      { "event": "message", "data": "Hello", "delay": 50 },
      { "event": "message", "data": " World", "delay": 50 },
      { "event": "message", "data": "!", "delay": 50 }
    ]
  }
}
```
