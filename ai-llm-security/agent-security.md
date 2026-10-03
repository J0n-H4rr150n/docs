# Agent Security

Agentic systems add risk that a plain chat model does not have: they call tools,
read untrusted results, and act in a loop.

## Trust boundaries

Map every point where data crosses from untrusted to trusted:

- User input -> agent
- Tool/API/web result -> agent context
- Agent decision -> privileged action (write, exec, network, spend)
- One agent -> another agent (handoffs)

The dangerous edge is the third one. Keep model output out of the path that
authorizes a privileged action.

## Tool-use risk

An agent's power is its tools. Each tool is attack surface:

- File and shell access beyond what the task needs.
- API and database access that is not scoped to the caller.
- Network egress that enables exfiltration or SSRF.

## Containment patterns

- **Least privilege per tool.** Scope and permissions enforced by the systems the
  agent calls, not by the prompt.
- **Human-in-the-loop for privileged actions.** The agent proposes; a person
  approves anything that writes or spends.
- **Budgets and quotas.** Hard caps on tool runs and iterations, so a loop cannot
  run away.
- **Verification.** Treat an agent's "done" or "fixed" as a claim to check
  independently, not a fact.
- **Observability.** Log every thought, action, and tool call so behavior is
  explainable after the fact.
