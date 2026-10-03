# Threats

A working taxonomy of attacks against LLM and agentic systems.

## Prompt injection

Untrusted input overrides the developer's instructions.

- **Direct:** the user types the injected instruction.
- **Indirect:** the instruction rides in content the model reads later - a web
  page, a file, an API response, a tool result. The user never sees it.

Indirect injection is the one that matters most for agents, because an agent
reads tool output and acts on it in the same loop.

## Tool poisoning

The attack lives in a tool's definition, not its input.

- **Poisoned descriptions:** a tool's description carries hidden instructions the
  user never sees but the model reads.
- **Rug pulls:** a tool looks benign at approval time, then its definition changes
  after it is trusted.
- **Tool shadowing:** a malicious server changes how a trusted server's tools
  behave.

## Jailbreaking

Getting the model past its own policy or safety training. Assume it can be done;
design so a jailbreak does not also grant real-world access.

## Supply chain

- Models, MCP servers, skills, and plugins are dependencies.
- Risks: poisoned training data, backdoored models, typosquatted or unvetted
  servers, dependency abuse in the serving stack.

## Infrastructure and deployment

- Exposed inference endpoints (local model servers on default ports).
- Vector database access and poisoning.
- Credentials and keys in agent config or environment.
- Over-broad tool permissions and scopes.
