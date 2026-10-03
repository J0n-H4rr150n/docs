# Model Context Protocol (MCP)

MCP is the open protocol that connects LLM applications to external tools and
data. It is worth understanding in detail because the tools, skills, and servers
it exposes are the thing a scanner has to inspect.

## What a server exposes

- **Tools:** functions the model can call.
- **Resources:** data and context for the model or user.
- **Prompts:** templated workflows.
- **Transports:** stdio (local) and HTTP.

## Attack surface

- **Tool definitions are prompts the user never sees.** Read them the way an
  attacker would: hidden instructions, suspicious URLs, scope creep.
- **Rug pulls:** a server's tool definitions changing after approval. Version-pin
  and diff on each new release.
- **Authorization:** MCP uses OAuth 2.1 patterns (PKCE, resource indicators,
  audience-bound tokens). Audience binding is what stops token passthrough.
- **SSRF via discovery:** a malicious server can point OAuth discovery URLs at
  internal or cloud-metadata endpoints.
- **Local server compromise:** a malicious start command or an insecure server
  left listening on localhost.

## Discovery (shadow MCP)

Find MCP servers and agents running outside governance by their config files and
behavior:

- Config locations per client (CLI agents, IDE agents, desktop agents).
- Local inference endpoints and model-server ports.
- Behavior: a process that loads model weights, starts a runtime, and runs a
  tool loop.
