---
name: moco
description: Use when working with Moco HTTP stub server - writing test mocks, configuring JSON stubs, using Java/REST/WebSocket/SSE/Socket APIs, or running standalone Moco servers
---

# Moco

Lightweight HTTP stub framework for testing and integration. Creates mock HTTP/HTTPS, WebSocket, SSE, and Socket servers with simple configuration.

## Quick Start

**Java API:**

```java
import static com.github.dreamhead.moco.Moco.*;
import static com.github.dreamhead.moco.Runner.*;

HttpServer server = httpServer(12306);
server.request(by(uri("/foo"))).response("bar");

running(server, () -> {
    // test against http://localhost:12306/foo
});
```

**JSON Config:**

```json
[
  {
    "request": { "uri": "/foo" },
    "response": { "text": "bar" }
  }
]
```

```shell
java -jar moco-runner-1.6.0-standalone.jar http -p 12306 -c foo.json
```

## Core Concepts

| Concept | Java API | JSON |
|---------|----------|------|
| **Match URI** | `by(uri("/foo"))` | `"uri": "/foo"` |
| **Match method** | `server.get(by(uri("/foo")))` | `"method": "get"` |
| **Match text** | `by("foo")` | `"text": "foo"` |
| **Match JSON** | `json("{\"foo\":\"bar\"}")` | `"json": {"foo": "bar"}` |
| **Match header** | `eq(header("foo"), "bar")` | `"headers": {"foo": "bar"}` |
| **Match query** | `eq(query("p"), "v")` | `"queries": {"p": "v"}` |
| **Respond text** | `.response("bar")` | `"text": "bar"` |
| **Respond JSON** | `.response(json(obj))` | `"json": {"foo": "bar"}` |
| **Respond status** | `.response(status(201))` | `"status": 201` |
| **Set header** | `header("Content-Type", "app/json")` | `"headers": {"Content-Type": "app/json"}` |
| **Template** | `template("${req.content}")` | `"template": "${req.content}"` |
| **Regex match** | `match(uri("/\\w+/foo"))` | `"uri": {"match": "/\\w+/foo"}` |
| **Proxy** | `proxy("http://target")` | `"proxy": "http://target"` |
| **Sequence** | `seq("a", "b", "c")` | `"seq": [{"text":"a"}, ...]` |
| **Latency** | `latency(1, TimeUnit.SECONDS)` | `"latency": {"duration":1, "unit":"second"}` |

**Operators:** `match` (regex), `startsWith`, `endsWith`, `contain`, `exist`, `path` (path variables)

**Template variables:** `${req.content}`, `${req.headers['x']}`, `${req.queries['q']}`, `${req.json.foo}`, `${req.xml.bar}`, `${req.cookies['c']}`, `${req.path.id}`, `${req.client.address}`, `${now('yyyy-MM-dd')}`, `${random()}`

**Verification:**

```java
RequestHit hit = requestHit();
HttpServer server = httpServer(12306, hit);
hit.verify(by(uri("/foo")), times(1));
hit.verify(unexpected(), never());
```

**Composing matchers and responses:**

Multiple request matchers MUST be combined with `and()` — never pass multiple matchers as separate arguments:

```java
// Correct: combine with and()
server.post(and(by(uri("/api/login")), by(json(text(...)))))

// Wrong: do NOT pass two matchers separately
server.post(by(uri("/api/login")), by(json(text(...))))
```

Multiple response handlers use `with()` or comma-separated args to `.response()`:

```java
.response(json(obj), status(200), cookie("token", "abc123))
// or
.response(with(json(obj)), header("Content-Type", "application/json"))
```

## Reference Files

| File | When to Read |
|------|-------------|
| [java-api.md](references/java-api.md) | Writing Java test code with Moco |
| [json-config.md](references/json-config.md) | Writing JSON config files or global settings |
| [rest-api.md](references/rest-api.md) | Building RESTful stub services |
| [websocket-api.md](references/websocket-api.md) | Setting up WebSocket mock servers |
| [sse-api.md](references/sse-api.md) | Mocking SSE streams (e.g. LLM token streaming) |
| [socket-api.md](references/socket-api.md) | Raw TCP socket stubs |
| [cli.md](references/cli.md) | Running standalone Moco server |
| [junit.md](references/junit.md) | JUnit 5 integration with annotations |

## Common Patterns

**Match POST with URI + JSON body, respond with JSON + status + cookie:**

```java
server.post(and(by(uri("/api/login")), by(json(text("{\"user\":\"admin\"}")))))
     .response(json(new MyResponse("ok")), status(200), cookie("token", "abc123"));
```

**Dynamic response with template:**

```java
server.request(path(uri("/user/{id}")))
     .response(template("${req.path.id}"));
```

**Proxy with failover:**

```java
server.request(by(uri("/api")))
     .response(proxy("http://real-server", failover("backup.json")));
```
