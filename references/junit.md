# JUnit 5 Integration Reference

## Setup

Add dependency:

```xml
<dependency>
  <groupId>com.github.dreamhead</groupId>
  <artifactId>moco-junit5</artifactId>
  <version>1.6.0</version>
  <scope>test</scope>
</dependency>
```

## @MocoExtension

Required for all Moco JUnit 5 tests. Server starts before each test and stops after.

```java
import org.junit.jupiter.api.extension.ExtendWith;
import com.github.dreamhead.moco.junit5.MocoExtension;
import com.github.dreamhead.moco.junit5.MocoHttpServer;

@ExtendWith(MocoExtension.class)
class MyTest {
    // ...
}
```

## HTTP Server

Use `filepath` to specify config file path:

```java
@ExtendWith(MocoExtension.class)
@MocoHttpServer(filepath = "foo/foo.json", port = 12306)
class MocoHttpTest {
    @Test
    void shouldReturnExpectedMessage() throws IOException {
        Content content = Request.Get("http://localhost:12306").execute().returnContent();
        assertThat(content.asString(), is("foo"));
    }
}
```

Use `classpath` to load from classpath:

```java
@ExtendWith(MocoExtension.class)
@MocoHttpServer(classpath = "foo.json", port = 12306)
class MocoHttpTest {
    // ...
}
```

## HTTPS Server

Add `@MocoCertificate` for HTTPS:

```java
@ExtendWith(MocoExtension.class)
@MocoHttpServer(filepath = "foo/foo.json", port = 12306)
@MocoCertificate(
    filepath = "certificate/cert.jks",
    keyStorePassword = "mocohttps",
    certPassword = "mocohttps"
)
class MocoHttpsTest {
    // ...
}
```

Certificate from classpath:

```java
@MocoCertificate(
    classpath = "certificate/cert.jks",
    keyStorePassword = "mocohttps",
    certPassword = "mocohttps"
)
```

## Annotations

| Annotation | Attributes | Description |
|-----------|------------|-------------|
| `@MocoExtension` | - | Enables Moco lifecycle management |
| `@MocoHttpServer` | `filepath`, `classpath`, `port` | Starts HTTP server with config |
| `@MocoCertificate` | `filepath`, `classpath`, `keyStorePassword`, `certPassword` | HTTPS certificate config |
