# LLM Security Gateway

**An independent security layer for AI agents.**

Designed by **Ethern Myth**.

LLM Security Gateway is a model-agnostic security gateway that exposes content inspection, threat detection, and redaction capabilities to AI agents through the Model Context Protocol (MCP).

It provides a security boundary between AI agents and the content they process — helping identify prompt injection attempts, sensitive information, exposed credentials, and potentially unsafe content before it reaches a model or downstream system.

**Your agents. Your models. Your infrastructure. One security boundary.**

## Why LLM Security Gateway?

AI agents increasingly interact with users, models, tools, APIs, and external systems. Every interaction introduces potential security risks.

LLM Security Gateway provides security tools that agents can explicitly invoke to help protect these interactions.

It is designed to help address:

- Prompt injection and jailbreak attempts.
- Personally identifiable information (PII) exposure.
- Accidental disclosure of credentials, API keys, and secrets.
- Sensitive information appearing in AI-generated responses.
- Unsafe content being forwarded to downstream systems.

## Core capabilities

| Capability | Description |
|---|---|
| Input Security | Inspect incoming content before it reaches an AI model. |
| Output Security | Inspect AI-generated content before it is released. |
| Prompt Injection Detection | Identify known prompt injection and jailbreak patterns. |
| PII Detection | Detect supported personal information patterns. |
| Secret Detection | Identify supported credentials and secret patterns. |
| Content Redaction | Replace detected sensitive content with redaction markers. |
| Security Decisions | Return Allow, Redact, or Block decisions. |
| Security Auditing | Record security events and inspection outcomes. |

## How it works

The gateway operates independently of the AI model or provider.

An agent or consuming application invokes the gateway's MCP tools at the appropriate points in its workflow.

```text
                  AI AGENT
                     |
                     |
              Incoming Content
                     |
                     v
          +----------------------+
          |   INPUT SECURITY     |
          |       GATE           |
          +----------------------+
                     |
              Allow / Redact /
                  Block
                     |
                     v
                AI MODEL
                     |
              Generated Output
                     |
                     v
          +----------------------+
          |   OUTPUT SECURITY   |
          |       GATE           |
          +----------------------+
                     |
              Allow / Redact /
                  Block
                     |
                     v
              Approved Content
```

The consuming agent or application is responsible for enforcing the security decision. MCP does not automatically intercept every model interaction or tool call.

## Available MCP tools

| Tool | Purpose |
|---|---|
| `scan_input` | Inspect incoming content and return a security decision. |
| `scan_output` | Inspect generated content and return a security decision. |
| `redact_input` | Inspect and return sanitized incoming content when permitted. |
| `redact_output` | Inspect and return sanitized generated content when permitted. |
| `get_security_audit` | Retrieve recent security audit events. |
| `get_gateway_status` | Retrieve gateway operational information and capabilities. |

## Security decisions

The gateway returns one of three security decisions:

- **Allow** — Content passed the configured security checks.
- **Redact** — Sensitive content was detected and sanitized content is available.
- **Block** — Content should not proceed.

Consuming applications should enforce these decisions and fail closed when the gateway cannot provide a valid security response.

## Architecture

```text
+-----------------------------+
|       AI AGENTS             |
|                             |
|  Agent Applications         |
|  MCP Clients                |
+--------------+--------------+
               |
               | Model Context Protocol
               | STDIO Transport
               |
+--------------v--------------+
|     LLM SECURITY GATEWAY     |
|                              |
|  Input Security Tools        |
|  Output Security Tools       |
|  Input Redaction Tools       |
|  Output Redaction Tools      |
|  Audit Tools                 |
|  Gateway Status              |
|                              |
+--------------+---------------+
               |
+--------------v---------------+
|     SECURITY CORE            |
|                              |
|  Content Inspection          |
|  Risk Assessment             |
|  PII and Secret Detection    |
|  Redaction                   |
|  Audit Recording             |
+------------------------------+
```

## Model agnostic by design

LLM Security Gateway does not host, provision, or require an AI model.

It is designed to operate alongside agent applications using different model providers and runtimes, including:

- Ollama
- OpenAI
- Anthropic
- Azure OpenAI
- Other compatible model providers

The gateway does not require provider-specific API keys or model configuration. Agents connect to its MCP server and invoke the available security tools.

## Audit and visibility

Security events capture information such as:

- Event identifier and timestamp.
- Security inspection stage.
- Security decision.
- Risk score.
- Detection reasons.
- Number of detected PII matches.

The current audit store is in-memory. Events are not retained after the server process terminates.

## MCP client configuration

The gateway can be configured as an MCP server in a compatible MCP client.

### Run through Docker

```json
{
  "mcpServers": {
    "llm-security-gateway": {
      "command": "docker",
      "args": [
        "run",
        "-i",
        "--rm",
        "ethernmyth/llm-security-gateway:alpha"
      ]
    }
  }
}
```

The `-i` option keeps standard input open for MCP STDIO communication. The container runs as an MCP server process, with standard output reserved for protocol messages.

The image must be available locally or published to the configured container registry before using this configuration.

## Security considerations

LLM Security Gateway is a security layer for agent workflows, not a transparent network proxy.

- Agents and consuming applications must explicitly invoke the security tools.
- Applications must enforce returned security decisions.
- Security failures and unexpected responses should be handled using a fail-closed approach.
- MCP server access should be restricted to trusted clients.
- Sensitive content should not be unnecessarily written to application logs.
- Detection is based on implemented patterns and inspection logic; it is not a guarantee that every attack or sensitive value will be identified.

## Release

**Alpha**

The initial Alpha release establishes the MCP server, security tools, content inspection, redaction, audit recording, and agent-side security enforcement workflow.

## About Ethern Myth

Ethern Myth builds software focused on security, infrastructure, and the systems that keep modern operations moving.

**Nothing ever stops. Operations should not either.**

---

**LLM Security Gateway — Designed by [Ethern-Myth](https://github.com/ethern-myth).**