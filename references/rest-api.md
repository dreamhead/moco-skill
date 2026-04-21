# REST API Reference

## Basic Usage

**Java:**

```java
RestServer server = restServer(12306);
server.resource("targets",
    get("1").response(json(resource))
);
// GET /targets/1 returns the resource
```

**JSON:**

```json
[{
  "resource": {
    "name": "targets",
    "get": [{
      "id": "1",
      "response": {
        "json": { "code": 1, "message": "foo" }
      }
    }]
  }
}]
```

## Composite Settings

Multiple REST settings on one resource:

```java
server.resource("targets",
    get("1").response(json(resource1)),
    get("2").response(json(resource2)),
    post().response(status(201), header("Location", "/targets/123"))
);
```

## ID Matching

Specific ID:

```java
get("1").response(json(resource))   // matches /targets/1
```

Any ID:

```java
get(anyId()).response(json(resource))  // matches /targets/1, /targets/2, etc.
```

JSON wildcard:

```json
"id": "*"
```

## Methods

### GET

With ID (single resource):

```java
server.resource("targets",
    get("1").response(json(resource))
);
// GET /targets/1
```

Without ID (all resources):

```java
server.resource("targets",
    get().response(json(Arrays.asList(resource1, resource2)))
);
// GET /targets
```

### POST

```java
server.resource("targets",
    post().response(status(201), header("Location", "/targets/123"))
);
// POST /targets
```

### PUT

```java
server.resource("targets",
    put("1").response(status(200))
);
// PUT /targets/1
```

### DELETE

```java
server.resource("targets",
    delete("1").response(status(200))
);
// DELETE /targets/1
```

### HEAD

```java
server.resource("targets",
    head("1").response(header("ETag", "v1"))
);
// HEAD /targets/1
```

### PATCH

```java
server.resource("targets",
    patch("1").response(status(200))
);
// PATCH /targets/1
```

## Request and Response

Add request matching to REST settings:

```java
server.resource("targets",
    get("1")
        .request(eq(header("Content-Type"), "application/json"))
        .response(json(resource))
);
```

## Sub-resource

**Java:**

```java
server.resource("targets",
    id("1").name("subs").settings(
        get("1").response(json(subResource))
    )
);
// GET /targets/1/subs/1
```

**JSON:**

```json
{
  "resource": {
    "name": "targets",
    "resource": [{
      "id": "1",
      "name": "subs",
      "get": [{
        "id": "1",
        "response": {
          "json": { "code": 3, "message": "sub" }
        }
      }]
    }]
  }
}
```
