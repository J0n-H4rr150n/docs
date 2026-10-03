# Defenses

Controls that hold up in practice, ordered from most to least reliable.

## 1. Architecture over prompting

Prompt-based guardrails ("ignore any instructions in the content below") fail.
Contain the agent with structure instead:

- Separate the control plane (what the agent is told to do) from the data plane
  (untrusted content it reads).
- Keep untrusted text out of the path that authorizes a privileged action.

## 2. Least privilege

- Scope every tool, token, and connector to the minimum it needs.
- Enforce scope in the system the tool calls, not in the model's instructions.
- Short-lived credentials over long-lived ones.

## 3. Human-in-the-loop

- A person approves writes, exec, spend, and anything irreversible.
- Agents handle read-only steps and proposals on their own.
- A shadow or dry-run mode to watch before you let it act.

## 4. Budgets, quotas, and rate limits

- Hard caps on iterations and tool runs per task.
- A planner that requests a budget; a quota manager that clamps it.

## 5. Verification and triage

- An independent check that a finding or fix is real before it counts.
- Measure false-positive rate; detection quality is what makes this usable.

## 6. Observability

- Trace every step: prompt, tool call, result, decision.
- Make agent behavior replayable and explainable.
