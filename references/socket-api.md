# Socket API Reference

## Server Creation

**Java:**

```java
SocketServer server = socketServer(12306);
```

**Standalone:**

```shell
java -jar moco-runner-1.6.0-standalone.jar socket -p 12306 -c foo.json
```

Socket server only supports content-based request matching and response (no HTTP concepts like URI, headers, etc.).

## Request Matching

### Text Content

**Java:**

```java
server.request(by("foo")).response("bar");
```

**JSON:**

```json
{
  "request": { "text": "foo" },
  "response": { "text": "bar" }
}
```

### File Content

**Java:**

```java
server.request(by(file("foo.request"))).response("bar");
```

**JSON:**

```json
{
  "request": { "file": "foo.request" },
  "response": { "text": "bar" }
}
```

### XML

**Java:**

```java
server.request(xml(text("<request><id>1</id></request>"))).response("foo");
```

**JSON:**

```json
{
  "request": {
    "text": { "xml": "<request><id>1</id></request>" }
  },
  "response": { "text": "foo" }
}
```

### XPath

**Java:**

```java
server.request(eq(xpath("/request/id/text()"), "1")).response("bar");
```

**JSON:**

```json
{
  "request": {
    "xpaths": { "/request/id/text()": "1" }
  },
  "response": { "text": "bar" }
}
```

### JSON

**Java:**

```java
server.request(json(text("{\"foo\":\"bar\"}"))).response("foo");
```

**JSON shortcut:**

```json
{
  "request": { "json": { "foo": "bar" } },
  "response": { "text": "foo" }
}
```

**JSON file:**

```json
{
  "request": { "file": { "json": "body.json" } },
  "response": { "text": "foo" }
}
```

### JSONPath

**Java:**

```java
server.request(eq(jsonPath("$.book[*].price"), "1")).response("foo");
```

**JSON:**

```json
{
  "request": {
    "json_paths": { "$.book[*].price": "1" }
  },
  "response": { "text": "foo" }
}
```

### Operators

```java
match(text("regex"))
startsWith(text("prefix"))
endsWith(text("suffix"))
contain(text("substring"))
```

**JSON:**

```json
"text": { "match": "regex" }
"text": { "startsWith": "prefix" }
"text": { "endsWith": "suffix" }
"text": { "contain": "substring" }
```

## Response

### Text

**Java:**

```java
server.request(by("foo")).response("bar");
```

**JSON:**

```json
{ "response": { "text": "bar" } }
```

### File

**Java:**

```java
server.request(by("foo")).response(file("bar.response"));
```

**JSON:**

```json
{ "response": { "file": "bar.response" } }
```

### Latency

**Java:**

```java
server.response(latency(5000));
server.response(latency(1, TimeUnit.SECONDS));
```

**JSON:**

```json
{ "response": { "latency": 5000 } }
```

### Sequence

**Java:**

```java
server.request(by("foo")).response(seq("first", "second", "third"));
```

### JSON Response

**JSON:**

```json
{
  "request": { "text": "json" },
  "response": { "json": { "foo": "bar" } }
}
```

## Template

### Content

**Java:**

```java
server.request(by("template")).response(template("${req.content}"));
```

**JSON:**

```json
{
  "request": { "text": "template" },
  "response": {
    "text": { "template": "${req.content}" }
  }
}
```

### Custom Variable

**Java:**

```java
server.response(template("${foo}", "foo", "bar"));
```

**JSON:**

```json
{
  "response": {
    "text": {
      "template": {
        "with": "${foo}",
        "vars": { "foo": "bar" }
      }
    }
  }
}
```

### File Name Template

**Java:**

```java
server.response(file(template("${req.content}.txt")));
```

**JSON:**

```json
{
  "response": {
    "file": {
      "name": { "template": "${req.content}.txt" }
    }
  }
}
```
