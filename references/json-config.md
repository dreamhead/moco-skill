# JSON Configuration Reference

## Structure

JSON config is an array of request/response pairs:

```json
[
  {
    "description": "optional comment (ignored at runtime)",
    "request": { "uri": "/foo" },
    "response": { "text": "bar" }
  }
]
```

Single pair (no array) also works:

```json
{
  "request": { "uri": "/foo" },
  "response": { "text": "bar" }
}
```

## Request Matching

### URI

```json
"uri": "/foo"
```

With operators:

```json
"uri": { "match": "/\\w+/foo" }
"uri": { "startsWith": "/foo" }
"uri": { "endsWith": "/foo" }
"uri": { "contain": "foo" }
"uri": { "path": "/users/{id}/posts/{postId}" }
```

### Method

```json
"method": "get"
```

Values: `get`, `post`, `put`, `delete`, `head`, `patch`, or any HTTP method.

### Headers

```json
"headers": {
  "Content-Type": "application/json",
  "Authorization": "Bearer token"
}
```

Header with operator:

```json
"headers": {
  "X-Foo": { "exist": "true" },
  "X-Bar": { "match": "prefix.*" }
}
```

### Query Parameters

```json
"queries": {
  "param": "value",
  "page": "1"
}
```

### Forms

```json
"forms": {
  "username": "admin",
  "password": "secret"
}
```

### Cookies

```json
"cookies": {
  "sessionId": "abc123"
}
```

### Text Content

```json
"text": "exact content"
```

```json
"text": { "match": "regex.*pattern" }
"text": { "startsWith": "prefix" }
"text": { "endsWith": "suffix" }
"text": { "contain": "substring" }
```

### JSON

```json
"json": {
  "foo": "bar",
  "count": 1
}
```

JSON text (when special characters are needed):

```json
"text": {
  "json": "{\"foo\":\"bar\"}"
}
```

JSON from file:

```json
"file": {
  "json": "body.json"
}
```

JSON struct matching (structure only):

```json
"struct": {
  "json": { "foo": 1 }
}
```

### XML

```json
"text": {
  "xml": "<root><id>1</id></root>"
}
```

```json
"file": {
  "xml": "body.xml"
}
```

XML struct matching:

```json
"struct": {
  "xml": "<foo>1</foo>"
}
```

### XPath

```json
"xpaths": {
  "/root/id/text()": "1"
}
```

### JSONPath

```json
"json_paths": {
  "$.foo.bar": "value",
  "$.items[0].price": "10"
}
```

## Response Definition

### Text

```json
"text": "response content"
```

### File

```json
"file": "response.txt"
```

With charset:

```json
"file": {
  "name": "response.txt",
  "charset": "GBK"
}
```

Path resource with charset:

```json
"path_resource": {
  "name": "response.txt",
  "charset": "GBK"
}
```

### Status Code

```json
"status": 200
```

### Headers

```json
"headers": {
  "Content-Type": "application/json",
  "X-Custom": "value"
}
```

### Cookies

Simple:

```json
"cookies": {
  "name": "value"
}
```

With attributes:

```json
"cookies": {
  "name": {
    "value": "val",
    "path": "/",
    "domain": "example.com",
    "secure": "true",
    "httpOnly": "true",
    "maxAge": { "duration": 1, "unit": "hour" },
    "sameSite": "Lax"
  }
}
```

SameSite values: `Strict`, `Lax`, `None`.

### JSON Response

```json
"json": {
  "foo": "bar",
  "count": 1
}
```

### Redirect

```json
"redirectTo": "http://example.com"
```

### Proxy

Single URL:

```json
"proxy": "http://target-server"
```

With failover:

```json
"proxy": {
  "url": "http://target",
  "failover": "backup.json"
}
```

With playback:

```json
"proxy": {
  "url": "http://target",
  "playback": "cache.json"
}
```

Custom failover status:

```json
"proxy": {
  "url": "http://target",
  "failover": {
    "file": "backup.json",
    "status": [404, 500]
  }
}
```

Batch URLs:

```json
"proxy": {
  "from": "/api",
  "to": "http://target/api"
}
```

Top-level proxy shortcut:

```json
"proxy": {
  "from": "/api",
  "to": "http://target/api"
}
```

### CORS

Allow all:

```json
"cors": true
```

Customized:

```json
"cors": {
  "allowOrigin": "*",
  "allowMethods": ["GET", "POST"],
  "allowHeaders": ["X-Custom"],
  "maxAge": { "duration": 3600, "unit": "second" },
  "allowCredentials": true,
  "exposeHeaders": ["X-Response"]
}
```

Can also use HTTP header names directly: `Access-Control-Allow-Origin`, etc.

### Attachment

```json
"attachment": {
  "filename": "download.txt",
  "file": "content.txt"
}
```

### Latency

```json
"latency": { "duration": 1, "unit": "second" }
```

### Sequence

```json
"seq": [
  { "text": "first" },
  { "text": "second" },
  { "text": "third" }
]
```

### Cycle

```json
"cycle": [
  { "text": "first" },
  { "text": "second" }
]
```

### Mount

```json
"mount": {
  "dir": "static",
  "uri": "/files",
  "includes": ["*.txt", "*.html"],
  "excludes": ["*.log"],
  "headers": {
    "Content-Type": "text/plain"
  }
}
```

## Template

### Basic

```json
"text": {
  "template": "${req.content}"
}
```

### Request Variables

| Variable | Description |
|----------|-------------|
| `${req.version}` | HTTP version |
| `${req.method}` | HTTP method |
| `${req.content}` | Request body |
| `${req.headers['X-Foo']}` | Header value |
| `${req.queries['q']}` | Query parameter |
| `${req.forms['field']}` | Form field |
| `${req.cookies['name']}` | Cookie value |
| `${req.json.foo}` | JSON field (request must be JSON) |
| `${req.xml.bar}` | XML element (request must be XML) |
| `${req.path.id}` | Path variable (with `path` operator) |
| `${req.client.address}` | Client IP |
| `${req.client.port}` | Client port |

### Custom Variables

```json
"text": {
  "template": {
    "with": "${greeting}",
    "vars": {
      "greeting": "hello"
    }
  }
}
```

With extractors:

```json
"text": {
  "template": {
    "with": "${price}",
    "vars": {
      "price": { "json_path": "$.book[0].price" }
    }
  }
}
```

### Template Functions

```json
"template": "${now('yyyy-MM-dd')}"
"template": "${random()}"
"template": "${random(100)}"
"template": "${random(10, 20)}"
"template": "${random('###.###')}"
```

### Template in Redirect

```json
"redirectTo": {
  "template": {
    "with": "${url}",
    "vars": { "url": "http://target" }
  }
}
```

### Template in Proxy URL

```json
"proxy": {
  "url": {
    "template": "http://target/${req.queries['id']}"
  }
}
```

### Template in File Name

```json
"file": {
  "name": {
    "template": "${req.headers['filename']}.txt"
  }
}
```

## Record and Replay

```json
[
  {
    "request": { "uri": "/record" },
    "response": {
      "record": {
        "group": "mygroup",
        "identifier": { "template": "${req.queries['type']}" },
        "tape": "/path/to/tape"
      }
    }
  },
  {
    "request": { "uri": "/replay" },
    "response": {
      "record": {
        "group": "mygroup",
        "modifier": "${req.queries['type']}"
      }
    }
  }
]
```

Modifier with response config:

```json
"modifier": {
  "text": { "template": "${req.content}" },
  "header": { "X-REPLAY": { "template": "${req.queries['type']}" } }
}
```

## Events

### GET on Complete

```json
{
  "request": { "uri": "/event" },
  "response": { "text": "ok" },
  "on": {
    "complete": {
      "get": { "url": "http://callback" }
    }
  }
}
```

### POST on Complete

```json
"on": {
  "complete": {
    "post": {
      "url": "http://callback",
      "content": "payload",
      "headers": { "X-Foo": "bar" }
    }
  }
}
```

POST with JSON:

```json
"on": {
  "complete": {
    "post": {
      "url": "http://callback",
      "json": { "foo": "bar" }
    }
  }
}
```

### Async

```json
"on": {
  "complete": {
    "async": "true",
    "latency": 1000,
    "post": { "url": "http://callback", "content": "payload" }
  }
}
```
