# Moco Skill

Moco Skill provides Moco HTTP stub server expertise. With this skill, Claude can help you write test mocks, configure JSON stubs, and use Java/REST/WebSocket/SSE/Socket APIs with accurate, up-to-date guidance.

## Version

This skill is based on Moco **1.6.0**. The version number is aligned with the [Moco](https://github.com/dreamhead/moco) release.

## What is Moco

[Moco](https://github.com/dreamhead/moco) is a lightweight HTTP stub framework for testing and integration. It supports mock HTTP/HTTPS, WebSocket, SSE, and Socket servers with simple configuration.

## Installation

Clone this repository into your Claude Code skills directory:

```shell
git clone https://github.com/dreamhead/moco-skill ~/.claude/skills/moco
```

Or add it to your project's `.claude/skills/` directory for project-local use:

```shell
git clone https://github.com/dreamhead/moco-skill .claude/skills/moco
```

To update to the latest version:

```shell
cd ~/.claude/skills/moco && git pull
```

## Usage

Once installed, Claude will automatically invoke the Moco skill when you ask about:

- Writing test mocks with Moco Java API

- Configuring JSON stub files

- Setting up REST/WebSocket/SSE/Socket mock servers

- Running standalone Moco servers

- JUnit integration with Moco


### Example Prompts

```
"Help me write a Moco stub that returns a JSON response for POST /api/users"
"How do I set up a WebSocket mock server with Moco?"
"Create a Moco JSON config file for these API endpoints..."
"Write a JUnit 5 test using Moco annotations"
"How do I verify request hit count with Moco?"
```

## Skill Structure

```
moco-skill/
├── SKILL.md                  # Skill definition and quick reference
└── references/               # Detailed API references
    ├── java-api.md           # Java API usage
    ├── json-config.md        # JSON configuration format
    ├── rest-api.md           # RESTful stub services
    ├── websocket-api.md      # WebSocket mock servers
    ├── sse-api.md            # SSE stream mocking
    ├── socket-api.md         # Raw TCP socket stubs
    ├── cli.md                # Standalone server CLI
    └── junit.md              # JUnit 5 integration
```

## Quick Reference

**Java API:**

```java
HttpServer server = httpServer(12306);
server.request(by(uri("/foo"))).response("bar");

running(server, () -> {
    // test against http://localhost:12306/foo
});
```

**JSON Config + CLI:**

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

## Supported Environments

- Claude Code CLI
- Claude Code IDE extensions (VS Code, JetBrains)
- Any environment that supports Claude Code skills

## Copyright and license

Copyright 2026 ZHENG Ye

Licensed under MIT License (the "License"); You may obtain a copy of the License in the LICENSE file, or at:

https://raw.github.com/dreamhead/moco-skill/master/MIT-LICENSE.txt
