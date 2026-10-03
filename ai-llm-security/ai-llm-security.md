# AI & LLM Security

Notes on securing AI and LLM-backed systems, with a focus on agentic systems:
tools, MCP servers, and autonomous workflows. This section covers the threats I
run into building and testing these systems, and the controls that actually
contain them.

The through-line: an LLM agent treats its input as instructions, and most of that
input is untrusted. The job is to keep untrusted text from reaching anything that
authorizes a privileged action.

## Pages

- [Threats](threats.md) - the attack taxonomy
- [Agent Security](agent-security.md) - trust boundaries and tool-use risk
- [Defenses](defenses.md) - controls and containment
- [Model Context Protocol (MCP)](mcp.md) - the protocol and its attack surface
- [Resources](resources.md) - frameworks, references, and my own projects

## Frameworks I map to

- OWASP Top 10 for LLM Applications
- MITRE ATLAS (adversarial threats to AI systems)
