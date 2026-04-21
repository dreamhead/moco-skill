# Java API Reference

## Dependencies

```xml
<dependency>
  <groupId>com.github.dreamhead</groupId>
  <artifactId>moco-core</artifactId>
  <version>1.6.0</version>
</dependency>
```

```groovy
testImplementation "com.github.dreamhead:moco-core:1.6.0"
```

## Server Creation

```java
import static com.github.dreamhead.moco.Moco.*;

// HTTP server
HttpServer server = httpServer(12306);         // fixed port
HttpServer server = httpServer();              // auto port, get via server.port()

// HTTPS server
HttpsCertificate cert = certificate(pathResource("cert.jks"), "mocohttps", "mocohttps");
HttpsServer server = httpsServer(cert, 12306);

// Socket server
SocketServer server = socketServer(12306);

// REST server
RestServer server = restServer(12306);

// JSON config server
HttpServer server = jsonHttpServer(12306, file("foo.json"));
```

## Running

```java
import static com.github.dreamhead.moco.Runner.*;

// Auto start/stop
running(server, () -> { /* test code */ });

// Manual control
Runner runner = runner(server);
runner.start();
// ...
runner.stop();
```

## Imports

```java
import static com.github.dreamhead.moco.Moco.*;           // core API
import static com.github.dreamhead.moco.Runner.*;          // runner API
import static com.github.dreamhead.moco.MocoSse.*;         // SSE API
```

## Request Matching

```java
// Basic matchers
by("text content")                        // match text
by(uri("/path"))                          // match URI
eq(header("X-Foo"), "bar")               // match header
eq(query("param"), "value")              // match query param
eq(cookie("name"), "value")              // match cookie
eq(form("field"), "value")               // match form field

// Method shortcuts
server.get(by(uri("/foo")))               // GET
server.post(by("body"))                   // POST
server.put(by("body"))                    // PUT
server.delete(by(uri("/foo")))            // DELETE
server.request(by(method("HEAD")))        // other methods

// Content types
json(text("{\"foo\":\"bar\"}"))           // match JSON text
json(pojo)                                // match JSON via POJO
json(file("body.json"))                   // match JSON from file
xml(text("<root><id>1</id></root>"))     // match XML
eq(xpath("/root/id/text()"), "1")        // match XPath
eq(jsonPath("$.foo.bar"), "value")       // match JSONPath

// Struct matching (structure only, ignores values)
struct(json("{\"foo\":1}"))
struct(xml("<foo>1</foo>"))

// Binary
binary(new byte[] {1, 2, 3})

// Compose matchers
and(by(uri("/foo")), eq(header("X"), "y"))
```

## Response Handlers

```java
// Basic
text("hello")                             // text response
file("response.txt")                      // file response
status(200)                               // status code
header("Content-Type", "application/json")// header
cookie("loggedIn", "true")               // cookie

// JSON response (sets Content-Type automatically)
json(pojo)
json((request) -> new MyPojo())           // dynamic JSON

// Binary
binary(new byte[] {1, 2, 3})

// Transform
text("hello").transform(raw -> /* transform bytes */)

// Dynamic text
text((request) -> "dynamic content")

// Redirect
redirectTo("http://example.com")

// Proxy
proxy("http://target-server")
proxy("http://target", failover("backup.json"))
proxy("http://target", playback("cache.json"))
proxy(from("/api").to("http://target"))

// Cookie with attributes
cookie("name", "value", path("/"), domain("example.com"), secure(), httpOnly(), maxAge(1, TimeUnit.HOURS), sameSite("Lax"))

// CORS
cors()                                                    // allow all
cors(allowOrigin("*"), allowMethods("GET", "POST"))      // customized

// Attachment
attachment("file.txt", file("content.txt"))

// Latency
latency(1, TimeUnit.SECONDS)

// Sequence (returns each response once, in order)
seq("first", "second", "third")
seq(status(302), status(302), status(200))

// Cycle (repeats the sequence)
cycle("first", "second", "third")

// Mount directory
server.mount(dir, to("/uri"))
server.mount(dir, to("/uri"), include("*.txt"), exclude("*.log"))

// Compose responses
with(text("body"), header("Content-Type", "text/html"), status(200))
```

## Template

```java
template("${req.content}")
template("${req.headers['X-Foo']}")
template("${req.queries['param']}")
template("${req.forms['field']}")
template("${req.cookies['name']}")
template("${req.json.foo}")
template("${req.xml.bar}")
template("${req.path.id}")                // with path() matcher
template("${req.client.address}")
template("${req.client.port}")
template("${req.method}")
template("${req.version}")

// Custom variables
template("${greeting}", "greeting", "hello")

// With extractors
template("${price}", "price", jsonPath("$.book[0].price"))

// Template functions
template("${now('yyyy-MM-dd')}")
template("${random()}")
template("${random(100)}")
template("${random(10, 20)}")
template("${random('###.###')}")

// Template in file name
file(template("${req.headers['filename']}.txt"))

// Template in redirect
redirectTo(template("${var}", "var", "http://target"))

// Template in proxy
proxy(template("http://target/${req.queries['id']}"))
```

## Events

```java
import static com.github.dreamhead.moco.Moco.*;

// GET on complete
server.request(by(uri("/event")))
      .response("ok")
      .on(complete(get("http://callback-url")));

// POST on complete
server.request(by(uri("/event")))
      .response("ok")
      .on(complete(post("http://callback-url", "content")));

// With headers
.on(complete(post("http://url", "content", header("X-Foo", "bar"))))

// Async event
.on(complete(async(post("http://url", "content"))))

// Async with latency
.on(complete(async(post("http://url", "content"), latency(1000))))

// Template in event
.on(complete(post("http://url", template("${target}", "target", var("value")))))
```

## Verification

```java
RequestHit hit = requestHit();
HttpServer server = httpServer(12306, hit);

// Verify request count
hit.verify(by(uri("/foo")), times(2));
hit.verify(by(uri("/foo")), once());
hit.verify(by(uri("/foo")), never());
hit.verify(by(uri("/foo")), atLeast(1));
hit.verify(by(uri("/foo")), atMost(3));
hit.verify(by(uri("/foo")), between(1, 3));

// Verify no unexpected requests
hit.verify(unexpected(), never());
```

## Logging

```java
HttpServer server = httpServer(log());                          // console log
HttpServer server = httpServer(log("path/to/log.log"));         // file log
HttpServer server = httpServer(log("log.log", Charset.forName("GBK"))); // with charset

// Combined with verification
HttpServer server = httpServer(12306, hit, log());
```
