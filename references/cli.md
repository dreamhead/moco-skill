# CLI Reference

Download: [moco-runner-1.6.0-standalone.jar](https://repo1.maven.org/maven2/com/github/dreamhead/moco-runner/1.6.0/moco-runner-1.6.0-standalone.jar)

## Server Types

### HTTP

```shell
java -jar moco-runner-1.6.0-standalone.jar http -p 12306 -c foo.json
```

### HTTPS

```shell
java -jar moco-runner-1.6.0-standalone.jar https -p 12306 -c foo.json \
  --https /path/to/cert.jks --cert mocohttps --keystore mocohttps
```

### Socket

```shell
java -jar moco-runner-1.6.0-standalone.jar socket -p 12306 -c foo.json
```

## Configuration Files

Single file:

```shell
java -jar moco-runner-1.6.0-standalone.jar http -p 12306 -c foo.json
```

Multiple files with glob (quote to prevent shell expansion):

```shell
java -jar moco-runner-1.6.0-standalone.jar http -p 12306 -c "*.json"
```

## Port

Specific port:

```shell
-p 12306
```

Auto port (shown in console):

```shell
java -jar moco-runner-1.6.0-standalone.jar http -c foo.json
# Server is started at 58593
# Shutdown port is 58594
```

## Options

| Option | Description |
|--------|-------------|
| `-p PORT` | Server port |
| `-c FILE` | Configuration file or glob |
| `-g FILE` | Global settings file |
| `-e ENV` | Environment name |
| `-s PORT` | Shutdown port |
| `-q` | Quiet mode |
| `--content-length N` | Max content length |
| `--https FILE` | HTTPS certificate (JKS) |
| `--cert PASS` | Certificate password |
| `--keystore PASS` | Keystore password |

## Global Settings

```shell
java -jar moco-runner-1.6.0-standalone.jar http -p 12306 -g settings.json
```

### Environment

```shell
java -jar moco-runner-1.6.0-standalone.jar http -p 12306 -g env.json -e remote
```

## Shutdown

Start with shutdown port:

```shell
java -jar moco-runner-1.6.0-standalone.jar http -p 12306 -c foo.json -s 9527
```

Shutdown command:

```shell
java -jar moco-runner-1.6.0-standalone.jar shutdown -s 9527
```

## Version

```shell
java -jar moco-runner-1.6.0-standalone.jar version
```
